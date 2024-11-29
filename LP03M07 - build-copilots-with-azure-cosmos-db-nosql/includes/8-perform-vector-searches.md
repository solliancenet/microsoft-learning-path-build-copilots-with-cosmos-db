Integrating the vector search capabilities of Azure Cosmos DB for NoSQL into a Python-based copilot significantly enhances the copilot's ability to perform similarity searches efficiently. Vector search allows for the retrieval of semantically similar items based on vector embeddings, making it ideal for the natural language processing tasks performed by copilots. By leveraging vector search, the copilot can quickly find and retrieve relevant information from large datasets, enriching its responses with accurate and contextually relevant data. This enhances the user experience by providing more precise and meaningful assistance, ultimately making the copilot smarter and more effective.

## Perform vector search using the VectorDistance function

The `VectorDistance` function in Azure Cosmos DB for NoSQL measures the similarity between two vectors by calculating their distance, using metrics like cosine similarity, dot product, or Euclidean distance. This function is vital for applications that require quick and accurate similarity searches, such as those involving natural language processing or recommendation systems. By utilizing `VectorDistance`, developers can efficiently handle high-dimensional vector queries, significantly improving the relevance and performance of their AI-driven applications.

After creating a container with a vector policy and inserting documents containing vector data, you can conduct vector searches using the `VectorDistance` system function. An example of a NoSQL query that projects the similarity score as the alias `SimilarityScore`, and sorts in order of most-similar to least-similar:

```sql
SELECT TOP 3 c.name, c.description, VectorDistance(c.embedding, [1,2,3]) AS SimilarityScore   
FROM c  
ORDER BY VectorDistance(c.embedding, [1,2,3])
```

> Important
>
> You should always use a `TOP N` clause in the `SELECT` statement of your queries. Without it, the vector search will attempt to return many more matches, resulting in the query costing more RUs and having higher latency than necessary.
