# OopsBot MCP Server

## Overview

The **OopsBot MCP Server** is a Model Context Protocol (MCP) implementation that provides AI agents with comprehensive test case generation and management capabilities. Transform your product requirements into comprehensive test cases using AI-powered automation.

## Key Features

- **🤖 AI-Powered Test Generation**: Generate comprehensive test cases from product requirements and user stories
- **📄 Document Processing**: Upload and process various document formats (PDF, DOCX, TXT, JSON, MD)
- **🔍 Semantic Search**: RAG-powered document retrieval with vector embeddings
- **📊 Project Management**: Organize test cases and documents by projects
- **🔐 Secure Authentication**: Enterprise-grade security with user isolation
- **☁️ Cloud Storage**: Scalable content management
- **🔄 Real-time Streaming**: Live test case generation with progress tracking

## Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   AI Agent      │───▶│   OopsBot MCP    │───▶│   Backend       │
│   (Client)      │    │   Server         │    │   Services      │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │
                                ▼
                       ┌──────────────────┐    ┌─────────────────┐
                       │   Cloud Storage  │    │   AI Services   │
                       │   (Test Cases)   │    │   (Documents)   │
                       └──────────────────┘    └─────────────────┘
```

## Available Tools

### 1. Project Management

#### `list_user_projects`
Lists all projects associated with the authenticated user.

**Returns:**
```json
{
  "projects": [
    {
      "project_id": "uuid-string",
      "name": "Project Name",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ],
  "total": 1,
  "user_id": "user@example.com"
}
```

#### `create_project`
Creates a new project to organize documents and test cases.

**Parameters:**
- `name` (string): Unique project name for the user

**Example:**
```json
{
  "name": "E-commerce Login Flow"
}
```

### 2. Document Management

#### `upload_document_to_rag`
Uploads documents from the model context to the RAG service for processing.

**Parameters:**
- `filename` (string): File name with extension
- `filecontent` (string): File content from the model's context
- `topic` (string, optional): Document category (default: "general")
- `project_id` (string, optional): Project ID (default: "default")

**Example:**
```json
{
  "filename": "login_requirements.pdf",
  "filecontent": "User authentication requirements...",
  "topic": "authentication",
  "project_id": "project-uuid"
}
```

### 3. Test Case Generation

#### `generate_testcase_titles`
Generates test case titles by starting a new active session.

**Parameters:**
- `project_id` (string): Project ID (not name)
- `query` (string): User's request for test case generation

**Example:**
```json
{
  "project_id": "project-uuid",
  "query": "Generate test cases for user login functionality"
}
```

**Returns:**
```json
{
  "project_id": "project-uuid",
  "session_id": "user@example.com_12345",
  "titles": [
    "Valid login with correct credentials",
    "Invalid login with wrong password",
    "Login with empty fields"
  ],
  "total_titles": 3
}
```

#### `generate_testcases_from_titles`
Generates full, detailed test cases from selected titles.

**Parameters:**
- `project_id` (string): Project ID
- `session_id` (string): Session ID from `generate_testcase_titles`
- `selected_titles` (array): List of 1-indexed title numbers

**Example:**
```json
{
  "project_id": "project-uuid",
  "session_id": "user@example.com_12345",
  "selected_titles": [1, 2, 3]
}
```

### 4. Test Case Retrieval

#### `list_current_session_test_titles`
Lists test case titles for the current active session.

**Parameters:**
- `project_id` (string): Project ID
- `session_id` (string, optional): Session ID (uses active session if not specified)
- `skip` (integer, optional): Pagination offset (default: 0)
- `limit` (integer, optional): Maximum results (default: 50)

#### `get_generated_testcases`
Retrieves all generated test cases for a project.

**Parameters:**
- `project_id` (string): Project ID

**Returns:**
```json
{
  "success": true,
  "project_id": "project-uuid",
  "testcases": [
    {
      "testcase_id": "tc_12345",
      "project_id": "project-uuid",
      "user_id": "user@example.com",
      "content": "Detailed test case content..."
    }
  ],
  "count": 1
}
```

## Authentication

The OopsBot MCP Server uses secure token-based authentication.

### Getting Access
1. Sign up at [app.oopsbot.com](https://app.oopsbot.com)
2. Generate your MCP API token from the dashboard
3. Use the token in your IDE or client application

### Authentication Format
```http
Authorization: Bearer <your_api_token>
```

## IDE Integration

### Supported IDEs

#### Visual Studio Code
1. Install the MCP extension
2. Add OopsBot MCP server configuration:
```json
{
  "mcpServers": {
    "oopsbot": {
      "command": "npx",
      "args": ["@oopsbot/mcp-server"],
      "env": {
        "OOPSBOT_API_TOKEN": "your_token_here"
      }
    }
  }
}
```

#### Cursor
1. Open Cursor settings
2. Navigate to MCP servers
3. Add OopsBot configuration:
```json
{
  "oopsbot": {
    "serverUrl": "https://mcp.oopsbot.com",
    "apiToken": "your_token_here"
  }
}
```

#### Claude Desktop
1. Edit `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "oopsbot": {
      "command": "node",
      "args": ["/path/to/oopsbot-mcp/server.js"],
      "env": {
        "OOPSBOT_API_TOKEN": "your_token_here"
      }
    }
  }
}
```

#### JetBrains IDEs (IntelliJ, PyCharm, WebStorm)
1. Install MCP plugin from marketplace
2. Configure in Settings > Tools > MCP:
   - Server URL: `https://mcp.oopsbot.com`
   - API Token: `your_token_here`

