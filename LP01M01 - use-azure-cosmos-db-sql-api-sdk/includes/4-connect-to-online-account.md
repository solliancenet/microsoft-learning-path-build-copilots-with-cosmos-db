Once the client library is imported, you can begin using the namespaces and classes within your project.

::: zone pivot=".NET"

## Import the namespace

Before using the library, you should import the **Microsoft.Azure.Cosmos** namespace using a **directive**. The using directive allows you to use types within the namespace without being forced to fully qualify each type.

```csharp
using Microsoft.Azure.Cosmos;
```

## Use the CosmosClient class

The two most common ways to create an instance for the **CosmosClient** class is to instantiate it with one of the following two constructors:

- A constructor that takes a single string value representing the connection string for the account.
- A constructor that takes two string values representing the endpoint and a key for the account.

> [!NOTE]
> You can always retrieve the connection string, endpoint, or any of the keys from the Azure portal. For the examples in this section, we will use a fictional endpoint of **https­://dp420.documents.azure.com:443/** and a sample key of **fDR2ci9QgkdkvERTQ==**.

> [!TIP]
> You can also use the CosmosClient class with the Microsoft Identity Platform directly for Microsoft Entra ID authentication, but that is beyond the scope of this module.

### Use with a connection string

The **CosmosClient** class has a constructor that only takes a single string value. Pass in the connection string of the account to use this constructor. This example uses a connection string in the ``AccountEndpoint=<account-endpoint>;AccountKey=<account-key>`` format with the fictional endpoint and key.

```csharp
string connectionString = "AccountEndpoint=https­://dp420.documents.azure.com:443/;AccountKey=fDR2ci9QgkdkvERTQ==";

CosmosClient client = new (connectionString);
```

### Use with an endpoint and key

Alternatively, you can use a constructor of the **CosmosClient** class that takes in two string parameters representing the account's **endpoint** and **key** in that order. This example uses the fictional endpoint and key.

```csharp
string endpoint = "https­://dp420.documents.azure.com:443/";
string key = "fDR2ci9QgkdkvERTQ==";

CosmosClient client = new (endpoint, key);
```

## Read properties of the account

> [!TIP]
> At this point, you only have a logical client-side representation of the Azure Cosmos DB for NoSQL account. The SDK won't initially connect to the account until you perform an operation.

Once the client instance is instantiated, you can use various methods directly. For example, you can asynchronously invoke the **ReadAccountAsync** method to get an object of type **AccountProperties** with various properties.

```csharp
AccountProperties account = await client.ReadAccountAsync();
```

The **AccountProperties** class includes useful properties such as, but not limited to:

| **Property** | **Description** |
| --- | --- |
| **Id** | Gets the unique name of the account |
| **ReadableRegions** | Gets a list of readable locations for the account |
| **WritableRegions** | Gets a list of writable locations for the account |
| **Consistency** | Gets the default consistency level for the account |

## Interact with a database

Once you have a client instance, you can retrieve or create a database using one of three methods:

- Retrieve an existing database using the name
- Create a new database passing in a unique database name
- Have the SDK check for the existence of the database and either create or retrieve it automatically

Any of these three methods will return an instance of type **Database** that you can use to interact with the database.

### Retrieve an existing database

```csharp
Database database = client.GetDatabase("cosmicworks");
```

### Create a new database

```csharp
Database database = await client.CreateDatabaseAsync("cosmicworks");
```

### Create database if it doesn't already exist

```csharp
Database database = await client.CreateDatabaseIfNotExistsAsync("cosmicworks");
```

## Interact with a container

Now that you have a database instance, you can retrieve or create a container using one of three methods:

- Retrieve an existing container using just the name
- Create a new container passing in a unique container name, partition key path, and the amount of throughput to manually provision
- Have the SDK check for the existence of the container and either create or retrieve it automatically

Any of these three methods will return an instance of type **Container** that you can use to interact with the container.

### Retrieve an existing container

```csharp
Container container = database.GetContainer("products");
```

### Create a new container

```csharp
Container container = await database.CreateContainerAsync(
    new ContainerProperties(chatContainerName, "/categoryId"), 
    ThroughputProperties.CreateAutoscaleThroughput(1000)
);
```

### Create container if it doesn't already exist

```csharp
Container container = await database.CreateContainerIfNotExistsAsync(
    new ContainerProperties(chatContainerName, "/categoryId"), 
    ThroughputProperties.CreateAutoscaleThroughput(1000)
);
```

