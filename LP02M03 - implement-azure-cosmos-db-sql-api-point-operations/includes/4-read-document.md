To do a point read of an existing item from the container, we need two things.

::: zone pivot=".NET"

First, we need the unique **id** of the item. Here, we store that id in a variable of the same name.

```csharp
string id = "027D0B9A-F9D9-4C96-8213-C8546C4AAE71";
```

Second, we need to create a variable of type **PartitionKey** with the string value for property defined as the partition key for the item we're seeking. We use the combination of the item id and the partition key value to perform the point read.

```csharp
string categoryId = "26C74104-40BC-4541-8EF5-9892F7F03D72";
PartitionKey partitionKey = new (categoryId);
```

Once we have both items, we can invoke the asynchronous and generic **ReadItemAsync<>** method, which returns an item of the given generic type, **Product**, in this example.

```csharp
Product saddle = await container.ReadItemAsync<Product>(id, partitionKey);
```

At this point, we can access properties of the **saddle** variable and print them to the console much like any local variable.

```csharp
string formattedName = $"New Product [${saddle}]";
Console.WriteLine(formattedName);
```

::: zone-end

::: zone pivot="Python"

First, we need the unique **id** of the item. Here, we store that id in a variable.

```python
item_id = "027D0B9A-F9D9-4C96-8213-C8546C4AAE71"
```

Next, define the value of the partition key for the item. We use the combination of the item id and the partition key value to perform the point read.

```python
partition_key_value = "26C74104-40BC-4541-8EF5-9892F7F03D72"
```

Once you have both the `id` and partition key, you can use the **read_item** method of the container object to perform the point read.

```python
saddle = container.read_item(item=item_id, partition_key=partition_key_value)
```

At this point, we can access properties of the **saddle** variable and print them to the console much like any local variable.

```python
formatted_name = f"New Product [{saddle['name']}]"
print(formatted_name)
```

::: zone-end

::: zone pivot="JavaScript"

First, we need the unique **id** of the item. Here, we store that id in a variable.

```javascript
const itemId = "027D0B9A-F9D9-4C96-8213-C8546C4AAE71";
```

Next, define the value of the partition key for the item. We use the combination of the item id and the partition key value to perform the point read.

```javascript
const partitionKeyValue = "26C74104-40BC-4541-8EF5-9892F7F03D72";
```

## Perform the Point Read

Once you have both the `id` and partition key, you can use the **readItem** method of the container object to perform the point read.

```javascript
const { resource: saddle } = await container.item(itemId, partitionKeyValue).read();
```

At this point, we can access properties of the **saddle** variable and print them to the console much like any local variable.

```javascript
const formattedName = `New Product [${saddle.name}]`;
console.log(formattedName);
```

::: zone-end
