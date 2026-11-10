### Interference openai-proxy API

## Install and start the API

First follow the [source setup guide](git.md), then install API dependencies in
the active virtual environment:

```sh
python -m pip install -e '.[api]'
python -m g4f.cli api --bind 127.0.0.1:1337
```

The editable install also makes `g4f api --bind 127.0.0.1:1337` available.
Open <http://127.0.0.1:1337/v1/models> for a startup check that does not contact a
chat provider. Stop the server with Ctrl+C. Avoid `python -m g4f.api.run` for
this example: that legacy launcher uses port **10000**, not 1337.

For a published release instead of a checkout, install `'g4f[api]'` in a separate
virtual environment; see [additional requirements](requirements.md).

## Optional OpenAI client example

In a second terminal with the virtual environment active, install the client:

```sh
python -m pip install openai
```

This example sends a real provider request; provider availability and any required
authentication are separate from the local server startup check. The API key here
is a placeholder for the local server, not a provider credential.

```python
from openai import OpenAI

client = OpenAI(
    api_key="not-needed",
    base_url="http://127.0.0.1:1337/v1",
)

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "write a poem about a tree"}],
    stream=True,
)

for token in response:
    content = token.choices[0].delta.content
    if content is not None:
        print(content, end="", flush=True)
```

####  API usage (POST)
Send the POST request to /v1/chat/completions with body containing the `model` method. This example uses python with requests library:
```python
import requests
url = "http://localhost:1337/v1/chat/completions"
body = {
    "model": "gpt-3.5-turbo-16k",
    "stream": False,
    "messages": [
        {"role": "assistant", "content": "What can you do?"}
    ]
}
json_response = requests.post(url, json=body).json().get('choices', [])

for choice in json_response:
    print(choice.get('message', {}).get('content', ''))
```

[Return to README](../README.md)