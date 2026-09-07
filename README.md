# AI Engineering Coursework

This repository contains coursework for my AI Engineering class. For this assignment, I completed both the hosted API task using the OpenAI API and the local model task using Ollama.

## Assignment Overview

The assignment introduces two different ways of working with foundation models:

1. Using a hosted AI model through an API.
2. Running a language model locally on a computer.

For the hosted API portion, I used OpenAI. For the local model portion, I used Ollama with the `llama3.2:3b` model.

---

## Part A — OpenAI API

### Python Script

The Python script sends a prompt to OpenAI's `gpt-4o-mini` model and prints the generated response in the terminal.

The prompt used in the assignment asks:

> What jobs are currently available for AI engineers, Explain job and functions.

The script:

```python
import os
from openai import OpenAI

client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])

response = client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {
            "role": "user",
            "content": "What jobs are currently available for AI engineers, Explain job and functions."
        }
    ]
)

print(response.choices[0].message.content)
```

### Requirements

* Python 3
* OpenAI Python SDK
* OpenAI API key

Install the OpenAI SDK with:

```bash
pip install openai
```

### API Key Setup

The API key is stored as an environment variable rather than being hardcoded into the Python file.

On Windows PowerShell:

```powershell
setx OPENAI_API_KEY "your-api-key-here"
```

After setting the environment variable, open a new PowerShell window.

To verify that the variable exists without displaying the actual API key:

```powershell
if ($env:OPENAI_API_KEY) { "API key is set" } else { "API key is NOT set" }
```

The API key is intentionally **not included in this repository**.

### Running the Script

Navigate to the repository directory and run:

```bash
python aicourse_1.py
```

When successful, the program sends the prompt to the OpenAI API and prints the model-generated response directly in the terminal.

A screenshot/text file showing the successful API response is included in this repository as evidence that the API call worked.

---

## Part B — Local Model with Ollama

For the second part of the assignment, I installed Ollama and ran a language model directly on my computer.

The model used was:

```text
llama3.2:3b
```

### Downloading the Model

```bash
ollama pull llama3.2:3b
```

### Running the Model

```bash
ollama run llama3.2:3b
```

After launching the model, I entered a prompt directly into the Ollama terminal interface and received a generated response.

Unlike the OpenAI portion, this model runs locally instead of sending the prompt to a remotely hosted API.

A screenshot/text file of the successful Ollama interaction is included in this repository.

---

## Repository Contents

```text
ai-engineering-coursework/
│
├── aicourse_1.py
├── README.md
├── openai-output/
│   └── OpenAI API output screenshot or text
│
└── ollama-output/
    └── Ollama session screenshot or text
```

The exact screenshot or output filenames may differ depending on how the evidence files are saved.

---

## Hosted API vs. Local Model

This assignment demonstrated two different approaches to using foundation models.

**OpenAI API:** The Python program sends a request over the internet to a model hosted by OpenAI. Authentication is handled using an API key stored securely as an environment variable.

**Ollama:** The model is downloaded and executed locally on the computer. Once downloaded, the model can be interacted with directly through the terminal without making the same type of hosted API request.

Completing both parts helped demonstrate the difference between consuming a hosted foundation model as a service and running a foundation model locally.

---

## Security

API keys and other credentials should never be committed to GitHub.

The OpenAI API key used for this assignment is stored only as an environment variable:

```text
OPENAI_API_KEY
```

No API credentials are included in the source code or repository.
