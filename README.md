# AI8 Platform: Agent Configuration and Usage Guide

This guide provides a comprehensive overview of configuring and using AI models on the AI8 platform.

## Agent Details Page

This page, accessed by clicking a agent's name on the dashboard, allows fine-tuning of its behavior.

### Editing Agent Settings

*   **Agent Name:**  A descriptive name for your agent.

*   **Agent Script:** The core logic (Python) defining your agent's behavior. Click "Edit Script" to access the editor.

    *   **Key Functions:**
        *   `query(model_name, conversation)`: Executes a query against a specified AI model with the current conversation history.
        *   `add(role, content, conversation)`: Adds a new message (user, assistant, or system) to the conversation.
        *   `attach(content, conversation, location="end")`: Attaches content to the beginning or end of the last message in the conversation.

    *   **Example Scripts:**
        *   **Chain of Thought Prompting:**
            ```python
            initial_response = query("gpt-4", conversation)
            follow_up = add("user", f"Explain your reasoning for that", conversation)
            final_response = query("gpt-4", conversation)
            ```

        *   **Model Switching Based on Conversation Length:**
            ```python
            if len(conversation) > 1000:
                response = query("gemini-1.5-flash-latest", conversation)  # Faster, less expensive model
            else:
                response = query("gpt-4", conversation) # More powerful model
            ```

*   **Plan:**
    *   **Decentralized:** Lower cost, community-supported inference.  Reliability may vary. Region selection is not available.
    *   **Dedicated:** Higher cost, dedicated resources for more reliable and consistent performance. Region selection is availible, but some regions may not have 100% uptime.

*   **Top P:** (Dedicated only) Controls the range of tokens considered by the agent when generating a response. A lower value makes the response more focused and deterministic, while a higher value allows for more diverse and creative outputs.

*   **Temperature:** (Dedicated only)  Influences the randomness of the agent's output.  Lower values (closer to 0) result in more predictable and conservative responses, while higher values (closer to 1) encourage more creative and unexpected outputs.

*   **Region:** (Dedicated only) Choose the geographical region for agent deployment.

