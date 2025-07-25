---
id: integrate_with_ragas.md
summary: This guide demonstrates how to use Ragas to evaluate a Retrieval-Augmented Generation (RAG) pipeline built upon Milvus.
title: Evaluation with Ragas
---

<a href="https://colab.research.google.com/github/milvus-io/bootcamp/blob/master/integration/evaluation_with_ragas.ipynb" target="_parent">
    <img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab"/>
</a>
<a href="https://github.com/milvus-io/bootcamp/blob/master/integration/evaluation_with_ragas.ipynb" target="_blank">
    <img src="https://img.shields.io/badge/View%20on%20GitHub-555555?style=flat&logo=github&logoColor=white" alt="GitHub Repository"/>
</a>

# Evaluation with Ragas

This guide demonstrates how to use Ragas to evaluate a Retrieval-Augmented Generation (RAG) pipeline built upon [Milvus](https://milvus.io/).

The RAG system combines a retrieval system with a generative model to generate new text based on a given prompt. The system first retrieves relevant documents from a corpus using Milvus, and then uses a generative model to generate new text based on the retrieved documents.

[Ragas](https://docs.ragas.io/en/latest/index.html#) is a framework that helps you evaluate your RAG pipelines. There are existing tools and frameworks that help you build these pipelines but evaluating it and quantifying your pipeline performance can be hard. This is where Ragas (RAG Assessment) comes in.




## Prerequisites

Before running this notebook, make sure you have the following dependencies installed:


```shell
$ pip install --upgrade pymilvus openai requests tqdm pandas ragas
```

<div class="alert note">

If you are using Google Colab, to enable dependencies just installed, you may need to **restart the runtime** (click on the "Runtime" menu at the top of the screen, and select "Restart session" from the dropdown menu).

We will use OpenAI as the LLM in this example. You should prepare the [api key](https://platform.openai.com/docs/quickstart) `OPENAI_API_KEY` as an environment variable.

</div>


```python
import os

os.environ["OPENAI_API_KEY"] = "sk-***********"
```

## Define the RAG pipeline

We will define the RAG class that use Milvus as the vector store, and OpenAI as the LLM.
The class contains the `load` method, which loads the text data into Milvus, the `retrieve` method, which retrieves the most similar text data to the given question, and the `answer` method, which answers the given question with the retrieved knowledge.


```python
from typing import List
from tqdm import tqdm
from openai import OpenAI
from pymilvus import MilvusClient


class RAG:
    """
    RAG (Retrieval-Augmented Generation) class built upon OpenAI and Milvus.
    """

    def __init__(self, openai_client: OpenAI, milvus_client: MilvusClient):
        self._prepare_openai(openai_client)
        self._prepare_milvus(milvus_client)

    def _emb_text(self, text: str) -> List[float]:
        return (
            self.openai_client.embeddings.create(input=text, model=self.embedding_model)
            .data[0]
            .embedding
        )

    def _prepare_openai(
        self,
        openai_client: OpenAI,
        embedding_model: str = "text-embedding-3-small",
        llm_model: str = "gpt-3.5-turbo",
    ):
        self.openai_client = openai_client
        self.embedding_model = embedding_model
        self.llm_model = llm_model
        self.SYSTEM_PROMPT = """
Human: You are an AI assistant. You are able to find answers to the questions from the contextual passage snippets provided.
"""
        self.USER_PROMPT = """
Use the following pieces of information enclosed in <context> tags to provide an answer to the question enclosed in <question> tags.
<context>
{context}
</context>
<question>
{question}
</question>
"""

    def _prepare_milvus(
        self, milvus_client: MilvusClient, collection_name: str = "rag_collection"
    ):
        self.milvus_client = milvus_client
        self.collection_name = collection_name
        if self.milvus_client.has_collection(self.collection_name):
            self.milvus_client.drop_collection(self.collection_name)
        embedding_dim = len(self._emb_text("foo"))
        self.milvus_client.create_collection(
            collection_name=self.collection_name,
            dimension=embedding_dim,
            metric_type="IP",  # Inner product distance
            consistency_level="Bounded",  # Strong consistency level
        )

    def load(self, texts: List[str]):
        """
        Load the text data into Milvus.
        """
        data = []
        for i, line in enumerate(tqdm(texts, desc="Creating embeddings")):
            data.append({"id": i, "vector": self._emb_text(line), "text": line})

        self.milvus_client.insert(collection_name=self.collection_name, data=data)

    def retrieve(self, question: str, top_k: int = 3) -> List[str]:
        """
        Retrieve the most similar text data to the given question.
        """
        search_res = self.milvus_client.search(
            collection_name=self.collection_name,
            data=[self._emb_text(question)],
            limit=top_k,
            search_params={"metric_type": "IP", "params": {}},  # Inner product distance
            output_fields=["text"],  # Return the text field
        )
        retrieved_texts = [res["entity"]["text"] for res in search_res[0]]
        return retrieved_texts[:top_k]

    def answer(
        self,
        question: str,
        retrieval_top_k: int = 3,
        return_retrieved_text: bool = False,
    ):
        """
        Answer the given question with the retrieved knowledge.
        """
        retrieved_texts = self.retrieve(question, top_k=retrieval_top_k)
        user_prompt = self.USER_PROMPT.format(
            context="\n".join(retrieved_texts), question=question
        )
        response = self.openai_client.chat.completions.create(
            model=self.llm_model,
            messages=[
                {"role": "system", "content": self.SYSTEM_PROMPT},
                {"role": "user", "content": user_prompt},
            ],
        )
        if not return_retrieved_text:
            return response.choices[0].message.content
        else:
            return response.choices[0].message.content, retrieved_texts
```

Let's initialize the RAG class with OpenAI and Milvus clients.


```python
openai_client = OpenAI()
milvus_client = MilvusClient(uri="./milvus_demo.db")

my_rag = RAG(openai_client=openai_client, milvus_client=milvus_client)
```

<div class="alert note">

As for the argument of `MilvusClient`:
- Setting the `uri` as a local file, e.g.`./milvus.db`, is the most convenient method, as it automatically utilizes [Milvus Lite](https://milvus.io/docs/milvus_lite.md) to store all data in this file.
- If you have large scale of data, you can set up a more performant Milvus server on [docker or kubernetes](https://milvus.io/docs/quickstart.md). In this setup, please use the server uri, e.g.`http://localhost:19530`, as your `uri`.
- If you want to use [Zilliz Cloud](https://zilliz.com/cloud), the fully managed cloud service for Milvus, adjust the `uri` and `token`, which correspond to the [Public Endpoint and Api key](https://docs.zilliz.com/docs/on-zilliz-cloud-console#free-cluster-details) in Zilliz Cloud.

</div>

## Run the RAG pipeline and get results

We use the [Milvus development guide](https://github.com/milvus-io/milvus/blob/master/DEVELOPMENT.md) to be as the private knowledge in our RAG, which is a good data source for a simple RAG pipeline.

Download it and load it into the rag pipeline.


```python
import os
import urllib.request

url = "https://raw.githubusercontent.com/milvus-io/milvus/master/DEVELOPMENT.md"
file_path = "./Milvus_DEVELOPMENT.md"

if not os.path.exists(file_path):
    urllib.request.urlretrieve(url, file_path)
with open(file_path, "r") as file:
    file_text = file.read()

# We simply use "# " to separate the content in the file, which can roughly separate the content of each main part of the markdown file.
text_lines = file_text.split("# ")
my_rag.load(text_lines)  # Load the text data into RAG pipeline
```

    Creating embeddings: 100%|██████████| 27/27 [00:20<00:00,  1.34it/s]


Let's define a query question about the content of the development guide documentation. And then use the `answer` method to get the answer and the retrieved context texts.


```python
question = "what is the hardware requirements specification if I want to build Milvus and run from source code?"
my_rag.answer(question, return_retrieved_text=True)
```




    ('The hardware requirements specification for building Milvus and running it from source code is as follows:\n\n- 8GB of RAM\n- 50GB of free disk space',
     ['Hardware Requirements\n\nThe following specification (either physical or virtual machine resources) is recommended for Milvus to build and run from source code.\n\n```yaml\n- 8GB of RAM\n- 50GB of free disk space\n```\n\n##',
      'Building Milvus on a local OS/shell environment\n\nThe details below outline the hardware and software requirements for building on Linux and MacOS.\n\n##',
      "Software Requirements\n\nAll Linux distributions are available for Milvus development. However a majority of our contributor worked with Ubuntu or CentOS systems, with a small portion of Mac (both x86_64 and Apple Silicon) contributors. If you would like Milvus to build and run on other distributions, you are more than welcome to file an issue and contribute!\n\nHere's a list of verified OS types where Milvus can successfully build and run:\n\n- Debian/Ubuntu\n- Amazon Linux\n- MacOS (x86_64)\n- MacOS (Apple Silicon)\n\n##"])



Now let's prepare some questions with its corresponding ground truth answers. We get answers and contexts from our RAG pipeline.


```python
from ragas import EvaluationDataset
from datasets import Dataset
import pandas as pd

user_input_list = [
    "what is the hardware requirements specification if I want to build Milvus and run from source code?",
    "What is the programming language used to write Knowhere?",
    "What should be ensured before running code coverage?",
]
reference_list = [
    "If you want to build Milvus and run from source code, the recommended hardware requirements specification is:\n\n- 8GB of RAM\n- 50GB of free disk space.",
    "The programming language used to write Knowhere is C++.",
    "Before running code coverage, you should make sure that your code changes are covered by unit tests.",
]
retrieved_contexts_list = []
response_list = []

for user_input in tqdm(user_input_list, desc="Answering questions"):
    response, retrieved_context = my_rag.answer(user_input, return_retrieved_text=True)
    retrieved_contexts_list.append(retrieved_context)
    response_list.append(response)

df = pd.DataFrame(
    {
        "user_input": user_input_list,
        "retrieved_contexts": retrieved_contexts_list,
        "response": response_list,
        "reference": reference_list,
    }
)
rag_results = EvaluationDataset.from_pandas(df)
df
```

    Answering questions: 100%|██████████| 3/3 [00:04<00:00,  1.37s/it]





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user_input</th>
      <th>retrieved_contexts</th>
      <th>response</th>
      <th>reference</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>what is the hardware requirements specificatio...</td>
      <td>[Hardware Requirements\n\nThe following specif...</td>
      <td>The hardware requirements specification to bui...</td>
      <td>If you want to build Milvus and run from sourc...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>What is the programming language used to write...</td>
      <td>[CMake &amp; Conan\n\nThe algorithm library of Mil...</td>
      <td>The programming language used to write Knowher...</td>
      <td>The programming language used to write Knowher...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>What should be ensured before running code cov...</td>
      <td>[Code coverage\n\nBefore submitting your pull ...</td>
      <td>Before running code coverage, it should be ens...</td>
      <td>Before running code coverage, you should make ...</td>
    </tr>
  </tbody>
</table>
</div>



## Evaluation with Ragas

We use Ragas to evaluate the performance of our RAG pipeline results.

Ragas provides a set of metrics that is easy to use. We take `Answer relevancy`, `Faithfulness`, `Context recall`, and `Context precision` as the metrics to evaluate our RAG pipeline. For more information about the metrics, please refer to the [Ragas Metrics](https://docs.ragas.io/en/latest/concepts/metrics/index.html).


```python
from ragas import evaluate
from ragas.metrics import AnswerRelevancy, Faithfulness, ContextRecall, ContextPrecision

from ragas.llms import LangchainLLMWrapper
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")
evaluator_llm = LangchainLLMWrapper(llm)

results = evaluate(
    dataset=rag_results,
    metrics=[
        AnswerRelevancy(llm=evaluator_llm),
        Faithfulness(llm=evaluator_llm),
        ContextRecall(llm=evaluator_llm),
        ContextPrecision(llm=evaluator_llm),
    ],
)
results
```

    Evaluating: 100%|██████████| 12/12 [00:10<00:00,  1.11it/s]





    {'answer_relevancy': 0.9894, 'faithfulness': 1.0000, 'context_recall': 1.0000, 'context_precision': 1.0000}