## Implement client singleton

Each instance of the ``CosmosClient`` class has a few features that are already implemented on your behalf:

- Instances are already thread-safe
- Instances efficiently manage connections
- Instances cache addresses when operating in **direct** mode

Because of this behavior, each time you destroy and recreate and instance within a single .NET ``AppDomain``, the new instances lose the benefits of the caching and connection management.

The Azure Cosmos DB for NoSQL SDK team recommends that you use a single instance per ``AppDomain`` for the lifetime of the application. This small change to your setup allows for better SDK client-side performance and more efficient connection management.

> [!TIP]
> It's simple to use a singleton in a typical .NET console application. For ASP.NET web applications, you should review how to create a singleton instance using the dependency injection framework of your choice.

::: zone-end

::: zone pivot="Python"

## Import the library

Before using the library, you need to import the **CosmosClient** and other necessary classes from the **azure.cosmos** module. Importing these classes allows you to access types without needing to fully qualify each type.

```python
from azure.cosmos import CosmosClient, PartitionKey, ThroughputProperties, ContainerProxy, exceptions
```

## Use the CosmosClient class

The two most common ways to create an instance of the **CosmosClient** class are:

- Using a connection string that represents the account.
- Using an endpoint and a key for the account.

> [!NOTE]
> You can retrieve the connection string, endpoint, or account keys from the Azure portal. For examples in this section, a fictional endpoint of **https://dp420.documents.azure.com:443/** and a sample key of **fDR2ci9QgkdkvERTQ==** will be used.

> [!TIP]
> You can also use the CosmosClient class with the Microsoft Identity Platform for Microsoft Entra ID authentication, but that is beyond the scope of this module.

### Use with a connection string

The **CosmosClient** class can be instantiated by passing the connection string. This example uses a connection string in the format `AccountEndpoint=<account-endpoint>;AccountKey=<account-key>`.

```python
connection_string = "AccountEndpoint=https://dp420.documents.azure.com:443/;AccountKey=fDR2ci9QgkdkvERTQ=="

client = CosmosClient.from_connection_string(connection_string)
```

### Use with an endpoint and key

Alternatively, you can create an instance of the **CosmosClient** class by providing the endpoint and the key as two separate parameters.

```python
endpoint = "https://dp420.documents.azure.com:443/"
key = "fDR2ci9QgkdkvERTQ=="

client = CosmosClient(endpoint, key)
```

## Read properties of the account

> [!TIP]
> At this point, the client instance is just a logical representation of the Azure Cosmos DB account. No connection is made until you perform an operation.

Once the client instance is created, you can use it to perform various operations. For example, you can use the **client.get_database_account()** method to access account properties.

```python
account_info = client.get_database_account()
```

The `account_info` object represents an instance of the `DatabaseAccount`, which includes useful properties, such as:

| **Property** | **Description** |
| --- | --- |
| **EnableMultipleWritableLocations** | Flag on the azure Cosmos account that indicates if writes can take place in multiple locations |
| **ReadableLocations** | A list of readable locations for the account |
| **WritableLocations** | A list of writable locations for the account |
| **ConsistencyPolicy** | The default consistency level for the account |

## Interact with a database

Once you have a client instance, you can retrieve or create a database using one of three methods:

- Retrieve an existing database using its name.
- Create a new database by passing a unique database name.
- Check for the existence of the database and either create or retrieve it automatically.

Any of these methods will return a **DatabaseProxy** instance that you can use to interact with the database.

### Retrieve an existing database

```python
database = client.get_database_client("cosmicworks")
```

### Create a new database

```python
database = client.create_database("cosmicworks")
```

### Create a database if it doesn't already exist

```python
database = client.create_database_if_not_exists("cosmicworks")
```

## Interact with a container

With a database instance, you can retrieve or create a container using one of the following methods:

- Retrieve an existing container by name.
- Create a new container by specifying a unique container name, partition key path, and manually provisioned throughput.
- Check for the existence of the container and either create or retrieve it automatically.

Each method returns a **ContainerProxy** instance that you can use to interact with the container.

### Retrieve an existing container

```python
container = database.get_container_client("products")
```

### Create a new container

```python
container = database.create_container(
    id="products",
    partition_key=PartitionKey(path="/categoryId"),
    throughput=ThroughputProperties(auto_scale_max_throughput=1000)
)
```

