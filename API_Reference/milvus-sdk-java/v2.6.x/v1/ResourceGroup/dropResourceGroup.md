# dropResourceGroup()

MilvusClient interface. This method drops a resource group by name.

```java
R<RpcStatus> dropResourceGroup(DropResourceGroupParam requestParam);
```

#### DropResourceGroupParam

Use the `DropResourceGroupParam.Builder` to construct a `DropResourceGroupParam` object.

```java
import io.milvus.param.DropResourceGroupParam;
DropResourceGroupParam.Builder builder = DropResourceGroupParam.newBuilder();
```

Methods of `DropResourceGroupParam.Builder`:

<table>
    <tr>
        <th><p>Method</p></th>
        <th><p>Description</p></th>
        <th><p>Parameters</p></th>
    </tr>
    <tr>
        <td><p>withGroupName(String groupName)</p></td>
        <td><p>Sets the group name. groupName cannot be empty or null.</p></td>
        <td><p>groupName: The name of the group to be dropped.</p></td>
    </tr>
    <tr>
        <td><p>build()</p></td>
        <td><p>Construct a DropResourceGroupParam object.</p></td>
        <td><p>N/A</p></td>
    </tr>
</table>

The `DropResourceGroupParam.Builder.build()` can throw the following exceptions:

- ParamException: error if the parameter is invalid.

#### Returns

This method catches all the exceptions and returns an `R<RpcStatus>` object.

- If the API fails on the server side, it returns the error code and message from the server.

- If the API fails by RPC exception, it returns `R.Status.Unknown` and error message of the exception.

- If the API succeeds, it returns `R.Status.Success`.

#### Example

```java
import io.milvus.param.DropResourceGroupParam;

R<RpcStatus> response = client.dropResourceGroup(DropResourceGroupParam.newBuilder()
            .withGroupName(name)
            .build());

if (response.getStatus() != R.Status.Success.getCode()) {
    throw new RuntimeException(response.getMessage());
}
```
