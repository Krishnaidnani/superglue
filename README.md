
<p align="center">
  <img src="https://github.com/user-attachments/assets/be0e65d4-dcd8-4133-9841-b08799e087e7" width="350" alt="superglue_logo_white">
</p>

<h2 align="center">API connector that writes its own code 🍯</h2>

superglue allows you to connect to any API/data source and get the data you want in the format you need. It’s an open source proxy server which sits between you and your target APIs. Thus, you can easily deploy it into your own infra.

Here's how it works: You define your desired data schema and provide basic instructions about an API endpoint (like "get all issues from jira"). Superglue then does the following:

- Automatically generates the API configuration by analyzing API docs.
- Handles pagination, authentication, and error retries.
- Transforms response data into the exact schema you want using JSONata expressions.
- Validates that all data coming through follows that schema, and fixes transformations when they break.



<div align="center">

[![GitHub](https://img.shields.io/github/license/superglue-ai/superglue?style=flat-square)](https://github.com/superglue-ai/superglue/blob/main/LICENSE)
[![Y Combinator](https://img.shields.io/badge/Y%20Combinator-W25-orange?style=flat-square)](https://www.ycombinator.com/companies/superglue)
[![Client SDK](https://img.shields.io/npm/v/@superglue/client?style=flat-square&logo=npm)](https://www.npmjs.com/package/@superglue/client)
[![Docker](https://img.shields.io/docker/pulls/superglueai/superglue?style=flat-square&logo=Docker)](https://hub.docker.com/r/superglueai/superglue)
[![Twitter Adina](https://img.shields.io/twitter/follow/adinagoerres?style=flat-square&logo=X)](https://twitter.com/adinagoerres)
[![Twitter Stefan](https://img.shields.io/twitter/follow/sfaistenauer?style=flat-square&logo=X)](https://twitter.com/sfaistenauer)


</div>

If you’re spending a lot of time writing code connecting to weird APIs, fumbling with custom fields in foreign language ERPs, mapping JSONs, extracting data from compressed CSVs sitting on FTP servers, and making sure your integrations don’t break when something unexpected comes through, superglue might be for you.

```mermaid
flowchart LR
    subgraph Input[data sources]
        A1[APIs]
        A2[files]
        A3[legacy systems]
    end

    subgraph Process[data transformation]
        T1[superglue engine]
    end

    subgraph Output[destination]
        D1[your system]
    end

    Input --> Process
    Process --> Output

    %% Styling
    classDef sourceStyle fill:#f9f,stroke:#333,stroke-width:2px
    classDef processStyle fill:#bbf,stroke:#333,stroke-width:2px
    classDef outputStyle fill:#bfb,stroke:#333,stroke-width:2px

    class Input sourceStyle
    class Process processStyle
    class Output outputStyle
```

## quick start
### hosted version

1. Sign up for early access to the hosted version of superglue at [superglue.cloud](https://superglue.cloud)

2. Install the superglue js/ts client:
```bash
npm install @superglue/client
```

3. Configure your first api call:
```javascript
import { SuperglueClient } from "@superglue/client";

const superglue = new SuperglueClient({
  apiKey: "************"
});

const config = {
  urlHost: "https://futuramaapi.com",
  urlPath: "/graphql",
  instruction: "get all characters from the show",
  responseSchema: {
    type: "object",
    properties: {
      characters: {
        type: "array",  
        items: {
          type: "object",
          properties: {
            name: { type: "string" },
            species: { type: "string", description: "lowercased" }
          }
        }
      }
    }
  }
};

const result = await superglue.call({endpoint: config});
console.log(JSON.stringify(result.data, null, 2));

/*
output:
{
  "characters": [
    {
      "name": "Phillip J. Fry",
      "species": "human"
    },
    ...
  ]
}
*/
```

### self-hosted version

Run your own instance of superglue using Docker:

1. Pull the Docker image:
```bash
docker pull superglueai/superglue
```

2. Create a `.env` file with the following configuration:
```env
# Server Configuration

# Port to run the superglue server
GRAPHQL_PORT=3000

# Port to run the web dashboard 
WEB_PORT=3001

# Endpoint the web interface will connect to
GRAPHQL_ENDPOINT=http://localhost:3000

# Authentication token for API access
AUTH_TOKEN=your-auth-token

# Datastore Configuration. File is a simple persistent option. Memory is fast but not persistent. Redis is distributed and can be configured to be persistent.
DATASTORE_TYPE=file or memory or redis

# if file
STORAGE_DIR=/data

# if redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_USERNAME=default
REDIS_PASSWORD=secret

# OpenAI Configuration
OPENAI_API_KEY=sk-...
# OpenAI model to use. We recommend gpt-4o-2024-11-20
OPENAI_MODEL=gpt-4o-2024-11-20
# Optional: Set a custom OpenAI API URL (for self-hosted models or proxies)
# OPENAI_API_BASE_URL=https://your-custom-endpoint/v1
```

3. Start the server:
```bash
docker run -d \
  --name superglue \
  --env-file .env \
  -p 3000:3000 \
  -p 3001:3001 \
  superglueai/superglue
```

4. Verify the installation:
```bash
curl http://localhost:3000/health
> OK

# or open http://localhost:3000/?token=your-auth-token
```

5. Open the dashboard to create your first configuration:
```bash
http://localhost:3001/
```

6. run your first call:
```bash
npm install @superglue/client
```

```javascript
import { SuperglueClient } from "@superglue/client";

const superglue = new SuperglueClient({
  endpoint: "http://localhost:3000",
  apiKey: "your-auth-token"
});

// either via config object
const config = {
  urlHost: "https://futuramaapi.com",
  urlPath: "/graphql",
  instruction: "get all characters from the show",
};

const result = await superglue.call({endpoint: config});

// or via the api id if you have already created the endpoint
const result2 = await superglue.call({id: "futurama-api"});

console.log(JSON.stringify(result.data, null, 2));
```


## key features

- **LLM-Powered Data Mapping**: Automatically generate data transformations using large language models
- **API Proxy**: Intercept and transform API responses in real-time with minimal added latency
- **File Processing**: Handle various file formats (CSV, JSON, XML) with automatic decompression
- **Schema Validation**: Ensure data compliance with your specified schemas
- **Flexible Authentication**: Support for various auth methods including header auth, api keys, oauth, and more
- **Smart Pagination**: Handle different pagination styles automatically
- **Caching & Retry Logic**: Built-in caching and configurable retry strategies

## 📖 Documentation

For detailed documentation, visit [docs.superglue.cloud](https://docs.superglue.cloud).

## 🤝 contributing
We love contributions! Feel free to open issues for bugs or feature requests.

[//]: # (To contribute to the docs, check out the /docs folder.)

## license

superglue is GPL licensed. The superglue client SDKs are MIT licensed. See [LICENSE](LICENSE) for details.

## 🙋‍♂️ support

- 💬 Discord: [Join our community](https://discord.gg/vUKnuhHtfW)
- 🐛 Issues: [GitHub Issues](https://github.com/superglue-ai/superglue/issues)

[![Twitter](https://img.shields.io/twitter/follow/superglue_d?style=social)](https://twitter.com/superglue_d)