#### Neovim
```lua
require('mcp').setup({
  servers = {
    oopsbot = {
      cmd = {'node', '/path/to/oopsbot-mcp/server.js'},
      env = {
        OOPSBOT_API_TOKEN = 'your_token_here'
      }
    }
  }
})
```

#### Emacs
```elisp
(use-package mcp-mode
  :config
  (add-to-list 'mcp-servers
               '(oopsbot
                 :command ("node" "/path/to/oopsbot-mcp/server.js")
                 :env (("OOPSBOT_API_TOKEN" . "your_token_here")))))
```

### Configuration

| Setting | Description | Required |
|---------|-------------|----------|
| `OOPSBOT_API_TOKEN` | Your API token from oopsbot.com | Yes |
| `OOPSBOT_SERVER_URL` | Server endpoint | No (defaults to production) |
| `OOPSBOT_PROJECT_ID` | Default project ID | No (defaults to "default") |

## Client Integration

### Direct API Integration

#### Python
```python
import requests

headers = {
    "Authorization": "Bearer your_api_token",
    "Content-Type": "application/json"
}

# Generate test case titles
response = requests.post(
    "https://api.oopsbot.com/mcp/tools/generate_testcase_titles",
    headers=headers,
    json={
        "project_id": "default",
        "query": "Test cases for user authentication"
    }
)
```

#### JavaScript/Node.js
```javascript
const response = await fetch('https://api.oopsbot.com/mcp/tools/list_user_projects', {
  method: 'POST',
  headers: {
    'Authorization': 'Bearer your_api_token',
    'Content-Type': 'application/json'
  }
});

const projects = await response.json();
```

#### cURL
```bash
curl -X POST https://api.oopsbot.com/mcp/tools/generate_testcase_titles \
  -H "Authorization: Bearer your_api_token" \
  -H "Content-Type: application/json" \
  -d '{"project_id": "default", "query": "Login test cases"}'
```

## Workflow Examples

### Complete Test Generation Workflow

1. **Create/Select Project**
   ```python
   # List existing projects
   projects = await client.call_tool("list_user_projects")
   
   # Or create new project
   project = await client.call_tool("create_project", {
       "name": "E-commerce Testing"
   })
   ```

2. **Upload Requirements Document**
   ```python
   result = await client.call_tool("upload_document_to_rag", {
       "filename": "requirements.pdf",
       "filecontent": "Product requirements content...",
       "topic": "authentication",
       "project_id": project["project"]["_id"]
   })
   ```

3. **Generate Test Case Titles**
   ```python
   titles = await client.call_tool("generate_testcase_titles", {
       "project_id": project["project"]["_id"],
       "query": "Generate comprehensive test cases for user login"
   })
   ```

4. **Generate Full Test Cases**
   ```python
   testcases = await client.call_tool("generate_testcases_from_titles", {
       "project_id": project["project"]["_id"],
       "session_id": titles["session_id"],
       "selected_titles": [1, 2, 3]  # Select desired titles
   })
   ```

5. **Retrieve Generated Test Cases**
   ```python
   all_testcases = await client.call_tool("get_generated_testcases", {
       "project_id": project["project"]["_id"]
   })
   ```

## Error Handling

### Common Error Responses

```json
{
  "error": "Authentication token not found",
  "success": false
}
```

```json
{
  "error": "Project 'ProjectName' already exists",
  "success": false
}
```

```json
{
  "error": "No active session found for project_id=default",
  "success": false
}
```

### Best Practices

1. **Always check authentication** before making requests
2. **Use project IDs, not names** for API calls
3. **Handle session management** properly in multi-step workflows
4. **Implement retry logic** for network failures
5. **Validate responses** before processing results

## Rate Limits & Quotas

- **Document Upload**: 50MB max file size
- **Test Generation**: Token-based consumption
- **API Calls**: Standard rate limiting applies
- **Concurrent Sessions**: One active session per user

## Security Considerations

- **JWT Tokens**: Use secure secrets and proper expiration
- **User Isolation**: All data is scoped to authenticated users
- **Transport Security**: HTTPS recommended for production
- **Token Storage**: Store tokens securely, never in plain text
- **Access Control**: Users can only access their own projects and data

## Monitoring & Logging

The server provides comprehensive logging for:
- Authentication events
- Tool execution
- File uploads
- Error conditions
- Performance metrics

Log levels: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`

## Support & Troubleshooting

### Common Issues

1. **Authentication Failures**: Verify your API token is valid
2. **Rate Limits**: Check your plan limits at oopsbot.com
3. **Upload Failures**: Ensure file size is under 50MB
4. **Generation Errors**: Verify documents are properly uploaded

### Getting Help

- 📧 Email: support@oopsbot.com
- 💬 Discord: [Join our community](https://discord.gg/2x2rVWmU)
- 📖 Documentation: [docs.oopsbot.com](https://www.oopsbot.com/docs)

## Pricing

- **Free Tier**: 15 test cases/day

[View detailed pricing](https://oopsbot.com/pricing)

## Version Information

- **MCP Protocol**: 2024-11-05
- **Server Version**: 1.0.0
- **API Version**: v1

---

*Get started at [oopsbot.com](https://oopsbot.com) • [Documentation](https://docs.oopsbot.com) • [Support](mailto:support@oopsbot.com)*