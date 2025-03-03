# Toolpendium: A Compendium of AI Tools

A comprehensive collection of 100,000+ LLM-compatible tools generated from the [APIs Guru] [OpenAPI Directory]. Toolpendium creates a semantic bridge between AI systems and the API ecosystem, enabling true Tool Augmented Generation [TAG].

## What is Toolpendium?

Toolpendium is a curated repository of over 100,000 Tool Handles that transform any AI system into a capable agent that can interact with thousands of web APIs. Each Tool Handle in this collection:

- Is completely self-contained with all necessary schemas
- Includes semantic documentation derived from API definitions
- Formats responses specifically to improve LLM comprehension
- Can be executed by a single [Tool Handle] runtime
- No per-tool code generation required

This collection represents a fundamental shift in AI-API integration: **treating tools as data rather than code**.

## Why This Matters

Current approaches to AI tool integration have critical scaling limitations:

1. **Custom code per API endpoint** doesn't scale beyond a few dozen tools
2. **Parameter matching alone** doesn't provide sufficient context for reliable API use
3. **Response parsing** is often left as an exercise for the LLM, leading to inconsistent results
4. **Semantic discovery** is typically limited to the tools hardcoded into a system

Toolpendium addresses these challenges through Tool Augmented Generation (TAG), a technique that:

- Stores tools in vector databases for semantic retrieval
- Provides complete context through rich, structured response templates
- Maintains a clean separation between tool definitions and execution mechanics
- Enables dynamic scaling to thousands of tools without overwhelming AI context windows

## How It Works

Toolpendium leverages multiple technologies to create a seamless bridge between AI intention and API execution:

### 1. Tool Form

[Tool Form] templates transform semantic parameters into precise protocol formats while maintaining security boundaries and reliable structure. These templates handle:

- Transformation between semantic and protocol spaces
- Format-specific encoding (JSON, multipart, markdown)
- Structured error handling
- Parameter validation and transformation

### 2. Tool Handle

[Tool Handle] provides a declarative execution model that separates tool definitions from credential management and execution mechanics. Each Tool Handle includes:

- Complete parameter schemas
- Request formation templates
- Response processing rules
- Security boundaries

### 3. Tool API

The internal `tool-api` package generates Tool Handles automatically from OpenAPI specifications. It:

- Tree-shakes schemas to create self-contained definitions
- Preserves API documentation within the tools
- Generates semantic response templates
- Ensures consistent parameter validation

### 4. Response Engineering

Each Tool Handle contains carefully designed response templates that:

- Format API responses into mixed markdown/JSON for optimal LLM comprehension
- Weave API documentation directly into responses
- Maintain structured representation of complex data
- Guide LLMs toward reliable interpretation

## Example

Let's see how Tool Handle's response engineering transforms a typical API response into something LLMs can reliably understand and process.

### Typical API Response

Consider this raw JSON response from the GitHub Issues API:

```json
{
  "id": 1,
  "node_id": "MDU6SXNzdWUx",
  "url": "https://api.github.com/repos/octocat/Hello-World/issues/1347",
  "repository_url": "https://api.github.com/repos/octocat/Hello-World",
  "html_url": "https://github.com/octocat/Hello-World/issues/1347",
  "number": 1347,
  "state": "open",
  "title": "Found a bug",
  "body": "I'm having a problem with this.",
  "user": {
    "login": "octocat",
    "id": 1,
    "avatar_url": "https://github.com/images/error/octocat_happy.gif",
    "node_id": "MDQ6VXNlcjE="
    // ... more user fields
  },
  "labels": [
    {
      "id": 208045946,
      "name": "bug",
      "color": "f29513"
    }
  ],
  "created_at": "2011-04-22T13:33:48Z",
  "updated_at": "2011-04-22T13:33:48Z"
  // ... more issue fields
}
```

While this response contains all necessary data, it presents several challenges for LLMs:

1. **Missing Context**: No explanation of what fields like `node_id` mean or how they relate to other entities
2. **Identifier Ambiguity**: Multiple `id` fields across different objects with different meanings
3. **Undifferentiated Structure**: No clear indication of which fields are most important
4. **Lack of Documentation**: No descriptions of what fields represent or how they should be used

