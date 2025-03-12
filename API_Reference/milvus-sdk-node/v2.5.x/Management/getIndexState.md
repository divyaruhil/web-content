# getIndexState()

This operation gets the status of the specified index.

```javascript
getIndexState(data): Promise<GetIndexStateResponse>
```

## Request Syntax

```javascript
milvusClient.getIndexState({
      db_name?: string,
      collection_name: string,
      field_name?: string,
      index_name?: string,
      timeout?: number
});
```

**PARAMETERS:**

- **db_name** (*string*) -

    The name of the database that holds the target collection.

- **collection_name** (*string*) -

    **[REQUIRED]**

    The name of an existing collection.

- **index_name** (*string*) -

    The name of the target index. This parameter and `field_name` are mutually exclusive. 

- **field_name** (*string*) -

    The name of the target field. This parameter and `index_name` are mutually exclusive. When you use this parameter, ensure that an index has been built upon the specified field.

- **timeout** (number) -

    The timeout duration for this operation. Setting this to **None** indicates that this operation timeouts when any response arrives or any error occurs.

**RETURNS** *Promise\<GetIndexStateResponse>*

This method returns a promise that resolves to a **GetIndexStateResponse** object.

```javascript
{
    state: IndexState,
    status: {
        code: number,
        error_code: string | number,
        reason: string
    }
}
```

**PARAMETERS:**

- **state** (*IndexState*) -

    The state of the specified index. Possible values are as follows:

    - **Failed** (4)

        The index building process failed.

    - **Finished** (3)

        The index building process succeeds.

    - **InProgress** (2)

        The index building process is in progress.

    - **Unissued** (1)

        The index building process has not started yet.

    - **IndexStateNone** (0)

        The index state is unknown.

- **total_rows** (*number*) -

    The number of entities that already persisted in the specified collection.

- **status** (*ResStatus*) -  

    The status of the response.

    - **code** (*number*) -

        A code that indicates the operation result. It remains **0** if this operation succeeds.

    - **error_code** (*string* | *number*) -

        An error code that indicates an occurred error. It remains **Success** if this operation succeeds. 

    - **reason** (*string*) - 

        The reason that indicates the reason for the reported error. It remains an empty string if this operation succeeds.

## Example

```java
const milvusClient = new MilvusClient(MILUVS_ADDRESS);
const getIndexStateReq = {
  collection_name: 'my_collection',
  index_name: 'my_index',
};
const res = await milvusClient.getIndexState(getIndexStateReq);
console.log(res);
```

