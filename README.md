# UI Engineer By LLMs

This is an open-source version of apps like [Anthropic's Claude Artifacts](https://www.anthropic.com/news/claude-3-5-sonnet), Vercel [v0](https://v0.dev), or [GPT Engineer](https://gptengineer.app).

![Preview](preview.png)

[→ Try on ai-ui-engineer.vercel.app]((https://ai-ui-engineer.vercel.app/))

## Features

- Based on Next.js 14 (App Router, Server Actions), shadcn/ui, TailwindCSS, Vercel AI SDK.
- Streaming in the UI.
- Can install and use any package from npm, pip.
- Supported stacks ([add your own](#adding-custom-personas)):
  - 🔸 Python interpreter
  - 🔸 Next.js
  - 🔸 Vue.js
  - 🔸 Streamlit
  - 🔸 Gradio
- Supported LLM Providers ([add your own](#adding-custom-llm-models)):
  - 🔸 OpenAI
  - 🔸 Anthropic
  - 🔸 Google AI
  - 🔸 Mistral
  - 🔸 Groq
  - 🔸 Fireworks
  - 🔸 Together AI
  - 🔸 Ollama

## Get started

### Prerequisites

- [git](https://git-scm.com)
- Recent version of [Node.js](https://nodejs.org) and npm package manager
- [E2B API Key](https://e2b.dev)
- LLM Provider API Key

### 1. Clone the repository

In your terminal:

```
git clone https://github.com/e2b-dev/fragments.git
```

### 2. Install the dependencies

Enter the repository:

```
cd fragments
```

Run the following to install the required dependencies:

```
npm i
```

### 3. Set the environment variables

Create a `.env.local` file and set the following:

```sh
# Get your API key here - https://e2b.dev/
E2B_API_KEY="your-e2b-api-key"

# OpenAI API Key
OPENAI_API_KEY=

# Other providers
ANTHROPIC_API_KEY=
GROQ_API_KEY=
FIREWORKS_API_KEY=
TOGETHER_API_KEY=
GOOGLE_AI_API_KEY=
GOOGLE_VERTEX_CREDENTIALS=
MISTRAL_API_KEY=
XAI_API_KEY=

### Optional env vars

# Domain of the site
NEXT_PUBLIC_SITE_URL=

# Disabling API key and base URL input in the chat
NEXT_PUBLIC_NO_API_KEY_INPUT=
NEXT_PUBLIC_NO_BASE_URL_INPUT=

# Rate limit
RATE_LIMIT_MAX_REQUESTS=
RATE_LIMIT_WINDOW=

# Vercel/Upstash KV (short URLs, rate limiting)
KV_REST_API_URL=
KV_REST_API_TOKEN=

# Supabase (auth)
SUPABASE_URL=
SUPABASE_ANON_KEY=

# PostHog (analytics)
NEXT_PUBLIC_POSTHOG_KEY=
NEXT_PUBLIC_POSTHOG_HOST=
```

### 4. Start the development server

```
npm run dev
```

### 5. Build the web app

```
npm run build
```

## Customize

### Adding custom personas

1. Make sure [E2B CLI](https://e2b.dev/docs/cli/installation) is installed and you're logged in.

2. Add a new folder under [sandbox-templates/](sandbox-templates/)

3. Initialize a new template using E2B CLI:

    ```
    e2b template init
    ```

    This will create a new file called `e2b.Dockerfile`.

4. Adjust the `e2b.Dockerfile`

    Here's an example streamlit template:

    ```Dockerfile
    # You can use most Debian-based base images
    FROM python:3.19-slim

    RUN pip3 install --no-cache-dir streamlit pandas numpy matplotlib requests seaborn plotly

    # Copy the code to the container
    WORKDIR /home/user
    COPY . /home/user
    ```

5. Specify a custom start command in `e2b.toml`:

    ```toml
    start_cmd = "cd /home/user && streamlit run app.py"
    ```

6. Deploy the template with the E2B CLI

    ```
    e2b template build --name <template-name>
    ```

    After the build has finished, you should get the following message:

    ```
    ✅ Building sandbox template <template-id> <template-name> finished.
    ```

7. Open [lib/templates.json](lib/templates.json) in your code editor.

    Add your new template to the list. Here's an example for Streamlit:

    ```json
    "streamlit-developer": {
      "name": "Streamlit developer",
      "lib": [
        "streamlit",
        "pandas",
        "numpy",
        "matplotlib",
        "request",
        "seaborn",
        "plotly"
      ],
      "file": "app.py",
      "instructions": "A streamlit app that reloads automatically.",
      "port": 8501 // can be null
    },
    ```

    Provide a template id (as key), name, list of dependencies, entrypoint and a port (optional). You can also add additional instructions that will be given to the LLM.

4. Optionally, add a new logo under [public/thirdparty/templates](public/thirdparty/templates)

### Adding custom LLM models

1. Open [lib/models.json](lib/models.ts) in your code editor.

2. Add a new entry to the models list:

    ```json
    {
      "id": "mistral-large",
      "name": "Mistral Large",
      "provider": "Ollama",
      "providerId": "ollama"
    }
    ```

    Where id is the model id, name is the model name (visible in the UI), provider is the provider name and providerId is the provider tag (see [adding providers](#adding-custom-llm-providers) below).

### Adding custom LLM providers

1. Open [lib/models.ts](lib/models.ts) in your code editor.

2. Add a new entry to the `providerConfigs` list:

    Example for fireworks:

    ```ts
    fireworks: () => createOpenAI({ apiKey: apiKey || process.env.FIREWORKS_API_KEY, baseURL: baseURL || 'https://api.fireworks.ai/inference/v1' })(modelNameString),
    ```

3. Optionally, adjust the default structured output mode in the `getDefaultMode` function:

    ```ts
    if (providerId === 'fireworks') {
      return 'json'
    }
    ```

4. Optionally, add a new logo under [public/thirdparty/logos](public/thirdparty/logos)

## Contributing

As an open-source project, we welcome contributions from the community. If you are experiencing any bugs or want to add some improvements, please feel free to open an issue or pull request.
