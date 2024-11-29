AI copilots, the next generation of intelligent assistants, are redefining productivity by providing context-aware support, enhancing decision-making processes, and automating complex workflows. With Azure Cosmos DB for NoSPythonector search, developers can build advanced AI copilots that deliver precise and efficient solutions by integrating the power of Python and its vast assortment of libraries and tools. Python's versatility in data manipulation integrated with the robust vector search capabilities of Azure Cosmos DB allows copilots to handle complex data queries and provide real-time insights efficiently. Additionally, vector search plays a crucial role when implementing a Retrieval-Augmented Generation (RAG) pattern. It enables the AI to retrieve the most relevant documents from a vast corpus based on similarity to the input query, thus enhancing the generation of accurate and contextually relevant responses. This synergy allows users to focus on strategic tasks while the AI copilots manage the heavy lifting of data processing and analysis.

## Configure a Python virtual environment

Virtual environments in Python are critical for maintaining a clean and organized development environment, offering numerous benefits that can significantly enhance your coding experience. They allow each project to have its own set of dependencies, isolated from others, which prevents conflicts and ensures a consistent development workflow. This isolation is particularly useful when deploying projects to production, as it ensures that the exact versions of dependencies used during development are maintained, reducing the risk of unexpected bugs and incompatibilities. Furthermore, virtual environments make it easier to collaborate with other developers by providing a consistent setup across different machines and development stages. By leveraging virtual environments, you can easily manage package versions, avoid dependency clashes, and ensure your projects run smoothly. This best practice is essential for a stable and dependable coding environment, making your development process more efficient and less prone to issues.

Creating a Python virtual environment can be easily accomplished using a command similar to the following, which creates a virtual environment named `.venv` in the directory in which the command is run:

```bash
python -m venv .venv
```

Once created, you can activate the virtual environment by selecting the appropriate command for your OS and shell from the table below.

| Platform | Shell | Command to activate virtual environment |
| -------- | ----- | --------------------------------------- |
| POSIX | bash/zsh | `source .venv/bin/activate` |
| | fish | `source .venv/bin/activate.fish` |
| | csh/tcsh | `source .venv/bin/activate.csh` |
| | pwsh | `.venv/bin/Activate.ps1` |
| Windows | cmd.exe | `.venv\Scripts\activate.bat` |
| | PowerShell | `.venv\Scripts\Activate.ps1` |

After activating the virtual environment, any required Python libraries can be installed using the `pip install` command. Typically, required libraries and their versions are maintained in a `requirements.txt` file. This file allows versions to be specified and retained within the project, ensuring all libraries and dependencies are maintained between environments. These files store dependencies in the following format:

```python
azure-cosmos==4.9.0
azure-identity==1.19.0
fastapi==0.115.5
openai==1.55.2
pydantic==2.10.2
requests==2.32.3
streamlit==1.40.2
uvicorn==0.32.1
```

Adding all libraries listed in this file can also be accomplished using the `pip install` command by running:

```bash
pip install -r requirements.txt
```

## Securely access Azure resources using Entra ID RBAC

Utilizing Microsoft Entra ID's Role-Based Access Control (RBAC) for authenticating against Azure services like Azure OpenAI and Azure Cosmos DB presents several key benefits over key-based methods. Entra ID RBAC enhances security through precise access controls tailored to user roles, effectively reducing unauthorized access risks. It also streamlines user management, enabling administrators to dynamically assign and modify permissions without the hassle of distributing and maintaining cryptographic keys. Furthermore, this approach enhances compliance and auditability by aligning with organizational policies and facilitating comprehensive access monitoring and review. Entra ID RBAC makes a more efficient and scalable solution for leveraging Azure services by streamlining secure access management.

The following Python code snippet demonstrates how to authenticate and configure a client for interacting with Azure OpenAI services using Microsoft Entra ID RBAC (role-based access control) authentication.

```python
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from openai import AzureOpenAI

# Enable Microsoft Entra ID RBAC authentication
token_provider = get_bearer_token_provider(
 DefaultAzureCredential(),
    "https://cognitiveservices.azure.com/.default"
)

client = AzureOpenAI(
    api_version = AZURE_OPENAI_API_VERSION,
    azure_endpoint = AZURE_OPENAI_ENDPOINT,
    azure_ad_token_provider = token_provider
)
```

