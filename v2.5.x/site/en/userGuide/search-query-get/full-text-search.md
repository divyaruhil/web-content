---
id: full-text-search.md
title: Full Text Search
related_key: full, text, search
summary: Full text search is a feature that retrieves documents containing specific terms or phrases in text datasets, then ranking the results based on relevance.
---

# Full Text Search​

Full text search is a feature that retrieves documents containing specific terms or phrases in text datasets, then ranks the results based on relevance. This feature overcomes the limitations of semantic search, which might overlook precise terms, ensuring you receive the most accurate and contextually relevant results. Additionally, it simplifies vector searches by accepting raw text input, automatically converting your text data into sparse embeddings without the need to manually generate vector embeddings.​

Using the BM25 algorithm for relevance scoring, this feature is particularly valuable in retrieval-augmented generation (RAG) scenarios, where it prioritizes documents that closely match specific search terms.​

<div class="alert note">

- By integrating full text search with semantic-based dense vector search, you can enhance the accuracy and relevance of search results. For more information, refer to [​Hybrid Search](multi-vector-search.md).​
- Full text search is available in Milvus Standalone and Milvus Distributed but not Milvus Lite, although adding it to Milvus Lite is on the roadmap.

</div>

## Overview​

Full text search simplifies the process of text-based searching by eliminating the need for manual embedding. This feature operates through the following workflow:​

1. **Text input**: You insert raw text documents or provide query text without needing to manually embed them.​

2. **Text analysis**: Milvus uses an analyzer to tokenize the input text into individual, searchable terms.​ For more information on analyzers, refer to [Analyzer Overview](analyzer-overview.md).

3. **Function processing**: The built-in function receives tokenized terms and converts them into sparse vector representations.​

4. **Collection store**: Milvus stores these sparse embeddings in a collection for efficient retrieval.​

5. **BM25 scoring**: During a search, Milvus applies the BM25 algorithm to calculate scores for the stored documents and ranks matched results based on their relevance to the query text.​

![Full text search](../../../../assets/full-text-search.png)

To use full text search, follow these main steps:​

