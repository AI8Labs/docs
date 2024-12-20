# AI8 Platform: Model Configuration and Usage Guide

This guide provides a comprehensive overview of configuring and using AI models on the AI8 platform.

## Model Details Page

This page, accessed by clicking a model's name on the dashboard, allows fine-tuning of model behavior.

### Editing Model Settings

*   **Model Name:**  A descriptive name for your model.

*   **Model Script:** The core logic (Python) defining your model's behavior. Click "Edit Script" to access the editor.

    *   **Key Functions:**
        *   `query(model_name, conversation)`: Executes a query against a specified AI model with the current conversation history.
        *   `add(role, content, conversation)`: Adds a new message (user, assistant, or system) to the conversation.
        *   `attach(content, conversation, location="end")`: Attaches content to the beginning or end of the last message in the conversation.

    *   **Available Models & Pricing (Credits):**  The following models are available for use in your scripts. Pricing is per request and subject to change.  Contact us for the most up-to-date pricing.
        *   `gpt-3.5-turbo`
        *   `gpt-4`
        *   `gpt-4-turbo`
        *   `gpt-4o`
        *   `gpt-4o-mini`
        *   `gemini-pro`
        *   `gemini-1.0-pro`
        *   `gemini-1.5-pro-latest`
        *   `gemini-1.5-flash-latest`
        *   `gemini-2-flash-exp`
        *   `llama-3.1-8b-instruct`
        *   `llama-3.2-11b-vision-instruct`
        *   `llama-3-405b`

    *   **Example Scripts:**
        *   **Chain of Thought Prompting:**
            ```python
            initial_response = query("gpt-4", conversation)
            follow_up = add("user", f"Explain your reasoning for {initial_response}", conversation)
            final_response = query("gpt-4", conversation)
            ```

        *   **Model Switching Based on Conversation Length:**
            ```python
            if len(conversation) > 5:
                response = query("gemini-1.5-flash-latest", conversation)  # Faster, less expensive model
            else:
                response = query("gpt-4", conversation) # More powerful model
            ```

        *   **Dynamic System Message:**
            ```python
            system_message = f"You are an expert in {user_specified_topic}"
            conversation = add("system", system_message, conversation)
            response = query("gpt-4o", conversation)
            ```

*   **Plan:**
    *   **Decentralized:** Lower cost, community-supported inference.  Reliability may vary. Region selection is not available.
    *   **Dedicated:** Higher cost, dedicated resources for more reliable and consistent performance.

*   **Top P:** (Dedicated only) Controls the range of tokens considered by the model when generating a response. A lower value makes the response more focused and deterministic, while a higher value allows for more diverse and creative outputs.

*   **Temperature:** (Dedicated only)  Influences the randomness of the model's output.  Lower values (closer to 0) result in more predictable and conservative responses, while higher values (closer to 1) encourage more creative and unexpected outputs.

*   **Region:** (Dedicated only) Choose the geographical region for model deployment.

*   **API Key:** Select the API key for authenticating requests to your model. Create new keys on the Trust/Keys page.

*   **System Message:**  Sets the initial context for the model. This message is prepended to every conversation.

*   **Model ID:** A unique identifier for your model.

*   **Try in Playground:** Opens the Playground for interactive testing.

*   **Attach Tools:**  Integrate external tools (see below).

*   **Save Changes:**  Saves your model configuration.


### Model Usage

*   **Requests:**  Number of requests made to the model.
*   **Credits:** Credits consumed by the model.


### Playground - Detailed Guide

The Playground allows you to interactively test your model.

1.  **Endpoint URL:** The URL for making API requests to your model.

2.  **Conversation Builder:**
    *   Add "user" and "assistant" messages to simulate a conversation.
    *   Edit the content within each message to craft specific prompts and responses.
    *   The first message should typically be a "system" message to set the context.

3.  **Headers:**  `Authorization` (your API key) and `Content-Type` (application/json) are pre-filled.  Ensure your API key is correct.

4.  **Run:** Sends the constructed conversation to your model for processing.

5.  **Show Code:** Generates code snippets in various programming languages (Python, JavaScript, cURL, etc.) demonstrating how to make API calls to your model with the current conversation.  This simplifies integration into your applications.

6.  **Playground Output:** Displays the raw JSON response from your model.


### Attaching Tools

Tools extend the capabilities of your models by allowing them to interact with external services and data.  Examples of tools might include:

*   **Search Engines:** Access real-time information.
*   **Calculators:** Perform mathematical operations.
*   **Code Execution:** Run code snippets.
*   **Database Access:** Retrieve data from databases.
*   **APIs:** Connect to external APIs.

The "Attach Tools" section lets you manage which tools are available to your model.  More documentation on specific tools and their integration will be provided separately.


## API Interaction

The AI8 Platform offers a robust REST API for programmatic interaction with your models. This allows seamless integration into your applications and workflows.  Here's a breakdown of how to interact with the API:

**1. Endpoint:**

The base URL for the API is `https://ai8-api.onrender.com`.  Model-specific endpoints follow this structure:

```url
https://ai8-api.onrender.com/v2/<model_id>
```

Replace `<model_id>` with the unique identifier of your model (found on the Model Details page).  The Playground also displays the full endpoint URL for your convenience.

**2. Method:**

All model queries use the `POST` method.

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
        "message": {
            "role": "assistant",
            "content": "The weather is warm, with temperatures around 25°C."
        },
        "model_id": "your_model_id",
        "credits_used": 5
    }
```
*   `message`: The model's response. It includes the role ("assistant") and the content of the response.
*   `model_id`: The unique identifier of the model used for this request.
*   `credits_used`: The number of credits consumed for this request.




### Key Points

*   Secure script execution.
*   Costs vary by plan and model usage.
*   Experiment and refine your models using the Playground.
*   Integrate models into your applications via the API.


## Tool Calls and Responses

The AI8 platform allows you to enhance your models by integrating external tools. This section details how tool calls work and how responses are formatted.

### Tool Integration Overview

Tools are external services or functions that your model can interact with to access information or perform actions beyond its core capabilities.  The [Toolbelt page](https://app.ai8labs.pages.dev/toolbelt) allows you to create and manage tools, defining their parameters and descriptions. These tools can then be attached to your models on the Model Details page.

When a user interacts with a model that has tools attached, the tools will **automatically** be called before executing your model script. **We are working to add more ways to execute tools, like executing them within the response** The platform handles the interaction with the tool and returns the result to your app.

### Tool Call Response Format

The response from a tool call is embedded within the main model response as a JSON object with a specific structure. This allows your model script to identify and process the tool's output.

```json
{
  "message": {
        "role": "toolcall.initiate",
        "content": 
        {
            "toolCalls": [
                {
                    "toolName": "Get Order Details", 
                    "orderId": "12345"
                }, 
                {
                    "toolName": "Get Customer Details",
                    "customerId": "67890"}
            ]
        }
    }
}
```

If parameters haven't been provided in the prompt, the response will be a follow-up question like this:

```json
{
    "message": {
        "role": "toolCall.question",
        "content": {
            "question": "Could you tell me the order ID?"
        }
    }
}
```


*   `role`:  Always set to `"toolCall.*"` to distinguish it from regular assistant messages.
*   `content.toolCalls`: An array of tool call objects.  Each object represents a single call to a tool.

    *   `toolName`: A unique identifier for the tool call.
    *   `your_params`: The values for the params you defined



## Managing Trust Keys and Agreements

The AI8 Platform uses API keys for authentication and requires acceptance of trust agreements for certain features. This section explains how to manage your API keys and view your agreements.

### API Keys (Trust Keys)

API keys are used to authenticate requests to your models.  You can manage your API keys on the Trust/Keys page (`keys.html`, `keys.js`).

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

Certain features, such as decentralized model deployment, require you to accept trust agreements.  You can view your accepted agreements on the Trust/Agreements page (`agreements.html`, `agreements.js`).

**Viewing Agreements:**

The Agreements page displays a table of your accepted agreements, including their name and the date you accepted them.  Clicking "View" next to an agreement will show the full text of the agreement.

**Accepting Agreements:**

Agreements are typically presented and accepted during specific actions, such as creating a decentralized model.  You'll be prompted to read and accept the agreement before proceeding.  Once accepted, the agreement will appear on the Agreements page.

**Types of Agreements:**

*   **Decentralized Inference Agreement:**  Acknowledges the risks and responsibilities associated with using decentralized inference.

<hr><br><br>



This guide should provide a more detailed understanding of the AI8 platform's model configuration and usage, but if you still need help, please feel free to [create a new issue.](https://github.com/AI8Labs/docs/issues/new)