*   **API Key:** Select the API key for authenticating requests to your agent. Create new keys on the [Trust Keys page](https://ai8labs.pages.dev/trust/keys).

*   **System Message:**  Sets the initial context for the agent. This message is prepended to every conversation.

*   **Stream:** Configure the agent to return a response stream. Note that the decentralized plan may not always support streaming. The response will be set as one chunk of the entire conversation. 

*   **Model ID:** A unique identifier for your agent.

*   **Try in Playground:** Opens the Playground for interactive testing.

*   **Attach Tools:**  Integrate external tools (see below).

*   **Save Changes:**  Saves your agent configuration.


### Available Models

The following table lists the available models, their availability (Decentralized, Dedicated, or Both), and their price in credits for each plan. Prices are estimates and may vary.

| Model                       | Availability   | Decentralized Price [(CPHQ)](#costdefinition) | Dedicated Price [(CPMT)](#costdefinition) |
|-----------------------------|----------------|---------------------------------------|-----------------------------------|
| `gpt-3.5-turbo`             | Decentralized  | 800                                   | N/A                                 |
| `gpt-4`                     | Decentralized  | 500                                   | N/A                               |
| `gpt-4-turbo`               | Decentralized  | 500                                   | N/A                               |
| `gpt-4o`                    | Both           | 1500                                  | 4000                              |
| `gpt-4o-mini`               | Both           | 50                                    | 250                               |
| `gemini-pro`                | Decentralized  | 100                                   | N/A                               |
| `gemini-1.0-pro`            | Decentralized  | 100                                   | N/A                               |
| `gemini-1.5-pro` `-latest`  | Both           | 500                                   | 2000                              |
| `gemini-1.5-flash` `-latest`| Both           | 25                                    | 100                               |
| `gemini-2-flash-exp`        | Dedicated      | N/A                                   | 10                                |
| `llama-3.1-8b-instruct`     | Decentralized  | 100                                   | N/A                               |
| `llama-3.2-11b-vision-instruct`| Decentralized  | 100                                | N/A                               |
| `llama-3-405b`              | Decentralized  | 100                                   | N/A                               |
* ***Use -latest when running on decentralized, use without -latest on dedicated.**


<a id="costdefinition"></a>
### How pricing is caluclated:

* CPHQ = credits per hundred queries.
* CPMT = credits per million tokens.
* 10 credits are 1 cent.

  Tool calls aren't billed. Decentralized model are billed per request, while dedicated models are billed per tokens. When using dedicated models, the tokens are calculated for **both input and output**, then credits are rounded to the nearest one hundreth of a credit. You may have a special offer that lowers the price of decentralized models.

### Agent Usage

*   **Requests:**  Number of requests made to the agent.
*   **Credits:** Credits consumed by the agent.


### Playground

The Playground allows you to interactively test your agent.

1.  **Endpoint URL:** The URL for making API requests to your agent.

2.  **Conversation Builder:**
    *   Add "user" and "assistant" messages to simulate a conversation.
    *   Edit the content within each message to craft specific prompts and responses.
    *   If you haven't set a default system message, you can set one here

3.  **Headers:**  You'll need to paste the API key into the `Authorization` header.  Ensure your API key is correct.

4.  **Run:** Sends the constructed conversation to your agent for processing.

5.  **Show Code:** Generates code snippets in various programming languages (Python, JavaScript, cURL, etc.) demonstrating how to make API calls to your agent with the current conversation.  This simplifies integration into your applications.

6.  **Playground Output:** Displays the raw JSON response from your agent.


### Attaching Tools

Tools extend the capabilities of your agents by allowing them to interact with external services and data.  Examples of tools might include:

*   **Search Engines:** Access real-time information.
*   **Calculators:** Perform mathematical operations.
*   **Code Execution:** Run code snippets.
*   **Database Access:** Retrieve data from databases.
*   **APIs:** Connect to external APIs.

The "Attach Tools" section lets you manage which tools are available to your agent. You can create these tools [here](https://app.ai8labs.pages.dev/toolbelt)


## API Interaction

The AI8 Platform offers a robust REST API for programmatic interaction with your agents. This allows seamless integration into your applications and workflows.  Here's a breakdown of how to interact with the API:

**1. Endpoint:**

The base URL for the API is `https://ai8-api.onrender.com`.  Agent-specific endpoints follow this structure:

```url
https://ai8-api.onrender.com/<version>/<agent_id>
```

NOTE: v1 has been removed. Only use the v2 API.

Replace `<agent_id>` with the unique identifier of your agent (found on the Agent Details page).  The Playground also displays the full endpoint URL for your convenience.

**2. Method:**

All agent queries use the `POST` method.

**3. Request Headers:**

*   **`Content-Type: application/json`**:  Indicates that the request body is in JSON format.  This is essential for proper parsing.
*   **`Authorization: <your_api_key>`**:  Authenticates your request. Replace `<your_api_key>` with your actual API key, which can be managed on the Trust/Keys page.  This header is crucial for security and access control.

**4. Request Body:**

The request body must be a JSON object with the following structure:

```json
{
  "conversation": [
    {"role": "system", "content": "You are a helpful assistant."},  // Optional system message
    {"role": "user", "content": "What is the capital of France?"},
    {"role": "assistant", "content": "Paris."}, // Optional previous turns
    // ... more conversation turns as needed
    {"role": "user", "content": "What is the weather like there?"} // The current user prompt
  ]
}
```

*   `conversation`: An array of message objects. Each message object has a `role` ("`system`", "`user`", or "`assistant`") and `content` (the text of the message). The conversation history provides context for the model. Include previous turns to maintain conversational state.

**5. Response:**

The API returns a JSON object containing the model's response and other relevant information. A successful response will have a 200 status code. Here's an example:
```json
    {
        "status": "success",
        "message": "Model query successful",
        "data": {
            "response": "The weather is warm, with temperatures around 25°C.",
            "agent_id": "your_agent_id",
            "credits_used": 5,
            "tool_calls": null,
            "follow_up_question": null
        },
        "error_details": null
        
    }
```

*   `status`: Indicates the outcome of the request. Either `success` or `error`
*   `message`: A human-readable description of the outcome.
*   `data`:
*   `response`: The agent's response to the user's prompt.
*   `model_id`: The ID of the agent used for the query.
*   `credits_used`: The number of credits consumed by the query.
*   `tool_calls`: An array of tool calls made during the query, if any. See below.
*   `follow_up_question`: An optional follow-up question to ask the user, if the agent couldn't find the neccessary parameters.


## Tool Calls and Responses

The AI8 platform allows you to enhance your agents by integrating external tools. This section details how tool calls work and how responses are formatted.

### Tool Integration Overview

Tools are external services or functions that your agent can interact with to access information or perform actions beyond its core capabilities.  The [Toolbelt page](https://app.ai8labs.pages.dev/toolbelt) allows you to create and manage tools, defining their parameters and descriptions. These tools can then be attached to your agents on the Agent Details page.

When a user interacts with a agent that has tools attached, the tools will **automatically** be called before executing your agent script. **We are working to add more ways to execute tools, like executing them within the response.** The platform handles the interaction with the tool and returns the result to your app.

### Tool Call Response Format

The response from a tool call is embedded within the main agent response as a JSON object with a specific structure. This allows your agent script to identify and process the tool's output.

```json
{
  "status": "success",
  "message": "Tool call initiated",
  "data": {
    "response": null,
    "agent_id": "your_agent_id",
    "credits_used": 0,
    "tool_calls": [
      {
        "toolName": "Get Order Details",
        "orderId": "12345"
      }
    ],
    "follow_up_question": null
  },
  "error_details": null
}
```

*   `content.toolCalls`: An array of tool call objects.  Each object represents a single call to a tool.

    *   `toolName`: A unique identifier for the tool call.
    *   `your_params`: The values for the params you defined


If parameters haven't been provided in the prompt, the response will be a follow-up question like this:

```json
{
  "status": "success",
  "message": "Follow-up question required",
  "data": {
    "response": null,
    "agent_id": "your_agent_id",
    "credits_used": 0,
    "tool_calls": null,
    "follow_up_question": {
      "question": "Could you tell me the order ID?"
    }
  },
  "error_details": null
}
```





## Managing Trust Keys and Agreements

The AI8 Platform uses API keys for authentication and requires acceptance of trust agreements for certain features. This section explains how to manage your API keys and view your agreements.

### API Keys (Trust Keys)

API keys are used to authenticate requests to your agents.  You can manage your API keys on the [Trust Keys page](https://ai8labs.pages.dev/trust/keys)

**Viewing Your Keys:**

The Keys page displays a table of your existing API keys, including their name, creation date, last used date, and the actual key value (only shown once before first use).

**Creating a New Key:**

1.  Click the "+ New API Key" button.
2.  Enter a descriptive name for your key in the modal.
3.  Click "Create".  **Important:** Copy the generated API key immediately.  You won't be able to view it again after the modal closes.

**Deleting a Key:**

Click the "Delete" button next to the key you want to remove.  This action is irreversible.

**Key Security:**

*   Treat your API keys like passwords.  Do not share them publicly or expose them in client-side code.
*   If a key is compromised, delete it immediately and create a new one.
*   Use descriptive key names to help you manage multiple keys.



### Trust Agreements

Certain features, such as decentralized agent deployment, require you to accept trust agreements.  You can view your accepted agreements on the [Trust/Agreements page](https://app.ai8labs.pages.dev/trust/agreements).

**Viewing Agreements:**

The Agreements page displays a table of your accepted agreements, including their name and the date you accepted them.  Clicking "View" next to an agreement will show the full text of the agreement.

**Accepting Agreements:**

Agreements are typically presented and accepted during specific actions, such as creating a decentralized model.  You'll be prompted to read and accept the agreement before proceeding.  Once accepted, the agreement will appear on the Agreements page.

**Types of Agreements:**

*   **Decentralized Inference Agreement:**  Acknowledges the risks and responsibilities associated with using decentralized inference.


## Logs Page

The Logs page provides a detailed view of your agent's usage history. You can access this page by clicking the "View Detailed Statistics" button on the Agent Details page.

### Features

*   **Agent Filter:** Filter logs by a specific agent.
*   **Search:** Search logs by model name or prompt content.
*   **Sortable Columns:** Sort logs by model name, timestamp, credits used, or response time.
*   **Pagination:** Navigate through logs using pagination controls.
*   **Detailed Log View:** Click the "View" button to see the full prompt, response, and metrics for a specific log entry.

### Log Details Modal

When you click the "View" button for a log entry, a modal will appear with the following information:

*   **Model Name:** The name of the model used for the request.
*   **Timestamp:** The date and time of the request.
*   **Prompt:** The full prompt sent to the model.
*   **Response:** The full response received from the model.
*   **Metrics:**
    *   **Response Time:** The time taken for the model to generate a response.
    *   **Tool Call Time:** The time taken to figure out which tool to call, if any.
    *   **Tool Parameterization Time:** The time taken to attach parameters to a tool call.

### Log Data

The logs table displays the following information for each log entry:

*   **Model Name:** The name of the model used for the request.
*   **Timestamp:** The date and time of the request.
*   **Credits:** The number of credits consumed by the request.
*   **Response Time:** The time taken for the model to generate a response.
*   **Tool Usage:** Indicates if a tool was used and the time taken for the tool call.
*   **Prompt:** A preview of the prompt sent to the model.
*   **View:** A button to view the full log details.


<hr><br><br>








This guide should provide a more detailed understanding of the AI8 platform's agent configuration and usage, but if you still need help, please feel free to [create a new issue.](https://github.com/AI8Labs/docs/issues/new)