### Response-Engineered Output

Here's an excerpt from how an auto-generated Tool Handle transforms this response:

````markdown
# Issue

Issues are a great way to keep track of tasks, enhancements, and bugs for your projects.

**Key properties:**

- **number**: Number uniquely identifying the issue within its repository
- **title**: Title of the issue
- **state**: State of the issue; either 'open' or 'closed'
- **body**: Contents of the issue
- **user**: A GitHub user.
  - **login**: The user's username
  - **id**: The unique identifier for this user
  - **avatar_url**: URL to the user's profile image
- **labels**: Labels to associate with this issue
  - **name**: The name of the label
  - **color**: The color code for the label
- **created_at**: When the issue was created
- **updated_at**: When the issue was last updated

```json
{
  "url": "https://api.github.com/repos/toolcog/toolpendium/issues/1",
  "repository_url": "https://api.github.com/repos/toolcog/toolpendium",
  "number": 1,
  "title": "Add a comprehensive README",
  "state": "open",
  "user": {
    "login": "c9r",
    "id": 1003343,
    "avatar_url": "https://avatars.githubusercontent.com/u/1003343?v=4"
    // Additional fields omitted for brevity
  },
  "labels": [
    {
      "name": "documentation",
      "color": "0075ca"
    }
  ],
  "created_at": "2025-03-03T18:10:25Z",
  "updated_at": "2025-03-03T18:10:25Z",
  "body": "Here's a straw man outline:\n\n- The Big Picture\n- Key Components..."
}
```
````

This transformation provides three critical layers:

1. **Semantic Context**: The document begins with a description of what an "Issue" is
2. **Field Documentation**: Each field is described with its purpose and relationship to other fields
3. **Actual Data**: The raw API response is included as a JSON code block

The LLM receives both the structured documentation and the raw data, allowing it to:

- Understand the meaning and importance of each field
- Access the complete response data
- Learn the API structure for future interactions
- Focus on the most relevant information for the user's task

### The Tool Handle Template

This rich response is generated by a Tool Form template that embeds documentation directly in the response structure:

```json
{
  "$encode": "markdown",
  "$block": [
    { "$h1": "Issue" },
    "Issues are a great way to keep track of tasks, enhancements, and bugs for your projects.",
    "**Key properties:**",
    {
      "$ul": [
        "**number**: Number uniquely identifying the issue within its repository",
        "**title**: Title of the issue",
        "**state**: State of the issue; either 'open' or 'closed'",
        "**body**: Contents of the issue",
        [
          "**user**: A GitHub user.",
          {
            "$ul": [
              "**login**: The user's username",
              "**id**: The unique identifier for this user",
              "**avatar_url**: URL to the user's profile image"
            ]
          }
        ]
        // Additional fields and nested objects with documentation
      ]
    },
    {
      "$lang": "json",
      "$code": {
        "$encode": "json",
        "$indent": true,
        "$content": { "$": "$.body" }
      }
    }
  ]
}
```

The complete Tool Handle templates include comprehensive documentation for all API fields, providing a complete teaching reference. This approach turns every API response into a learning opportunity for the LLM - it doesn't just get data, it gets context about what that data means and how it fits into the API's domain model.

While the baseline templates generated by Toolpendium are comprehensive (sometimes showing hundreds of potential fields), they're designed to be starting points that AI can optimize over time - focusing on the most relevant fields while still maintaining the complete knowledge of what's available.

### AI-Enhanced Tool Handles

While the auto-generated templates provide a solid foundation, AI enhancement transforms them into precision instruments for reliable LLM tool use. Here's what happens when a GitHub issue response gets enhanced by AI optimization:

````markdown
# GitHub Issue

This is a open issue #42 titled **Add AI optimization examples** in the repository toolcog/toolpendium

## Labels

- enhancement
- documentation

## Description

> We should add examples of AI-optimized Tool Handles to demonstrate how the baseline templates can be enhanced for better LLM reliability.
>
> The examples should show:
>
> ```javascript
> // Before optimization
> const result = await callTool(tool, params);
> console.log(result); // Overwhelming, unfocused response
>
> // After optimization
> const result = await callTool(optimizedTool, params);
> console.log(result); // Clear, actionable, context-rich response
> ```

