# flushSync()

This operation manually seals a segment and persists the data on disk. It is recommended that this operation be called after all the data has been inserted into a collection. This is the synchronous function that ensures the flush operation is complete before the function returns.

```javascript
flushSync(data): Promise<GetFlushStateResponse>
```

<div class="admonition note">

<p><b>notes</b></p>

<p>Milvus automatically flushes data into persistent storage at intervals. You are advised to rely on this automatic data persistence mechnism.</p>

</div>

## Request Syntax

```javascript
milvusClient.flushSync({
    db_name?: string,
    collection_names: string[],
    timeout?: number
})
```

**PARAMETERS:**

- **db_name** (*string*) -

    The name of the target database to which the target collections belong.

- **collection_names** (*string[]*) -

    **[REQUIRED]**

    A list of the target collection names.

- **timeout** (*number*)  

    The timeout duration for this operation. 

    Setting this to **None** indicates that this operation timeouts when any response arrives or any error occurs.

**RETURNS** *Promise\<GetFlushStateResponse>*

This method returns a promise that resolves to a **GetFlushStateResponse** object.

```javascript
{
    flushed: boolean,
    status: {
        code: number,
        error_code: string | number,
        reason: string
    }
}
```

**PARAMETERS:**

- **flushed** (*boolean*) -

    Whether data is persisted into storage.

- **status** (*ResStatus*) - 

    - **code** (*number*) -

        A code that indicates the operation result. It remains **0** if this operation succeeds.

    - **error_code** (*string* | *number*) -

        An error code that indicates an occurred error. It remains **Success** if this operation succeeds. 

    - **reason** (*string*) - 

        The reason that indicates the reason for the reported error. It remains an empty string if this operation succeeds.

## Example

```java
const milvusClient = new milvusClient(MILUVS_ADDRESS);
const flushSyncStatus = await milvusClient.flushSync({
    collection_names: ['my_collection'],
});
```

