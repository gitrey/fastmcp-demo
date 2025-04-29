# FastMCP Demo Application

This is a simple demonstration application using the FastMCP framework.

## Prerequisites

Before deploying, ensure you have the following:

1.  **Google Cloud SDK (`gcloud`):** Installed and authenticated.
    *   Install: https://cloud.google.com/sdk/docs/install
    *   Login: `gcloud auth login`
    *   Set Project: `gcloud config set project YOUR_PROJECT_ID` (Replace `YOUR_PROJECT_ID` with your actual Google Cloud project ID)
2.  **Enabled APIs:** Cloud Run API and Cloud Build API must be enabled for your project.
    ```bash
    gcloud services enable run.googleapis.com cloudbuild.googleapis.com
    ```
3.  **Billing Enabled:** Your Google Cloud project must have billing enabled.

## Project Structure

Ensure your project has the following files in the root directory:

*   `server.py`: Your main application code using FastMCP.
*   `requirements.txt`: Lists the Python dependencies.
    ```txt
    fastmcp
    asyncio
    uvicorn
    ```
*   `Procfile`: Tells Cloud Run how to start your web server. Since `uvicorn` is listed as a dependency, it's recommended to use it for running the application.
    ```Procfile
    web: uvicorn server:mcp --host 0.0.0.0 --port $PORT
    ```
    *(Note: This assumes your FastMCP instance is named `mcp` within your `server.py` file. Adjust `server:mcp` if your filename or instance name is different.)*

## Deployment to Cloud Run (from Source)

1.  **Navigate to the project directory:**
    Open your terminal and change to the directory containing `server.py`, `requirements.txt`, and `Procfile`.
    ```bash
    cd ~/fastmcp-demo
    ```

2.  **Run the deployment command:**
    ```bash
    gcloud run deploy fastmcp-demo \
        --source . \
        --region YOUR_REGION \
        --allow-unauthenticated
    ```
    *   Replace `fastmcp-demo` with your desired service name.
    *   Replace `YOUR_REGION` with the Google Cloud region you want to deploy to (e.g., `us-central1`, `europe-west1`).
    *   The `--allow-unauthenticated` flag makes the service publicly accessible. Remove this flag if you want to manage access using IAM.

3.  **Confirm Deployment:**
    The command will prompt you for confirmation. It will then trigger a Cloud Build process to build the container image and deploy it to Cloud Run.

4.  **Test Your Service:**
    Once the deployment is complete, `gcloud` will output the URL of your deployed service. 

    Install cmcp
    ```bash
    pip install cmcp
    ```

    List available tools:
    ```bash
    cmcp https://fastmcp-demo-658000000000.us-central1.run.app tools/list 
    ```

    Output:
    ```json
    { "meta": null,
    "nextCursor": null,
    "tools": [
    {
      "name": "add",
      "description": "Add two numbers",
      "inputSchema": {
        "properties": {
          "a": {
            "title": "A",
            "type": "integer"
          },
          "b": {
            "title": "B",
            "type": "integer"
          }
        },
        "required": [
          "a",
          "b"
        ],
        "title": "addArguments",
        "type": "object"
        }
        }
    ]}
    ```

    Call the tool:
    ```bash
    cmcp https://fastmcp-demo-658000000000.us-central1.run.app tools/call -d '{"name": "add", "arguments": {"a": 1, "b": 2}}'
    ```

    Output:
    ```json
    {
        "meta": null,
        "content": [
    {
      "type": "text",
      "text": "3",
      "annotations": null
    }
    ],
    "isError": false
    }
    ```