# How to Connect to Weaviate, Define a Class, Use Custom Vectors, and Perform Queries

## Step 1: Install Weaviate Client Library

Before you begin, make sure you have the Weaviate client library installed. We recommend using the Python client library for this tutorial.

```python
pip install weaviate-client
```

## Step 2: Create an Instance on Weaviate Cloud Services (WCS)

To use Weaviate, you'll need an instance. You can create a sandbox instance on Weaviate Cloud Services (WCS). Make sure to collect the API key and URL from the Details tab.

[Follow WCS Setup Instructions](#) (link to WCS setup instructions)

## Step 3: Connect to Weaviate

Now, let's connect to your Weaviate instance using the provided information:

```python
import weaviate
import json

client = weaviate.Client(
    url="https://your-weaviate-instance-url",  # Replace with your Weaviate instance URL
    auth_client_secret=weaviate.AuthApiKey(api_key="YOUR-WEAVIATE-API-KEY"),  # Replace with your Weaviate instance API key
    additional_headers={
        "X-OpenAI-Api-Key": "YOUR-OPENAI-API-KEY"  # Replace with your OpenAI API key
    }
)
```

Now you are connected to your Weaviate instance.

## Step 4: Define a Class

In Weaviate, you define a class to store objects. Let's create a class named "MyClass" and specify the vectorizer and generative module:

```python
class_obj = {
    "class": "MyClass",
    "vectorizer": "text2vec-openai",
    "moduleConfig": {
        "text2vec-openai": {},
        "generative-openai": {}  # Ensure the `generative-openai` module is used for generative queries
    }
}

client.schema.create_class(class_obj)
```

Now you have a class ready to store objects.

## Step 5: Add Objects

You can add objects to Weaviate using a batch import process. We will cover two methods: using a vectorizer or providing custom vectors.

### Option 1: Using a Vectorizer

This code imports objects without specifying a vector. Weaviate will use the vectorizer defined for the class to create vector embeddings for each object:

```python
import requests
resp = requests.get('https://raw.githubusercontent.com/weaviate-tutorials/quickstart/main/data/jeopardy_tiny.json')
data = json.loads(resp.text)  # Load data

client.batch.configure(batch_size=100)  # Configure batch
with client.batch as batch:  # Initialize a batch process
    for i, d in enumerate(data):  # Batch import data
        print(f"Importing question: {i+1}")
        properties = {
            "answer": d["Answer"],
            "question": d["Question"],
            "category": d["Category"],
        }
        batch.add_data_object(
            data_object=properties,
            class_name="MyClass"
        )
```

### Option 2: Providing Custom Vectors

Alternatively, you can provide your own vectors to Weaviate:

```python
import requests
fname = "jeopardy_tiny_with_vectors_all-OpenAI-ada-002.json"
url = f'https://raw.githubusercontent.com/weaviate-tutorials/quickstart/main/data/{fname}'
resp = requests.get(url)
data = json.loads(resp.text)  # Load data

client.batch.configure(batch_size=100)  # Configure batch
with client.batch as batch:  # Configure a batch process
    for i, d in enumerate(data):  # Batch import all Questions
        print(f"Importing question: {i+1}")
        properties = {
            "answer": d["Answer"],
            "question": d["Question"],
            "category": d["Category"],
        }
        batch.add_data_object(
            data_object=properties,
            class_name="MyClass",
            vector=d["vector"]  # Add custom vector
        )
```

## Step 6: Queries

Now that you've built your vector database and populated it with data, let's run queries.

### Semantic Search

Perform a similarity search to find quiz objects most similar to "biology":

```python
response = (
    client.query
    .get("MyClass", ["question", "answer", "category"])
    .with_near_text({"concepts": ["biology"]})
    .with_limit(2)
    .do()
)

print(json.dumps(response, indent=4))
```

### Semantic Search with a Filter

Add a filter to search only in objects with a "category" value of "ANIMALS":

```python
response = (
    client.query
    .get("MyClass", ["question", "answer", "category"])
    .with_near_text({"concepts": ["biology"]})
    .with_where({
        "path": ["category"],
        "operator": "Equal",
        "valueText": "ANIMALS"
    })
    .with_limit(2)
    .do()
)

print(json.dumps(response, indent=4))
```

### Generative Search (Single Prompt)

Perform a generative search with a single prompt and get plain-language explanations for each answer:

```python
response = (
    client.query
    .get("MyClass", ["question", "answer", "category"])
    .with_near_text({"concepts": ["biology"]})
    .with_generate(single_prompt="Explain {answer} as you might to a five-year-old.")
    .with_limit(2)
    .do()
)

print(json.dumps(response, indent=4))
```

### Generative Search (Grouped Task)

Perform a generative search with a grouped task prompt and generate a tweet about the search results:

```python
response = (
    client.query
    .get("MyClass", ["question", "answer", "category"])
    .with_near_text({"concepts": ["biology"]})
    .with_generate(grouped_task="Write a tweet with emojis about these facts.")
    .with_limit(2)
    .do()
)

print(response["data"]["Get"]["MyClass"][0]["_additional"]["generate"]["groupedResult"])
```

Congratulations! You have successfully connected to Weaviate, defined a class, used custom vectors, and performed various queries. You can explore more advanced Weaviate features and continue learning in the Weaviate Academy. Happy querying!