## Metadata

Created by **toolcog-dev** on 2024-04-15 • Assigned to: `c9r`, `sara-dev` • Milestone: **v1.0-beta** (8 open issues)

## Actions

To respond to this issue, you can:

- **Comment**: Add information or ask questions
- **Close**: Mark the issue as resolved or invalid
- **Review PR**: This issue has an associated pull request at https://github.com/toolcog/toolpendium/pull/43

## Code Snippets

```javascript
// Before optimization
const result = await callTool(tool, params);
console.log(result); // Overwhelming, unfocused response

// After optimization
const result = await callTool(optimizedTool, params);
console.log(result); // Clear, actionable, context-rich response
```

## Related Endpoints

- Create a comment: `POST /repos/{owner}/{repo}/issues/{issue_number}/comments`
- List comments: `GET /repos/{owner}/{repo}/issues/{issue_number}/comments`
- Update status: `PATCH /repos/{owner}/{repo}/issues/{issue_number}`

## Raw Response

```json
{
  "url": "https://api.github.com/repos/toolcog/toolpendium/issues/42",
  "number": 42,
  "title": "Add AI optimization examples",
  "state": "open"
  // Full response data...
}
```
````

Notice how this enhanced response transforms the data in multiple dimensions:

1. **Prioritizes Relevance**: Surfaces the most important fields (title, state, creator) immediately
2. **Adds Contextual Examples**: Shows related API endpoints to guide further actions
3. **Provides Decision Support**: Lists appropriate actions based on issue state
4. **Improves Navigation**: Creates clear sections with hierarchical organization
5. **Enhances Readability**: Formats dates, transforms URLs, and extracts code snippets
6. **Establishes Relationships**: Shows connections between issues, repositories, and users

This transformation is powered by an AI-enhanced Tool Handle template that looks like this:

````json
{
  "$encode": "markdown",
  "$block": [
    { "$h1": "GitHub Issue" },
    "This is a {{state}} issue #{{number}} titled **{{title}}** in the repository {{repository_url|replace:'https://api.github.com/repos/':''}}",

    // Context-rich field documentation with prioritization
    {
      "$when": "$.labels.*",
      "$block": ["## Labels", { "$ul": { "$": "labels.*.name" } }]
    },

    // Primary content with rich formatting
    "## Description",
    { "$blockquote": { "$": "body" } },

    // Actionable metadata in readable format
    "## Metadata",
    {
      "$inline": [
        "Created by ",
        { "$strong": "{{user.login}}" },
        " on {{created_at|date:'YYYY-MM-DD'}}",
        {
          "$when": "$.assignees.*",
          "$inline": [
            " • Assigned to: ",
            {
              "$each": "$.assignees.*",
              "$as": "assignee",
              "$join": ", ",
              "$value": { "$code": "{{assignee.login}}" }
            }
          ]
        },
        {
          "$when": "$.milestone",
          "$inline": [
            " • Milestone: ",
            { "$strong": "{{milestone.title}}" },
            " ({{milestone.open_issues}} open issues)"
          ]
        }
      ]
    },

    // Decision guidance for LLMs
    "## Actions",
    {
      "$p": [
        "To respond to this issue, you can:",
        {
          "$ul": [
            "**Comment**: Add information or ask questions",
            {
              "$when": "$.state == 'open'",
              "$inline": ["**Close**: Mark the issue as resolved or invalid"]
            },
            {
              "$when": "$.state == 'closed'",
              "$inline": [
                "**Reopen**: Mark the issue as requiring further attention"
              ]
            },
            {
              "$when": "$.pull_request",
              "$inline": [
                "**Review PR**: This issue has an associated pull request at {{pull_request.html_url}}"
              ]
            }
          ]
        }
      ]
    },

    // Example code snippets that demonstrate usage
    {
      "$when": "$.body|contains:'```'",
      "$block": ["## Code Snippets", { "$": "body|extractCodeBlocks" }]
    },

    // Semantic API relationship map
    "## Related Endpoints",
    {
      "$ul": [
        {
          "$inline": [
            "Create a comment: ",
            {
              "$code": "POST /repos/{owner}/{repo}/issues/{issue_number}/comments"
            }
          ]
        },
        {
          "$inline": [
            "List comments: ",
            {
              "$code": "GET /repos/{owner}/{repo}/issues/{issue_number}/comments"
            }
          ]
        },
        {
          "$inline": [
            "Update status: ",
            { "$code": "PATCH /repos/{owner}/{repo}/issues/{issue_number}" }
          ]
        }
      ]
    },

    // Original response data with schema
    "## Raw Response",
    {
      "$lang": "json",
      "$code": {
        "$encode": "json",
        "$indent": true,
        "$content": { "$": "$" }
      }
    }
  ]
}
````

