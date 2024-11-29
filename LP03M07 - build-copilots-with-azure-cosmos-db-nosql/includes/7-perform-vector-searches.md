Integrating the vector search capabilities of Azure Cosmos DB for NoSQL into a Python-based copilot significantly enhances the copilot's ability to perform similarity searches efficiently. Vector search allows for the retrieval of semantically similar items based on vector embeddings, making it ideal for the natural language processing tasks performed by copilots. By leveraging vector search, the copilot can quickly find and retrieve relevant information from large datasets, enriching its responses with accurate and contextually relevant data. This enhances the user experience by providing more precise and meaningful assistance, ultimately making the copilot smarter and more effective.

## What is vector search?

Vector search is a technique that allows items to be found based on their data characteristics instead of exact matches on a specific property or field. Instead of requiring exact matches, vector search enables you to identify matches based on their vector representations. This technique is advantageous when performing similarity searches and is particularly valuable in applications that require searching for information within large blocks of text. It plays a critical role in the implementation of the retrieval augmented generation (RAG) pattern frequently used by AI copilots by enabling secure and efficient integration of proprietary data into large language model (LLM) responses, ensuring that AI copilots can provide precise, domain-specific answers while maintaining the confidentiality and integrity of sensitive information.

## Understand the steps involved in vector search

TODO: Rewrite the below paragraph

Let’s take an example of creating a database for an internet-based bookstore and you're storing Title, Author, ISBN, and Description for each book. We’ll also define two properties to contain vector embeddings. The first is the "embedding" property, which contains text embeddings generated from the description content of the product (for example, concatenating the “title” “author” “isbn” and “description” properties before creating the embedding).

1. Create and store vector embeddings for the fields on which you want to perform vector search.
2. Specify the vector embedding paths in the vector embedding policy.
3. Include any desired vector indexes in the indexing policy for the container.

## Perform vector search using the VectorDistance function

The `VectorDistance` function in Azure Cosmos DB for NoSQL measures the similarity between two vectors by calculating their distance, using metrics like cosine similarity, dot product, or Euclidean distance. This function is vital for applications that require quick and accurate similarity searches, such as those involving natural language processing or recommendation systems. By utilizing `VectorDistance`, developers can efficiently handle high-dimensional vector queries, significantly improving the relevance and performance of their AI-driven applications.

TODO: Combine the below paragraph and sentences, rewriting to make them original...

Once you create a container with the desired vector policy, and insert vector data into the container, you can conduct a vector search using the Vector Distance system function in a query. Suppose you want to search for books about food recipes by looking at the description, you first need to get the embeddings for your query text. In this case, you might want to generate embeddings for the query text – “food recipe”. Once you have the embedding for your search query, you can use it in the VectorDistance function in the vector search query and get all the items that are similar to your query as shown here:

After creating a container with a vector policy and inserting documents containing vector data, you can conduct vector searches using the `VectorDistance` system function. An example of a NoSQL query that projects the similarity score as the alias `SimilarityScore`, and sorts in order of most-similar to least-similar:

```sql
SELECT TOP 3 c.name, c.description, VectorDistance(c.embedding, [1,2,3]) AS SimilarityScore   
FROM c  
ORDER BY VectorDistance(c.embedding, [1,2,3])
```

> Important
>
> You should always use a `TOP N` clause in the `SELECT` statement of your queries. Without it, the vector search will attempt to return many more matches, resulting in the query costing more RUs and having higher latency than necessary.