1. [Create a collection](#Create-a-collection-for-full-text-search): Set up a collection with necessary fields and define a function to convert raw text into sparse embeddings.​

2. [Insert data](#Insert-text-data): Ingest your raw text documents to the collection.​

3. [Perform searches](#Perform-full-text-search): Use query texts to search through your collection and retrieve relevant results.​

## Create a collection for full text search​

To enable full text search, create a collection with a specific schema. This schema must include three necessary fields:​

- The primary field that uniquely identifies each entity in a collection.​

- A `VARCHAR` field that stores raw text documents, with the `enable_analyzer` attribute set to `True`. This allows Milvus to tokenize text into specific terms for function processing.​

- A `SPARSE_FLOAT_VECTOR` field reserved to store sparse embeddings that Milvus will automatically generate for the `VARCHAR` field.​

### Define the collection schema

First, create the schema and add the necessary fields:​

<div class="multipleCode">
    <a href="#python">Python </a>
    <a href="#java">Java</a>
    <a href="#javascript">Node.js</a>
    <a href="#curl">cURL</a>
</div>

```python
from pymilvus import MilvusClient, DataType, Function, FunctionType​

client = MilvusClient(uri="http://localhost:19530")
​
schema = client.create_schema()​
​
schema.add_field(field_name="id", datatype=DataType.INT64, is_primary=True, auto_id=True)​
schema.add_field(field_name="text", datatype=DataType.VARCHAR, max_length=1000, enable_analyzer=True)​
schema.add_field(field_name="sparse", datatype=DataType.SPARSE_FLOAT_VECTOR)​

```

```java
import io.milvus.v2.common.DataType;
import io.milvus.v2.service.collection.request.AddFieldReq;
import io.milvus.v2.service.collection.request.CreateCollectionReq;

CreateCollectionReq.CollectionSchema schema = CreateCollectionReq.CollectionSchema.builder()
        .build();
schema.addField(AddFieldReq.builder()
        .fieldName("id")
        .dataType(DataType.Int64)
        .isPrimaryKey(true)
        .autoID(true)
        .build());
schema.addField(AddFieldReq.builder()
        .fieldName("text")
        .dataType(DataType.VarChar)
        .maxLength(1000)
        .enableAnalyzer(true)
        .build());
schema.addField(AddFieldReq.builder()
        .fieldName("sparse")
        .dataType(DataType.SparseFloatVector)
        .build());
```

```javascript
import { MilvusClient, DataType } from "@zilliz/milvus2-sdk-node";

const address = "http://localhost:19530";
const token = "root:Milvus";
const client = new MilvusClient({address, token});
const schema = [
  {
    name: "id",
    data_type: DataType.Int64,
    is_primary_key: true,
  },
  {
    name: "text",
    data_type: "VarChar",
    enable_analyzer: true,
    enable_match: true,
    max_length: 1000,
  },
  {
    name: "sparse",
    data_type: DataType.SparseFloatVector,
  },
];


console.log(res.results)
```

```curl
export schema='{
        "autoId": true,
        "enabledDynamicField": false,
        "fields": [
            {
                "fieldName": "id",
                "dataType": "Int64",
                "isPrimary": true
            },
            {
                "fieldName": "text",
                "dataType": "VarChar",
                "elementTypeParams": {
                    "max_length": 1000,
                    "enable_analyzer": true
                }
            },
            {
                "fieldName": "sparse",
                "dataType": "SparseFloatVector"
            }
        ]
    }'
```

In this configuration,​

- `id`: serves as the primary key and is automatically generated with `auto_id=True`.​

- `text`: stores your raw text data for full text search operations. The data type must be `VARCHAR`, as `VARCHAR` is Milvus' string data type for text storage. Set `enable_analyzer=True` to allow Milvus to tokenize the text. By default, Milvus uses the [standard analyzer](standard-analyzer.md) for text analysis. To configure a different analyzer, refer to [​Overview](analyzer-overview.md).​

- `sparse`: a vector field reserved to store internally generated sparse embeddings for full text search operations. The data type must be `SPARSE_FLOAT_VECTOR`.​

Now, define a function that will convert your text into sparse vector representations and then add it to the schema:​

<div class="multipleCode">
    <a href="#python">Python </a>
    <a href="#java">Java</a>
    <a href="#javascript">Node.js</a>
    <a href="#curl">cURL</a>
</div>

```python
bm25_function = Function(​
    name="text_bm25_emb", # Function name​
    input_field_names=["text"], # Name of the VARCHAR field containing raw text data​
    output_field_names=["sparse"], # Name of the SPARSE_FLOAT_VECTOR field reserved to store generated embeddings​
    function_type=FunctionType.BM25,​
)​
​
schema.add_function(bm25_function)​

```

```java
import io.milvus.common.clientenum.FunctionType;
import io.milvus.v2.service.collection.request.CreateCollectionReq.Function;

import java.util.*;

schema.addFunction(Function.builder()
        .functionType(FunctionType.BM25)
        .name("text_bm25_emb")
        .inputFieldNames(Collections.singletonList("text"))
        .outputFieldNames(Collections.singletonList("vector"))
        .build());
```

```javascript
const functions = [
    {
      name: 'text_bm25_emb',
      description: 'bm25 function',
      type: FunctionType.BM25,
      input_field_names: ['text'],
      output_field_names: ['vector'],
      params: {},
    },
]；
```

```curl
export schema='{
        "autoId": true,
        "enabledDynamicField": false,
        "fields": [
            {
                "fieldName": "id",
                "dataType": "Int64",
                "isPrimary": true
            },
            {
                "fieldName": "text",
                "dataType": "VarChar",
                "elementTypeParams": {
                    "max_length": 1000,
                    "enable_analyzer": true
                }
            },
            {
                "fieldName": "sparse",
                "dataType": "SparseFloatVector"
            }
        ],
        "functions": [
            {
                "name": "text_bm25_emb",
                "type": "BM25",
                "inputFieldNames": ["text"],
                "outputFieldNames": ["sparse"],
                "params": {}
            }
        ]
    }'
```

<table data-block-token="EfAfdS3iXoAULPxQ3mwckzTrnUb"><thead><tr><th data-block-token="O3sLd5KNXou4Egxq6XVcoNiJnMW" colspan="1" rowspan="1"><p data-block-token="QRttdgJBpo2hEuxb438c7eOgn2f">Parameter​</p>

</th><th data-block-token="SMGGduN8zo3cgXxVnwZcW0UAnbA" colspan="1" rowspan="1"><p data-block-token="LY39dA2eOoyVUUxvKwlcyyjdn3e">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="Pbj3dPvuno3x6kxnCsWcTb3knag" colspan="1" rowspan="1"><p data-block-token="EeHOdxCjloFUAGxuY1CcScCTnDe"><code>name</code>​</p>

<p data-block-token="FzAJdVbrzozmTdxwy4fcJQkQnlh">​</p>

</td><td data-block-token="VJWydnWHJoV66jx6oEPcH9lGnvh" colspan="1" rowspan="1"><p data-block-token="Clg3dWrJpo39lfxSWjVcbE7GnYm">The name of the function. This function converts your raw text from the <code>text</code> field into searchable vectors that will be stored in the <code>sparse</code> field.​</p>

</td></tr><tr><td data-block-token="ShPJdlvMQoXnSHxIQ1GcoyegnEb" colspan="1" rowspan="1"><p data-block-token="HFT1dYVCioUj4PxnNSVcYIBInNh"><code>input_field_names</code>​</p>

</td><td data-block-token="YiZCdrUaaovWnrxef29cmpQFn9c" colspan="1" rowspan="1"><p data-block-token="YFVOd29cUovDpXx7L2zcJK37n1g">The name of the <code>VARCHAR</code> field requiring text-to-sparse-vector conversion. For <code>FunctionType.BM25</code>, this parameter accepts only one field name.​</p>

</td></tr><tr><td data-block-token="QpcMdDoXfo62aNxQfoyc2E6lneg" colspan="1" rowspan="1"><p data-block-token="D1LkdH1KIojwKDx14HUcHdDJnPh"><code>output_field_names</code>​</p>

</td><td data-block-token="TrvodS2xDoF6UhxeFNScRg86nuf" colspan="1" rowspan="1"><p data-block-token="CO6bdbNhQo9ZprxlGdecjs9RnEf">The name of the field where the internally generated sparse vectors will be stored. For <code>FunctionType.BM25</code>, this parameter accepts only one field name.​</p>

</td></tr><tr><td data-block-token="UvgkdWp5RoXa0QxL3CKcoEZVnIf" colspan="1" rowspan="1"><p data-block-token="PWZSd2E48oWB2QxqVoVcMHGxn7c"><code>function_type</code>​</p>

</td><td data-block-token="VdcmdmiiWoy0nex8a29clnslnQg" colspan="1" rowspan="1"><p data-block-token="Q2eSdvOqeoNa6dxcGjcc2LKinDg">The type of the function to use. Set the value to <code>FunctionType.BM25</code>.​</p>

</td></tr></tbody></table>

<div class="alert note">

For collections with multiple `VARCHAR` fields requiring text-to-sparse-vector conversion, add separate functions to the collection schema, ensuring each function has a unique name and `output_field_names` value.​

</div>

### Configure the index

After defining the schema with necessary fields and the built-in function, set up the index for your collection. To simplify this process, use `AUTOINDEX` as the `index_type`, an option that allows Milvus to choose and configure the most suitable index type based on the structure of your data.​

<div class="multipleCode">
    <a href="#python">Python </a>
    <a href="#java">Java</a>
    <a href="#javascript">Node.js</a>
    <a href="#curl">cURL</a>
</div>

```python
index_params = client.prepare_index_params()​
​
index_params.add_index(​
    field_name="sparse",​
    index_type="AUTOINDEX", ​
    metric_type="BM25"​
)​

```

```java
import io.milvus.v2.common.IndexParam;

List<IndexParam> indexes = new ArrayList<>();
indexes.add(IndexParam.builder()
        .fieldName("sparse")
        .indexType(IndexParam.IndexType.SPARSE_INVERTED_INDEX)
        .metricType(IndexParam.MetricType.BM25)
        .build());
```

```javascript
const index_params = [
  {
    field_name: "sparse",
    metric_type: "BM25",
    index_type: "AUTOINDEX",
  },
];
```

```curl
export indexParams='[
        {
            "fieldName": "sparse",
            "metricType": "BM25",
            "indexType": "AUTOINDEX"
        }
    ]'
```

<table data-block-token="XEoodLxOFoukWJx9aLXcH46snXc"><thead><tr><th data-block-token="PfGNdbuq9o9PEWxzAWecWWoInUf" colspan="1" rowspan="1"><p data-block-token="KX1VdsOJCoO0Exxhg8acsduwncd">Parameter​</p>

</th><th data-block-token="VNwBdAyWKoPktSxYaBtcn5rKnNb" colspan="1" rowspan="1"><p data-block-token="Oo1PduIsxo4HcMx2NRmcxvAMnld">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="UxxWdkIBPoSbjOx7MO8csiFEn5d" colspan="1" rowspan="1"><p data-block-token="NYODddTbmoYoBrxPQ8ectvGxnPe"><code>field_name</code>​</p>

</td><td data-block-token="L2ZGdkB2voKhmsx8ezecoPxmnVf" colspan="1" rowspan="1"><p data-block-token="Y16fdZ6hPoXVlgxSTQjctsTonac">The name of the vector field to index. For full text search, this should be the field that stores the generated sparse vectors. In this example, set the value to <code>sparse</code>.​</p>

</td></tr><tr><td data-block-token="Wn1rdzso5o8AmqxqxiqccBpCnD4" colspan="1" rowspan="1"><p data-block-token="WLDrdOzSXoiKEOxoDREctDounRf"><code>index_type</code>​</p>

</td><td data-block-token="I9TpdLWlXozM3Hx2Z9mcWvDHnNc" colspan="1" rowspan="1"><p data-block-token="Q3cgdK7OTo3kzXxQ1Y2cSarZned">The type of the index to create. <code>AUTOINDEX</code> allows Milvus to automatically optimize index settings. If you need more control over your index settings, you can choose from various index types available for sparse vectors in Milvus. For more information, refer to <a href="https://milvus.io/docs/index.md#Indexes-supported-in-Milvus">Indexes supported in Milvus</a>.​</p>

</td></tr><tr><td data-block-token="KJfgdQmD1odMgdxkG6uczBYknQh" colspan="1" rowspan="1"><p data-block-token="XVCsdz9Ulo93A2xavPtcF9Bvnec"><code>metric_type</code>​</p>

</td><td data-block-token="S3NHds6MTodtrsxRILIc8E1wngh" colspan="1" rowspan="1"><p data-block-token="G9i7dPczzoyJRHxyXbecrWBBn0d">The value for this parameter must be set to <code>BM25</code> specifically for full text search functionality.​</p>

</td></tr></tbody></table>

### Create the collection​

Now create the collection using the schema and index parameters defined.​

<div class="multipleCode">
    <a href="#python">Python </a>
    <a href="#java">Java</a>
    <a href="#javascript">Node.js</a>
    <a href="#curl">cURL</a>
</div>

```python
client.create_collection(​
    collection_name='demo', ​
    schema=schema, ​
    index_params=index_params​
)​

```

```java
import io.milvus.v2.service.collection.request.CreateCollectionReq;

CreateCollectionReq requestCreate = CreateCollectionReq.builder()
        .collectionName("demo")
        .collectionSchema(schema)
        .indexParams(indexes)
        .build();
client.createCollection(requestCreate);
```

```javascript
await client.create_collection(
    collection_name: 'demo', 
    schema: schema, 
    index_params: index_params,
    functions: functions
);
```

```curl
export CLUSTER_ENDPOINT="http://localhost:19530"
export TOKEN="root:Milvus"

curl --request POST \
--url "${CLUSTER_ENDPOINT}/v2/vectordb/collections/create" \
--header "Authorization: Bearer ${TOKEN}" \
--header "Content-Type: application/json" \
-d "{
    \"collectionName\": \"demo\",
    \"schema\": $schema,
    \"indexParams\": $indexParams
}"
```

## Insert text data

After setting up your collection and index, you're ready to insert text data. In this process, you need only to provide the raw text. The built-in function we defined earlier automatically generates the corresponding sparse vector for each text entry.​

<div class="multipleCode">
    <a href="#python">Python </a>
    <a href="#java">Java</a>
    <a href="#javascript">Node.js</a>
    <a href="#curl">cURL</a>
</div>

```python
client.insert('demo', [
    {'text': 'information retrieval is a field of study.'},
    {'text': 'information retrieval focuses on finding relevant information in large datasets.'},
    {'text': 'data mining and information retrieval overlap in research.'},
])

```

```java
import com.google.gson.Gson;
import com.google.gson.JsonObject;

import io.milvus.v2.service.vector.request.InsertReq;

Gson gson = new Gson();
List<JsonObject> rows = Arrays.asList(
        gson.fromJson("{\"text\": \"information retrieval is a field of study.\"}", JsonObject.class),
        gson.fromJson("{\"text\": \"information retrieval focuses on finding relevant information in large datasets.\"}", JsonObject.class),
        gson.fromJson("{\"text\": \"data mining and information retrieval overlap in research.\"}", JsonObject.class)
);

client.insert(InsertReq.builder()
        .collectionName("demo")
        .data(rows)
        .build());
```

```javascript
await client.insert({
collection_name: 'demo', 
data: [
    {'text': 'information retrieval is a field of study.'},
    {'text': 'information retrieval focuses on finding relevant information in large datasets.'},
    {'text': 'data mining and information retrieval overlap in research.'},
]);
```

```curl
curl --request POST \
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/insert" \
--header "Authorization: Bearer ${TOKEN}" \
--header "Content-Type: application/json" \
-d '{
    "data": [
        {"text": "information retrieval is a field of study."},
        {"text": "information retrieval focuses on finding relevant information in large datasets."},
        {"text": "data mining and information retrieval overlap in research."}       
    ],
    "collectionName": "demo"
}'
```

## Perform full text search

Once you've inserted data into your collection, you can perform full text searches using raw text queries. Milvus automatically converts your query into a sparse vector and ranks the matched search results using the BM25 algorithm, and then returns the topK (`limit`) results.​

<div class="multipleCode">
    <a href="#python">Python </a>
    <a href="#java">Java</a>
    <a href="#javascript">Node.js</a>
    <a href="#curl">cURL</a>
</div>

```python
search_params = {​
    'params': {'drop_ratio_search': 0.2},​
}​
​
client.search(​
    collection_name='demo', ​
    data=['whats the focus of information retrieval?'],​
    anns_field='sparse',​
    limit=3,​
    search_params=search_params​
)​

```

```java
import io.milvus.v2.service.vector.request.SearchReq;
import io.milvus.v2.service.vector.request.data.EmbeddedText;
import io.milvus.v2.service.vector.response.SearchResp;

Map<String,Object> searchParams = new HashMap<>();
searchParams.put("drop_ratio_search", 0.2);
SearchResp searchResp = client.search(SearchReq.builder()
        .collectionName("demo")
        .data(Collections.singletonList(new EmbeddedText("whats the focus of information retrieval?")))
        .annsField("sparse")
        .topK(3)
        .searchParams(searchParams)
        .outputFields(Collections.singletonList("text"))
        .build());
```

```javascript
await client.search(
    collection_name: 'demo', 
    data: ['whats the focus of information retrieval?'],
    anns_field: 'sparse',
    limit: 3,
    params: {'drop_ratio_search': 0.2},
)
```

```curl
curl --request POST \
--url "${CLUSTER_ENDPOINT}/v2/vectordb/entities/search" \
--header "Authorization: Bearer ${TOKEN}" \
--header "Content-Type: application/json" \
--data-raw '{
    "collectionName": "demo",
    "data": [
        "whats the focus of information retrieval?"
    ],
    "annsField": "sparse",
    "limit": 3,
    "outputFields": [
        "text"
    ],
    "searchParams":{
        "params":{
            "drop_ratio_search":0.2
        }
    }
}'
```

<table data-block-token="M37Zdx7XdoYN41xdKtfcHcJpnqh"><thead><tr><th data-block-token="UhTwdxk3Mo5eLjxff0PcL1CHn8b" colspan="1" rowspan="1"><p data-block-token="OwUXdMhOgoRxjzx5t9ecKR9Zn6J">Parameter​</p>

</th><th data-block-token="GM88dTMzTof30QxS9O2cVyrnnJd" colspan="1" rowspan="1"><p data-block-token="Nlp5dAJY8or40nxV6auc20XHnjh">Description​</p>

</th></tr></thead><tbody><tr><td data-block-token="QpGIdQ2m0oogCvxColKcNWnYnUc" colspan="1" rowspan="1"><p data-block-token="TkffdBxkKo2hVvx9gGucca46nic"><code>search_params</code>​</p>

</td><td data-block-token="HYemdqt6Dow9tvxOcYScmYdPn8e" colspan="1" rowspan="1"><p data-block-token="JiIOdJrBcoGIQ4xrqYycMdjnn7g">A dictionary containing search parameters.​</p>

</td></tr><tr><td data-block-token="DJDgdH5WUoZQxkxmLzQcXqcXnQh" colspan="1" rowspan="1"><p data-block-token="LKWbdw498o9mtRxm9gDcg28FnQd"><code>params.drop_ratio_search</code>​</p>

</td><td data-block-token="SEJ7d5y18otFTOxy7gLcvLYRnfb" colspan="1" rowspan="1"><p data-block-token="MnladDjOGoUphGxrZzXchD0anzf">Proportion of low-frequency terms to ignore during search. For details, refer to <a href="sparse_vector.md">Sparse Vector</a>.​</p>

</td></tr><tr><td data-block-token="XPPYdAYUPoASg5xuIYmcyxqHnPe" colspan="1" rowspan="1"><p data-block-token="T90ndG7H0okLa4xa1wzcHQmEnEg"><code>data</code>​</p>

</td><td data-block-token="NMhsduxr1oUESPx2J8YcA8csnA1" colspan="1" rowspan="1"><p data-block-token="ZmEQdkdGtofQsAx9YXNcsnlHnYe">The raw query text.​</p>

</td></tr><tr><td data-block-token="O4OVdL9BIollH1xORz3czhInnSh" colspan="1" rowspan="1"><p data-block-token="CYdGd82dRopaWrxfJ9ycWQQnnPc"><code>anns_field</code>​</p>

</td><td data-block-token="MsKIdxGj6oWeBExoFurcxWCnnGh" colspan="1" rowspan="1"><p data-block-token="RsMDdgo0roTSBuxYwm6cGw3inZd">The name of the field that contains internally generated sparse vectors.​</p>

</td></tr><tr><td data-block-token="G0ewd9TQ1o1RQRxZA9ucMO9tnBK" colspan="1" rowspan="1"><p data-block-token="JOyTdUmLIo5aV0x4ChOcLiDQnLh"><code>limit</code>​</p>

</td><td data-block-token="H21hdYGZQoQe5FxYnwCch58qn0g" colspan="1" rowspan="1"><p data-block-token="ATKidHgXoo7c7dxM7cgcE46engb">Maximum number of top matches to return.​</p>

</td></tr></tbody></table>

​