### Create a container if it doesn't already exist

```python
container = database.create_container_if_not_exists(
    id="products",
    partition_key=PartitionKey(path="/categoryId"),
    offer_throughput=ThroughputProperties(auto_scale_max_throughput=1000)
)
```

::: zone-end

::: zone pivot="JavaScript"

## Import the library

Before using the library, you need to import the **CosmosClient** and other necessary classes from the **@azure/cosmos** package. Importing these classes allows you to access types without needing to fully qualify each type.

```javascript
const { CosmosClient, Database, Container } = require("@azure/cosmos");
```

## Use the CosmosClient class

The two most common ways to create an instance of the **CosmosClient** class are:

- Using a connection string that represents the account.
- Using an endpoint and a key for the account.

> [!NOTE]
> You can retrieve the connection string, endpoint, or account keys from the Azure portal. For examples in this section, a fictional endpoint of **https://dp420.documents.azure.com:443/** and a sample key of **fDR2ci9QgkdkvERTQ==** will be used.

> [!TIP]
> You can also use the CosmosClient class with the Microsoft Identity Platform for Microsoft Entra ID authentication, but that is beyond the scope of this module.

### Use with a connection string

The **CosmosClient** class can be instantiated by passing the connection string. This example uses a connection string in the format `AccountEndpoint=<account-endpoint>;AccountKey=<account-key>`.

```javascript
const connectionString = "AccountEndpoint=https://dp420.documents.azure.com:443/;AccountKey=fDR2ci9QgkdkvERTQ==";

const client = new CosmosClient({ connectionString });
```

### Use with an endpoint and key

Alternatively, you can create an instance of the **CosmosClient** class by providing the endpoint and the key as two separate parameters.

```javascript
const endpoint = "https://dp420.documents.azure.com:443/";
const key = "fDR2ci9QgkdkvERTQ==";

const client = new CosmosClient({ endpoint, key });
```

## Read properties of the account

> [!TIP]
> At this point, the client instance is just a logical representation of the Azure Cosmos DB account. No connection is made until you perform an operation.

Once the client instance is created, you can use it to perform various operations. For example, you can retrieve account properties using the **getDatabaseAccount** method.

```javascript
async function readAccountProperties() {
    const { resource: accountInfo } = await client.getDatabaseAccount();
    console.log(accountInfo);
}

readAccountProperties().catch((error) => console.error(error));
```

The `accountInfo` object represents an instance of the **DatabaseAccount**, which includes useful properties, such as:

| **Property** | **Description** |
| --- | --- |
| **enableMultipleWritableLocations** | Flag on the azure Cosmos account that indicates if writes can take place in multiple locations |
| **readableLocations** | A list of readable locations for the account |
| **writableLocations** | A list of writable locations for the account |
| **consistencyPolicy** | The default consistency level for the account |

## Interact with a database

Once you have a client instance, you can retrieve or create a database using one of three methods:

- Retrieve an existing database using its name.
- Create a new database by passing a unique database name.
- Check for the existence of the database and either create or retrieve it automatically.

Any of these methods will return a **Database** instance that you can use to interact with the database.

### Retrieve an existing database

```javascript
const database = client.database("cosmicworks");
```

### Create a new database

```javascript
const { resource: database } = await client.databases.createIfNotExists({ id: "cosmicworks" });
```

### Create a database if it doesn't already exist

```javascript
const { resource: database } = await client.databases.createIfNotExists({ id: "cosmicworks" });
```

## Interact with a container

With a database instance, you can retrieve or create a container using one of the following methods:

- Retrieve an existing container by name.
- Create a new container by specifying a unique container name, partition key path, and manually provisioned throughput.
- Check for the existence of the container and either create or retrieve it automatically.

Each method returns a **Container** instance that you can use to interact with the container.

### Retrieve an existing container

```javascript
const container = database.container("products");
```

### Create a new container

```javascript
const { resource: container } = await database.containers.create({
    id: "products",
    partitionKey: {
        paths: ["/categoryId"]
    },
    maxThroughput: 1000
});
```

### Create a container if it doesn't already exist

```javascript
const { resource: container } = await database.containers.createIfNotExists({
    id: "products",
    partitionKey: {
        paths: ["/categoryId"]
    },
    maxThroughput: 1000
});
```

::: zone-end
