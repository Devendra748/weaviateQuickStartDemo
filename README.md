# Weaviate Vector Database Client

This README provides a guide on installing the Weaviate client library, configuring it, and using it to work with the Weaviate Vector Database. Weaviate is a powerful database technology that leverages vectorization for advanced data retrieval and analysis.

## Installation

You can install the Weaviate client library using the following pip command:

```bash
!pip install weaviate-client
```

## Configuration

Before using the Weaviate client, you need to configure it with your API key and endpoint URL. Replace `api_key` and `url` with your specific configuration.

```python
import weaviate

auth_config = weaviate.AuthApiKey(api_key="api_key")

client = weaviate.Client(
      url="url",
      auth_client_secret=auth_config
)
```

## Creating a Schema

You can define a data schema in Weaviate using Python code. Here's an example of creating a class for questions:

```python
class_obj = {
    "class": "Question",
    "vectorizer": "text2vec-openai",  
    "moduleConfig": {
        "text2vec-openai": {},
        "generative-openai": {}  
    }
}

client.schema.create_class(class_obj)
```

## Importing Data

You can import data into Weaviate in batches. Here's an example of importing data from a JSON file:

```python
import requests
import json

resp = requests.get('https://raw.githubusercontent.com/weaviate-tutorials/quickstart/main/data/jeopardy_tiny.json')
data = json.loads(resp.text)

client.batch.configure(batch_size=100)

with client.batch as batch:
    for i, d in enumerate(data):
        properties = {
            "answer": d["Answer"],
            "question": d["Question"],
            "category": d["Category"],
        }
        batch.add_data_object(
            data_object=properties,
            class_name="Question"
        )
```

## Querying Data

You can query data in Weaviate using the Weaviate client. Here's an example of a query:

```python
response = (
    client.query
    .get("Question", ["question", "answer", "category"])
    .with_near_text({"concepts": ["biology"]})
    .with_limit(2)
    .do()
)

print(json.dumps(response, indent=4))
```

This will return relevant questions related to the concept "biology."

That's it! You've successfully installed, configured, and used the Weaviate client to work with the Weaviate Vector Database. Explore more features and functionalities to harness the power of Weaviate in your applications.