The `token_provider = get_bearer_token_provider(DefaultAzureCredential(), "https://cognitiveservices.azure.com/.default")` line above creates a token provider using the `DefaultAzureCredential` and the Azure Cognitive Services scope URL. `DefaultAzureCredential` handles the authentication process, and the `get_bearer_token_provider` utility function returns a token provider that obtains access tokens for Azure services. The `AzureOpenAI` client then uses the created `token_provider` to authenticate requests to the Azure OpenAI service using Microsoft Entra ID RBAC.

## Copilot architecture

Using an architecture with separate frontend and backend layers provides significant extensibility, allowing for the seamless integration of additional functionalities over time. Once the initial copilot is developed, incorporating new features, such as LangChain orchestration, into the APIs becomes straightforward.

![A high-level copilot architecture diagram, showing a UI developed in Python using Streamlit, a backend API written in Python, and interactions with Azure Cosmos DB and Azure OpenAI.](../media/copilot-high-level-architecture-diagram.png)

This modular design enables developers to enhance the backend with advanced capabilities without disrupting the existing front end. For instance, LangChain can be added to handle complex workflows and chain multiple tasks, boosting the copilot's functionality. This flexibility ensures the system remains scalable and adaptable, ready to incorporate future advancements and efficiently meet evolving user needs.

## Create a UI with Streamlit

Streamlit is a powerful open-source Python library that enables rapid development of interactive web applications, making it an ideal choice for building a copilot's user interface. With its intuitive API, developers can quickly create dynamic, responsive chat interfaces that facilitate real-time interactions. Streamlit's built-in support for various widgets and its seamless integration with popular data visualization tools allow for the easy incorporation of chat functionality, enabling users to communicate with the copilot efficiently. By leveraging Streamlit, you can streamline the process of constructing a user-friendly and engaging interface, ensuring a smooth and productive experience for users interacting with your AI copilot.

```python
# Create a copilot UI with Streamlit
import streamlit as st
import requests

st.set_page_config(page_title="Cosmic Works Copilot", layout="wide")

def send_message_to_copilot(message):
    """Send a message to the Copilot's backend chat endpoint."""
 api_endpoint = <backend-api-endpoint>
 request = {"message": message}
 response = requests.post(f"{api_endpoint}/chat", json=request, timeout=60)
    return response.text

def main():
    # Respond to user input
    if prompt := st.chat_input("How I can help you today?"):
        with st.spinner("Awaiting the Copilot's response to your question..."):
            # Display user message in chat message container
 st.chat_message("user").markdown(prompt)

            # Send user message to Copilot and get response
 response = send_message_to_copilot(prompt)

            # Display assistant response in chat message container
            with st.chat_message("assistant"):
 st.markdown(response)
```

## Build a backend API with Python and FastAPI

FastAPI is a modern web framework for building APIs with Python, which is particularly well-suited for creating robust backend APIs. When developing a copilot UI with Streamlit, FastAPI can serve as the powerful backend engine that handles interactions with Azure services like Azure OpenAI and Cosmos DB. By leveraging FastAPI's efficient request handling and ability to integrate seamlessly with asynchronous Python code, developers can quickly set up endpoints that facilitate communication between the Streamlit front end and Azure's services. This setup ensures that user queries to the copilot are processed smoothly, allowing real-time responses and efficient data management. FastAPI's simplicity and high performance make it an excellent choice for building the backend infrastructure needed to support advanced AI-driven copilots.

```pyPythonrom fastapi import FastAPI
from azure.identity import DefaultAzureCredential, get_bearer_token_provider
from openai import AzureOpenAI

app = FastAPI()

# Enable Microsoft Entra ID RBAC authentication
token_provider = get_bearer_token_provider(
 DefaultAzureCredential(),
    "https://cognitiveservices.azure.com/.default"
)

client = AzureOpenAI(
    api_version = AZURE_OPENAI_API_VERSION,
    azure_endpoint = AZURE_OPENAI_ENDPOINT,
    azure_ad_token_provider = token_provider
)

@app.get("/chat")
async def root(message: str, deployment_name: str = "gpt-4o"):
 messages = [{"role": "user", "content": message}]
 completion = client.chat.completions.create(
        model=deployment_name,
        messages=messages,
 )
    return completion
```