The template demonstrates advanced Tool Form features that make API responses dramatically more useful for LLMs:

- **Conditional Sections**: Only shows labels, assignees, or PRs when they exist
- **Data Transformations**: Formats dates, extracts code blocks, and simplifies URLs
- **Decision Logic**: Changes available actions based on issue state
- **Smart Formatting**: Uses appropriate styling for different content types
- **Contextual Integration**: Relates the issue to other possible API operations

Now imagine this level of transformation applied to 100,000+ tools across thousands of APIs. Each API response becomes not just data, but a rich interface that guides LLMs to understand, reason about, and act upon the information effectively. This is the true power of Tool Augmented Generation - the ability to dynamically connect AI to any API while maintaining contextual understanding and operational reliability.

## Getting Started

### Prerequisites

- Node.js 20+
- Access to a vector database for semantic tool retrieval (optional)

### Installation

```bash
# Clone the repository
git clone https://github.com/toolcog/toolpendium
cd toolpendium

# Install dependencies
npm install
```

### Using Toolpendium

There are several ways to use Toolpendium:

#### 1. Direct Tool Access

Access tools directly as JSON files from the repository:

```javascript
import { readFile } from "fs/promises";
import { executeToolHandle } from "@toolcog/tool-handle";

// Load a specific tool
const toolJson = await readFile(
  "./http/github.com/api.github.com/1.1.4/issues/create.json",
);
const tool = JSON.parse(toolJson);

// Execute the tool
const result = await executeToolHandle(tool, {
  owner: "toolcog",
  repo: "toolpendium",
  body: {
    title: "Test Issue",
    body: "This is a test issue created using a Toolpendium Tool Handle",
    labels: ["test", "documentation"],
  },
});
```

#### 2. Vector Database Integration

For true Tool Augmented Generation, index tools in a vector database:

```javascript
import { glob } from "glob";
import { readFile } from "fs/promises";
import { createEmbedding } from "./your-embedding-service";
import { storeInVectorDb } from "./your-vector-db";

// Index all tools
async function indexTools() {
  const toolPaths = await glob("./tool-handle/**/*.json");

  for (const path of toolPaths) {
    const tool = JSON.parse(await readFile(path, "utf-8"));

    // Create embedding from tool name, description and parameters
    const text = `${tool.name} ${tool.description} ${JSON.stringify(tool.parameters)}`;
    const embedding = await createEmbedding(text);

    // Store in vector database with the full tool as metadata
    await storeInVectorDb(embedding, tool);
  }
}
```

#### 3. TAG Pipeline

Implement a complete TAG pipeline with user query processing:

```javascript
import { similaritySearch } from "./your-vector-db";
import { executeToolHandle } from "@toolcog/tool-handle";

async function processQuery(userQuery) {
  // 1. Find relevant tools through vector similarity
  const relevantTools = await similaritySearch(userQuery, 3);

  // 2. Present tools to the LLM along with the query
  const llmResponse = await callLLM({
    prompt: userQuery,
    tools: relevantTools,
  });

  // 3. If the LLM chooses to use a tool, execute it
  if (llmResponse.toolCall) {
    const result = await executeToolHandle(
      llmResponse.toolCall.tool,
      llmResponse.toolCall.parameters,
    );

    // 4. Return the result to the LLM for interpretation
    return await callLLM({
      prompt: userQuery,
      conversation: [
        // previous messages
        { role: "assistant", content: llmResponse.content },
        { role: "tool", name: llmResponse.toolCall.tool.name, content: result },
      ],
    });
  }

  return llmResponse;
}
```

## Generating Tool Handles

