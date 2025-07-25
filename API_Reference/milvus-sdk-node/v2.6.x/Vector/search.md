# search()

This operation conducts a vector similarity search with an optional scalar filtering expression.

```javascript
search(data): Promise<ResStatus>
```

## Request Syntax

```javascript
milvusClient.search({
   collection_name: string,
   partition_names?: string[], 
   data: number[] | number[][], 
   filter: string,
   limit?: number,
   offset?: number
   output_fields?: string | list[string],
   partition_names?: string | list[string],
   consistency_level?: string,
   ignore_growing?: boolean,
   group_by_field?: string,
   group_size?: number,
   strict_group_size?: boolean,
   hints?: string,
   timeout?: number,
 })
```

**PARAMETERS:**

- **collection_name** (*string*) -

    **[REQUIRED]**

    The name of the collection to search

- **consistency_level** (*ConsistencyLevelEnum*) -

    The consistency level of the target collection. The value defaults to **Bounded** (**1**) with options of **Strong** (**0**), **Bounded** (**1**), **Session** (**2**), and **Eventually** (**3**).

- **data** (*number[]* | *number[][]*) -

    A list of vector embeddings.

    Milvus searches for the most similar vector embeddings to the specified ones.

- **filter** (*string*) -

    A scalar filtering condition to filter matching entities. 

    The value defaults to an empty string, indicating that no condition applies.

    You can set this parameter to an empty string to skip scalar filtering. To build a scalar filtering condition, refer to [Boolean Expression Rules](https://milvus.io/docs/boolean.md). 

- **exprValues** (*keyValueObj*) -

    If you choose to use placeholders in `filter` as stated in [Filtering Templating](https://milvus.io/docs/filtering-templating.md), then you can specify the actual values for these placeholders as key-value pairs as the value of this parameter.

- **ignore_growing** (*boolean*) -

    A boolean value indicating whether to skip the search in growing segments.

- **limit** (*number*) - 

    The total number of entities to return.

    You can use this parameter in combination with **offset** in **param** to enable pagination.

    The sum of this value and **offset** in **param** should be less than 16,384. 

    In a grouping search, however, `limit` specifies the maximum number of groups to return, rather than individual entities. Each group is formed based on the specified `group_by_field`.

- **offset** (*number*) - 

    The number of records to skip in the search result. 

    You can use this parameter in combination with `limit` to enable pagination.

    The sum of this value and `limit` should be less than 16,384. 

- **params** (*KeyValueObj*) -

    The additional search parameters in key-value pairs.

    - **radius** (*number*) -

        Determines the threshold of least similarity. When setting `metric_type` to `L2`, ensure that this value is greater than that of **range_filter**. Otherwise, this value should be lower than that of **range_filter**. 

    - **range_filter**  (*number*) -  

        Refines the search to vectors within a specific similarity range. When setting `metric_type` to `IP` or `COSINE`, ensure that this value is greater than that of **radius**. Otherwise, this value should be lower than that of **radius**.

    - **max_empty_result_buckets** (*number*)

        This param is only used for range search for IVF-serial indexes, including **BIN_IVF_FLAT**, **IVF_FLAT**, **IVF_SQ8**, **IVF_PQ**, and **SCANN**. The value defaults to 1 and ranges from 1 to 65536.

        During range search, the search process terminates early if the number of buckets with no valid range search results reaches the specified value. Increasing this parameter improves range search recall.

    - **output_fields** (*string[]*) -

        A list of field names to include in each entity in return.

        The value defaults to **None**. If left unspecified, only the primary field is included.

    - **partition_names** (*string[]*) -

        A list of the names of the partitions to search.

    - **timeout** (*number*) -

        The timeout duration for this operation. Setting this to **None** indicates that this operation timeouts when any response arrives or any error occurs.

- **output_fields** (*string[]*) -

    A list of field names to include in each entity in return.

    The value defaults to **None**. If left unspecified, only the primary field is included.

- **partition_names** (*string[]*) -

    A list of the names of the partitions to search.

- **group_by_field** (*string*) -

    Groups search results by a specified field to ensure diversity and avoid returning multiple results from the same group.

- **group_size** (*number*) -

    The target number of entities to return within each group in a grouping search. For example, setting `group_size=2` instructs the system to return up to 2 of the most similar entities (e.g., document passages or vector representations) within each group. Without setting `group_size`, the system defaults to returning only 1 entity per group.

- **strict_group_size** (*boolean*) -

    This Boolean parameter dictates whether `group_size` should be strictly enforced. When `group_size=true`, the system will attempt to fill each group with exactly `group_size` results, as long as sufficient data exists within each group. If there is an insufficient number of entities in a group, it will return only the available entities, ensuring that groups with adequate data meet the specified `group_size`.

- **hints** (*string*) -

- **timeout** (*number*) -

    The timeout duration for this operation. Setting this to **None** indicates that this operation timeouts when any response arrives or any error occurs.

**RETURNS** *Promise\<SearchResults>*

This method returns a promise that resolves to a **SearchResults** object.

```javascript
{
    status: object,
    results: list[string],
    recalls: list[number]
}
```

**PARAMETERS:**

- **status** (*object*) -

    - **code** (*number*) -

        A code that indicates the operation result. It remains **0** if this operation succeeds.

    - **error_code** (*string* | *number*) -

        An error code that indicates an occurred error. It remains **Success** if this operation succeeds. 

    - **reason** (*string*) - 

        The reason that indicates the reason for the reported error. It remains an empty string if this operation succeeds.

- **results** (*list[object]*) -

    Each result object has the following keys:

    - **id** (*string*) -

        The ID of the search result

    - **score**(*number*) -

        The similarity score of the search result.

    - Plus output fields and their values.

- **recalls** (*list[number]*) -

    Each number indicates the recall rate of a search against a query vector.

## Example

```plaintext
const milvusClient = new milvusClient(MILUVS_ADDRESS);
const searchResults = await milvusClient.search({
   collection_name: 'my_collection',
   vector: [1, 2, 3, 4],
});
```