Toolpendium provides multiple ways to generate Tool Handles from OpenAPI specifications:

### Using the Generate Script

The simplest way to generate Tool Handles for all APIs in the repository is with the included script:

```bash
npm run generate
```

This script uses the `tool-api` package to:

1. Process all OpenAPI files in the repository
2. Generate Tool Handles for each operation
3. Output them to the `./tool-handle` directory

### Generating Tools for a Single API

For more control over the generation process, use the `tool-api generate` command directly:

```bash
npx tool-api generate ./path/to/openapi.json --output-dir ./my-tools --format json
```

This command supports several options:

- `--output-dir, -o`: Directory where Tool Handles will be written
- `--format`: Output format (`json` or `yaml`)
- `--server-url`: Override the server URL for all operations
- `--include`: Only generate Tool Handles for operations matching this pattern
- `--exclude`: Skip operations matching this pattern
- `--tags`: Only include operations with specific tags (comma-separated)
- `--verbose, -v`: Print detailed information during processing
- `--dry-run`: Preview generation without writing files
- `--skip-existing`: Skip generation if file already exists

### Bulk Generation

For processing multiple OpenAPI files, the batch command provides efficient parallel processing:

```bash
npx tool-api batch --format json --output-dir ./my-tools 'path/to/openapi/**/*.{json,yaml}'
```

This is the command used to generate the entire Toolpendium collection from the APIs Guru OpenAPI Directory.

### AI Optimization

Once you've generated Tool Handles, you can further optimize them using AI to:

- Enhance documentation where it's incomplete
- Refine response templates for better LLM comprehension
- Add examples for complex parameters
- Normalize naming conventions across different APIs

The response-engineered templates are designed as a starting point that can be continuously improved through usage data and AI-driven optimization.

## Project Structure

```
toolpendium/
├── http/                   # Original OpenAPI specifications
│   └── github.com/         # Organized by API provider
│       └── api.github.com/ # Version-specific API specs
│           └── 1.1.4/      # API version
│               └── openapi.json  # Full OpenAPI specification
├── tool-handle/            # Generated Tool Handles
│   └── http/               # Mirrors the structure of http/
│       └── github.com/
│           └── api.github.com/
│               └── 1.1.4/
│                   └── issues/
│                       ├── list.json    # Tool Handle for listing issues
│                       └── create.json  # Tool Handle for creating issues
└── package.json           # Project configuration
```

## Contributing

We welcome contributions to enhance the toolpendium and improve tool reliability:

1. **Response Template Improvements**: Help optimize response templates for better LLM comprehension
2. **Documentation Enhancement**: Fill gaps in API documentation
3. **Tool Validation**: Test tools and report any issues
4. **New API Integrations**: Add OpenAPI specs for APIs not yet in the collection

Please see [CONTRIBUTING.md][Contributing Guide] for details.

## Related Projects

Toolpendium is part of a larger ecosystem:

- **[Tool Form]**: Structural transformation engine
- **[Tool Handle]**: Universal tool executor
- **[HTTP Handle]**: HTTP protocol handler

## The Vision

Toolpendium represents a significant step toward a future where AI systems can seamlessly interact with the entire digital ecosystem. By treating tools as data rather than code, we enable:

1. **Semantic Discovery**: Finding exactly the right tool for any task
2. **Scale Without Compromise**: Supporting thousands of tools without overwhelming LLM context
3. **Continuous Improvement**: Enhancing tools based on usage patterns
4. **Security By Design**: Maintaining clear boundaries between intention and execution

The ultimate goal is to create an AI-API boundary layer that lets AI systems reliably interact with any service while maintaining proper governance and control.

## License

MIT © Tool Cognition Inc.

[TAG]: https://github.com/toolcog/tool-handle#readme
[Tool Handle]: https://github.com/toolcog/tool-handle/tree/main/packages/tool-handle#readme
[HTTP Handle]: https://github.com/toolcog/tool-handle/tree/main/packages/http#readme
[Tool Form]: https://github.com/toolcog/tool-form#readme
[Contributing Guide]: CONTRIBUTING.md
[APIs Guru]: https://apis.guru
[OpenAPI Directory]: https://github.com/APIs-guru/openapi-directory
