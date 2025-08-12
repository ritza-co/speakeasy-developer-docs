---
title: "Use MCP to turn your best Slack threads into social posts"
description: "Learn how to use MCP servers with OpenAI Agents, Slack API, Gram and Postiz to create social media posts from Slack threads."
---

import { Screenshot } from "@/mdx/components";

# Use MCP to turn your best Slack threads into social posts

Teams create valuable insights in Slack discussions daily. Technical breakthroughs, market observations, and customer feedback make for excellent social media content. But extracting these insights from lengthy threads and manually creating posts is time-consuming.

What if adding a 🚀 emoji to any valuable message could automatically create and schedule social media posts? What would normally require custom webhook handling and API orchestration becomes a simple AI agent and MCP server setup.

This article shows how to build a Slack MCP server using [Gram](https://getgram.ai), connect it with [Postiz](https://docs.postiz.com/public-api) for social media scheduling, and create an AI agent that transforms team discussions into engaging social content with just an emoji reaction.

## Why use MCP? 

Teams already turning Slack threads into social posts typically:

- Search for valuable threads in a workspace.
- Read through long conversations to identify key insights.
- Extract specific messages worth sharing externally.
- Rewrite content for different social platforms.
- Manually schedule posts across multiple channels.

When teams are having multiple high-value discussions weekly, this manual process **is** overwhelming and great content **is** probably buried in Slack.

Automating parts of the process with custom integrations is possible. For instance, writing Python scripts that call the Slack API to retrieve messages, send them to OpenAI for analysis and content creation, then use function calling to structure responses and fire requests to [Postiz](https://docs.postiz.com/public-api) for scheduling. But the process would still be time-consuming: manually running scripts whenever finding a useful thread.

Instead, turn threads into social posts using just a reaction emoji. Here's how it works: The 🚀 emoji triggers a Slack event that hits a webhook endpoint, which runs an AI agent with access to MCP tools for both Slack and Postiz. The agent retrieves the thread content, analyzes it for key insights, composes engaging social media posts, and schedules them automatically.

### The MCP Advantage

MCP standardizes how LLMs access different tools. Instead of writing custom integrations for Slack, Postiz, and future tools to add, there is one consistent interface that agents can use intelligently across any platform. Since MCP is an open protocol, it works with any LLM model or agent framework.

With [Gram](https://getgram.ai/), there's no need to implement MCP protocol code. The platform automatically converts OpenAPI documents into hosted MCP servers, allowing focus on building APIs.

Here's the automated flow:

<Screenshot
  variant="cli"
  image={{
    src: "./assets/mcp-slack-workflow.png",
    alt: "Automated Workflow",
  }}
/>

## Prerequisites

To build this automated Slack-to-social media system, these are required:

* [Python](https://www.python.org/) 3.11 or higher.
* [Gram](https://getgram.ai/) account – To host the Slack MCP server.
* [Postiz](https://platform.postiz.com/auth) account – To schedule social media posts (7-day free trial available).
* [OpenAI API](https://openai.com/api/) key – To power the AI agent that orchestrates the workflow.
* [Ngrok](https://ngrok.com/) – To expose local API during development (or use any hosting provider).
* [Slack account](https://slack.com/) with the appropriate permissions for adding apps to a workspace.

### Quick start option

To skip straight to [turning the API into an MCP server](#turning-the-api-into-an-mcp-server), find the complete Slack API server code in the [example repository](github.com/ritza-co/speakeasy-examples/slack-mcp-server-api) and follow the README to get a working application running.

### Implementation overview

Implementing this automation includes four steps:

- Build a Slack API wrapper that exposes message and thread data through HTTP endpoints.
- Deploy an MCP server on Gram using the API OpenAPI document.
- Configure Postiz MCP integration for social media scheduling.
- Create an AI agent that connects to both Slack and Postiz MCP servers and handles the automated workflow.

### Create Slack app and bot token

Create a new Slack app to access workspace data:

Navigate to [api.slack.com/apps](https://api.slack.com/apps) and click **Create New App**. Select **From scratch**, enter app name and workspace.

<Screenshot
  image={{
    src: "./assets/create-slack-app.png",
    alt: "Create new Slack app",
  }}
/>

Navigate to **OAuth & Permissions** in the sidebar. Scroll to **Bot Token Scopes** and add these required scopes:

<Screenshot
  image={{
    src: "./assets/slack-bot-scopes.png",
    alt: "Add bot token scopes",
  }}
/>

Click **Install to Workspace** at the top of the page. Authorize the app and copy the **Bot User OAuth Token** that starts with `xoxb-`.

<Screenshot
  image={{
    src: "./assets/slack-bot-token.png",
    alt: "Copy bot user OAuth token",
  }}
/>

Save the token to environment variables as `SLACK_USER_TOKEN`.

## Build the Slack integration

First, let's create an API that will retrieve Slack messages and threads for the agent. This API will serve as the backend for the MCP server and give the agent access to Slack data through HTTP endpoints.

The API exposes these endpoints for the agent's Slack threads analysis:

- **GET /messages/search**: Search for messages across Slack channels with advanced filtering options.
- **GET /messages/{channel_id}/{message_ts}/thread**: Retrieve a complete message thread including parent message and all replies.
- **GET /users/{user_id}**: Get detailed information about a Slack user.
- **GET /channels**: List all accessible Slack channels.
- **GET /channels/{channel_id}/messages**: Retrieve messages from a specific channel.

Building the API involves:

- Set up the Python project with dependencies.
- Define the FastAPI application and Pydantic models for data validation.
- Implement the endpoint logic with proper Slack SDK integration.
- Enrich the OpenAPI document with `x-gram` extensions that help Gram generate better MCP tool descriptions for the agent.

### Create the project

Create a new Python project directory and set up the environment:

```bash
mkdir slack-mcp-server
cd slack-mcp-server
```

Create a virtual environment and activate it:

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

Create a `requirements.txt` file with all the dependencies needed for the Slack API integration, FastAPI web server, and OpenAI Agents SDK with Gram AI SDK for MCP integration:

```txt
fastapi[all]==0.115.5
slack-sdk==3.29.0
python-dotenv==1.0.1
pydantic>=2.11.2
uvicorn[standard]==0.32.1
openai-agents>=0.2.4
gram-ai>=0.1.0
pytest==8.3.4
pytest-asyncio==0.25.0
httpx==0.28.1
```

Install all dependencies:

```bash
pip install -r requirements.txt
```

Create a Python package named `app` with the following structure:

```bash
touch .env
mkdir app
touch app/__init__.py
touch app/main.py
```

### Add the FastAPI app and Pydantic models

Setting up the FastAPI application requires authentication and data validation. The bearer token authentication is needed to access Slack securely, Pydantic models to structure API responses, and detailed endpoint documentation for Gram's MCP conversion.

In the `main.py` file, create the FastAPI app with Bearer token security:

```python
from fastapi import FastAPI, HTTPException, Depends, Query, Path, Request
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
from fastapi.openapi.utils import get_openapi
from fastapi.responses import PlainTextResponse, JSONResponse
from typing import Optional, List, Dict, Any, Union
from pydantic import BaseModel, Field
from datetime import datetime
from dotenv import load_dotenv
from slack_sdk import WebClient
from slack_sdk.errors import SlackApiError
import asyncio

load_dotenv()

app = FastAPI(
    title="Slack MCP Server API",
    description="A comprehensive API for searching and retrieving Slack messages, threads, and user information",
    version="1.0.0",
    openapi_tags=[
        {
            "name": "messages",
            "description": "Operations for searching and retrieving Slack messages",
        },
        {"name": "threads", "description": "Operations for managing message threads"},
        {"name": "users", "description": "Operations for retrieving user information"},
        {
            "name": "channels",
            "description": "Operations for channel management and information",
        },
    ],
)

# Security
security = HTTPBearer()
```

Define Pydantic models for data validation and automatic OpenAPI schema generation:

```python
# Pydantic models
class SlackMessage(BaseModel):
    """Model representing a Slack message"""

    ts: str = Field(..., description="Message timestamp")
    text: str = Field(..., description="Message text content")
    user: str = Field(..., description="User ID who sent the message")
    channel: str = Field(..., description="Channel ID where message was sent")
    thread_ts: Optional[str] = Field(
        None, description="Thread timestamp if message is part of a thread"
    )
    reply_count: Optional[int] = Field(None, description="Number of replies in thread")
    reactions: Optional[List[Dict[str, Any]]] = Field(
        None, description="Message reactions"
    )


class SlackUser(BaseModel):
    """Model representing a Slack user"""

    id: str = Field(..., description="User ID")
    name: str = Field(..., description="Username")
    real_name: Optional[str] = Field(None, description="Real name")
    email: Optional[str] = Field(None, description="Email address")
    is_bot: bool = Field(..., description="Whether user is a bot")
    profile: Optional[Dict[str, Any]] = Field(
        None, description="User profile information"
    )


class SlackChannel(BaseModel):
    """Model representing a Slack channel"""

    id: str = Field(..., description="Channel ID")
    name: str = Field(..., description="Channel name")
    is_private: bool = Field(..., description="Whether channel is private")
    is_archived: bool = Field(..., description="Whether channel is archived")
    member_count: Optional[int] = Field(None, description="Number of members")
    topic: Optional[str] = Field(None, description="Channel topic")
    purpose: Optional[str] = Field(None, description="Channel purpose")


class MessageSearchResponse(BaseModel):
    """Response model for message search results"""

    messages: List[SlackMessage] = Field(..., description="List of matching messages")
    total: int = Field(..., description="Total number of results")
    has_more: bool = Field(..., description="Whether more results are available")


class ThreadResponse(BaseModel):
    """Response model for thread information"""

    parent_message: SlackMessage = Field(
        ..., description="Parent message of the thread"
    )
    replies: List[SlackMessage] = Field(..., description="Thread replies")
    reply_count: int = Field(..., description="Total number of replies")
    
class SlackUrlVerificationEvent(BaseModel):
    """Model for Slack URL verification event"""
    
    token: str = Field(..., description="Verification token")
    challenge: str = Field(..., description="Challenge string to echo back")
    type: str = Field(..., description="Event type - should be 'url_verification'")


class SlackEventWrapper(BaseModel):
    """Model for general Slack event payload"""
    
    token: Optional[str] = Field(None, description="Verification token")
    challenge: Optional[str] = Field(None, description="Challenge for url_verification")
    type: str = Field(..., description="Event type")
    team_id: Optional[str] = Field(None, description="Team ID")
    api_app_id: Optional[str] = Field(None, description="App ID")
    event: Optional[Dict[str, Any]] = Field(None, description="Inner event data")
    event_context: Optional[str] = Field(None, description="Event context")
    event_id: Optional[str] = Field(None, description="Event ID")
    event_time: Optional[int] = Field(None, description="Event timestamp")
```

Create the authenticated Slack client dependency:

```python
async def get_slack_client(
    credentials: HTTPAuthorizationCredentials = Depends(security),
) -> WebClient:
    """
    Get authenticated Slack WebClient

    Args:
        credentials: Bearer token from Authorization header

    Returns:
        WebClient: Authenticated Slack client

    Raises:
        HTTPException: If token is invalid
    """
    try:
        client = WebClient(token=credentials.credentials)
        # Test the token
        response = client.auth_test()
        if not response["ok"]:
            raise HTTPException(status_code=401, detail="Invalid Slack token")
        return client
    except SlackApiError as e:
        raise HTTPException(
            status_code=401,
            detail=f"Slack authentication failed: {e.response['error']}",
        )
```

The FastAPI application now has proper authentication, data validation, and models. This will generate a clean OpenAPI document for the app that Gram can convert into MCP tools.

### Add the endpoints

Each endpoint handles a specific aspect of Slack data retrieval, from searching messages to retrieving complete threads.

Add the `/messages/search` GET endpoint to enable searching Slack messages across channels and filtering by text, user, channel, date range, or result limits:

```python
@app.get(
    "/messages/search",
    response_model=MessageSearchResponse,
    tags=["messages"],
    summary="Search Slack messages",
    operation_id="search_messages",
    description="""
    Search for messages across Slack channels using various filters.
    Supports text search, user filtering, date ranges, and channel-specific searches.
    Requires a valid Slack bot token with search:read scope.
    """,
    responses={
        200: {"description": "Search results with matching messages"},
        401: {"description": "Authentication failed - invalid token"},
        403: {"description": "Insufficient permissions"},
        429: {"description": "Rate limit exceeded"},
    },
)
async def search_messages(
    query: str = Query(
        ..., description="Search query text", examples=["important meeting"]
    ),
    channel: Optional[str] = Query(
        None, description="Specific channel ID to search in", examples=["C1234567890"]
    ),
    user: Optional[str] = Query(
        None, description="Filter by user ID", examples=["U1234567890"]
    ),
    after: Optional[datetime] = Query(
        None, description="Search messages after this date"
    ),
    before: Optional[datetime] = Query(
        None, description="Search messages before this date"
    ),
    limit: int = Query(
        20, ge=1, le=100, description="Maximum number of results to return"
    ),
    slack_client: WebClient = Depends(get_slack_client),
):
    """Search for messages in Slack channels with comprehensive filtering options"""
    try:
        # Build search query
        search_query = query

        if channel:
            search_query += f" in:#{channel}"
        if user:
            search_query += f" from:@{user}"
        if after:
            search_query += f" after:{after.strftime('%Y-%m-%d')}"
        if before:
            search_query += f" before:{before.strftime('%Y-%m-%d')}"

        # Perform search
        response = slack_client.search_messages(
            query=search_query, count=limit, sort="timestamp"
        )

        if not response["ok"]:
            raise HTTPException(
                status_code=400, detail=f"Search failed: {response['error']}"
            )

        # Process results
        messages = []
        for match in response["messages"]["matches"]:
            message = SlackMessage(
                ts=match["ts"],
                text=match.get("text", ""),
                user=match.get("user", ""),
                channel=match.get("channel", {}).get("id", ""),
                thread_ts=match.get("thread_ts"),
                reply_count=match.get("reply_count", 0),
            )
            messages.append(message)

        return MessageSearchResponse(
            messages=messages,
            total=response["messages"]["total"],
            has_more=len(messages) < response["messages"]["total"],
        )

    except SlackApiError as e:
        raise HTTPException(
            status_code=400, detail=f"Slack API error: {e.response['error']}"
        )

```

The code above constructs a Slack search query by combining the text query with optional filters, sends the request through the Slack SDK client, and returns structured message data in `MessageSearchResponse` that the MCP server will expose as a tool.

Add the `/messages/{channel_id}/{message_ts}/thread` GET endpoint to enable retrieving complete conversation threads, which is essential for the AI agent to understand the full context of discussions.

```python
@app.get(
    "/messages/{channel_id}/{message_ts}/thread",
    response_model=ThreadResponse,
    tags=["threads"],
    summary="Get message thread",
    operation_id="get_thread",
    description="""
    Retrieve a complete thread including the parent message and all replies.
    Useful for getting conversation context around a specific message.
    Requires a valid Slack bot token with channels:history scope and access to the channel.
    """,
    responses={
        200: {"description": "Thread information with parent message and replies"},
        401: {"description": "Authentication failed"},
        404: {"description": "Message or thread not found"},
        403: {"description": "Access denied to channel"},
    },
)
async def get_thread(
    channel_id: str = Path(..., description="Channel ID containing the message"),
    message_ts: str = Path(..., description="Timestamp of the parent message"),
    slack_client: WebClient = Depends(get_slack_client),
):
    """Retrieve a complete message thread with all replies"""
    try:
        # Get thread replies
        response = slack_client.conversations_replies(
            channel=channel_id, ts=message_ts, inclusive=True
        )

        if not response["ok"]:
            raise HTTPException(
                status_code=400, detail=f"Failed to get thread: {response['error']}"
            )

        messages_data = response["messages"]
        if not messages_data:
            raise HTTPException(status_code=404, detail="Thread not found")

        # Convert to SlackMessage objects
        messages = []
        for msg_data in messages_data:
            message = SlackMessage(
                ts=msg_data["ts"],
                text=msg_data.get("text", ""),
                user=msg_data.get("user", ""),
                channel=channel_id,
                thread_ts=msg_data.get("thread_ts"),
                reply_count=msg_data.get("reply_count", 0),
                reactions=msg_data.get("reactions", []),
            )
            messages.append(message)

        parent_message = messages[0]
        replies = messages[1:] if len(messages) > 1 else []

        return ThreadResponse(
            parent_message=parent_message, replies=replies, reply_count=len(replies)
        )

    except SlackApiError as e:
        raise HTTPException(
            status_code=400, detail=f"Slack API error: {e.response['error']}"
        )
```

The endpoint above uses Slack's message timestamp (like `1234567890.123456`) as the unique identifier to fetch the complete thread conversation, separating the parent message from its replies for easier agent processing.

Add the `/users/{user_id` GET endpoint to enable retrieving detailed user information that helps the agent understand message authorship and context when analyzing threads.

```python
@app.get(
    "/users/{user_id}",
    response_model=SlackUser,
    tags=["users"],
    summary="Get user information",
    operation_id="get_user",
    description="""
    Retrieve detailed information about a Slack user including profile details, 
    real name, and other metadata. Requires a valid Slack bot token with users:read scope.
    """,
    responses={
        200: {"description": "User information"},
        401: {"description": "Authentication failed"},
        404: {"description": "User not found"},
    },
)
async def get_user(
    user_id: str = Path(..., description="Slack user ID", examples=["U1234567890"]),
    slack_client: WebClient = Depends(get_slack_client),
):
    """Get detailed information about a Slack user"""
    try:
        response = slack_client.users_info(user=user_id)

        if not response["ok"]:
            if response["error"] == "user_not_found":
                raise HTTPException(status_code=404, detail="User not found")
            raise HTTPException(
                status_code=400, detail=f"Failed to get user: {response['error']}"
            )

        user_data = response["user"]

        return SlackUser(
            id=user_data["id"],
            name=user_data["name"],
            real_name=user_data.get("real_name"),
            email=user_data.get("profile", {}).get("email"),
            is_bot=user_data.get("is_bot", False),
            profile=user_data.get("profile", {}),
        )

    except SlackApiError as e:
        raise HTTPException(
            status_code=400, detail=f"Slack API error: {e.response['error']}"
        )

```

The endpoint above fetches user profile information from Slack's API and formats it into the structured `SlackUser` model, giving the agent access to names, email addresses, and other profile details for better thread analysis.

Add the `/channels` GET endpoint to enable listing all accessible channels in the workspace, allowing the agent to discover available channels and convert channel names to IDs for targeted searches and message retrieval.

```python
@app.get(
    "/channels",
    response_model=List[SlackChannel],
    tags=["channels"],
    summary="List channels",
    operation_id="list_channels",
    description="""
    List all channels accessible to the bot, including public channels,
    private channels the bot is a member of, and optionally archived channels.
    Requires a valid Slack bot token with channels:read scope.
    """,
    responses={
        200: {"description": "List of accessible channels"},
        401: {"description": "Authentication failed"},
    },
)
async def list_channels(
    include_private: bool = Query(True, description="Include private channels"),
    include_archived: bool = Query(False, description="Include archived channels"),
    limit: int = Query(
        100, ge=1, le=1000, description="Maximum number of channels to return"
    ),
    slack_client: WebClient = Depends(get_slack_client),
):
    """List all accessible Slack channels"""
    try:
        # Get public channels
        channels = []

        # Get public channels
        response = slack_client.conversations_list(
            types="public_channel", exclude_archived=not include_archived, limit=limit
        )

        if response["ok"]:
            channels.extend(response["channels"])

        # Get private channels if requested
        if include_private:
            response = slack_client.conversations_list(
                types="private_channel",
                exclude_archived=not include_archived,
                limit=limit,
            )

            if response["ok"]:
                channels.extend(response["channels"])

        # Convert to SlackChannel objects
        result = []
        for channel_data in channels:
            channel = SlackChannel(
                id=channel_data["id"],
                name=channel_data["name"],
                is_private=channel_data.get("is_private", False),
                is_archived=channel_data.get("is_archived", False),
                member_count=channel_data.get("num_members"),
                topic=channel_data.get("topic", {}).get("value"),
                purpose=channel_data.get("purpose", {}).get("value"),
            )
            result.append(channel)

        return result

    except SlackApiError as e:
        raise HTTPException(
            status_code=400, detail=f"Slack API error: {e.response['error']}"
        )
```

The endpoint above makes separate API calls to retrieve public and private channels, then transforms the raw Slack data into the structured `SlackChannel` models with standardized fields.

Add the ` /channels/{channel_id}/message` GET endpoint to enable retrieving messages from a specific channel with optional date filtering and pagination, enabling the agent to analyze recent channel activity or messages from specific time periods.

```python
@app.get(
    "/channels/{channel_id}/messages",
    response_model=List[SlackMessage],
    tags=["messages"],
    summary="Get channel messages",
    operation_id="get_channel_messages",
    description="""
    Retrieve messages from a specific channel with optional date range filtering 
    and pagination support. Requires a valid Slack bot token with channels:history 
    scope and access to the specified channel.
    """,
    responses={
        200: {"description": "List of channel messages"},
        401: {"description": "Authentication failed"},
        403: {"description": "Access denied to channel"},
        404: {"description": "Channel not found"},
    },
)
async def get_channel_messages(
    channel_id: str = Path(..., description="Channel ID to retrieve messages from"),
    latest: Optional[str] = Query(
        None, description="Latest message timestamp to include"
    ),
    oldest: Optional[str] = Query(
        None, description="Oldest message timestamp to include"
    ),
    limit: int = Query(
        50, ge=1, le=1000, description="Maximum number of messages to return"
    ),
    slack_client: WebClient = Depends(get_slack_client),
):
    """Retrieve messages from a specific channel"""
    try:
        response = slack_client.conversations_history(
            channel=channel_id,
            latest=latest,
            oldest=oldest,
            limit=limit,
            inclusive=True,
        )

        if not response["ok"]:
            if response["error"] == "channel_not_found":
                raise HTTPException(status_code=404, detail="Channel not found")
            elif response["error"] == "not_in_channel":
                raise HTTPException(status_code=403, detail="Bot not in channel")
            raise HTTPException(
                status_code=400, detail=f"Failed to get messages: {response['error']}"
            )

        # Convert to SlackMessage objects
        messages = []
        for msg_data in response["messages"]:
            message = SlackMessage(
                ts=msg_data["ts"],
                text=msg_data.get("text", ""),
                user=msg_data.get("user", ""),
                channel=channel_id,
                thread_ts=msg_data.get("thread_ts"),
                reply_count=msg_data.get("reply_count", 0),
                reactions=msg_data.get("reactions", []),
            )
            messages.append(message)

        return messages

    except SlackApiError as e:
        raise HTTPException(
            status_code=400, detail=f"Slack API error: {e.response['error']}"
        )
```

The code above uses Slack's `conversations_history` API with optional timestamp filtering to retrieve messages from a specific channel, handling common errors like missing channels or insufficient permissions, then converts the raw message data into the `SlackMessage` model.

### Customize the OpenAPI output

Let's enhance the OpenAPI document for MCP server generation. 

Standard OpenAPI descriptions are designed for humans, not LLMs. Verbose descriptions cause token bloat and vague descriptions lead to poor tool selection. 

Gram's `x-gram` extensions provide better context and instructions for the agent, enabling LLM-optimized descriptions with context about when to use each tool and any prerequisites needed.

```python
def custom_openapi():
    """Customize OpenAPI Output with x-gram extensions for getgram MCP servers"""

    if app.openapi_schema:
        return app.openapi_schema

    openapi_schema = get_openapi(
        title=app.title,
        version=app.version,
        description=app.description,
        routes=app.routes,
        tags=app.openapi_tags,
    )

    # Add x-gram extensions to specific operations
    x_gram_extensions = {
        "search_messages": {
            "x-gram": {
                "name": "search_slack_messages",
                "summary": "Search for messages in Slack channels",
                "description": """<context>
                This tool searches for messages across Slack channels with comprehensive filtering options.
                It can search by text content, specific users, date ranges, and within specific channels.
                Perfect for finding important conversations, tracking mentions, or analyzing communication patterns.
                </context>

                <prerequisites>
                - If you have a channel name instead of ID, use the list_channels tool first to get the channel ID
                - If you have a username instead of user ID, use the get_user tool first to get the user ID  
                - Ensure the bot has appropriate permissions to search the target channels
                </prerequisites>""",
                "responseFilterType": "jq",
            }
        },
        "get_thread": {
            "x-gram": {
                "name": "get_slack_thread",
                "summary": "Retrieve complete message thread with replies",
                "description": """<context>
                This tool fetches an entire conversation thread, including the original message
                and all replies. Essential for understanding the full context of discussions
                and following conversation flows in Slack.
                </context>

                <prerequisites>
                - You need both the channel ID and the timestamp of the parent message
                - The message timestamp should be the thread_ts value from search results
                - Ensure the bot has access to the channel containing the thread
                </prerequisites>""",
                "responseFilterType": "jq",
            }
        },
        "get_user": {
            "x-gram": {
                "name": "get_slack_user",
                "summary": "Get detailed information about a Slack user",
                "description": """<context>
                This tool retrieves comprehensive user information from Slack, including
                profile details, contact information, and user status. Perfect for understanding
                message authorship and getting user context.
                </context>

                <prerequisites>
                - You need the user ID (starts with U) not the username
                - If you only have a username or display name, search for it first
                - User information availability depends on workspace privacy settings
                </prerequisites>""",
                "responseFilterType": "jq",
            }
        },
        "list_channels": {
            "x-gram": {
                "name": "list_slack_channels",
                "summary": "List all accessible Slack channels",
                "description": """<context>
                This tool provides a comprehensive list of Slack channels that the bot can access.
                Includes channel names, IDs, member counts, and topics. Use this to discover
                available channels before searching or retrieving messages.
                </context>

                <prerequisites>
                - The bot will only see public channels and private channels it's a member of
                - To access private channels, the bot must be explicitly added to them
                - Channel information depends on bot permissions and workspace settings
                </prerequisites>""",
                "responseFilterType": "jq",
            }
        },
        "get_channel_messages": {
            "x-gram": {
                "name": "get_channel_messages",
                "summary": "Retrieve messages from a specific channel",
                "description": """<context>
                This tool fetches messages from a particular Slack channel, with support
                for date range filtering and pagination. Ideal for getting recent channel
                activity or messages from specific time periods.
                </context>

                <prerequisites>
                - You need the channel ID (starts with C) not the channel name
                - Use the list_channels tool first if you only have the channel name
                - Ensure the bot is a member of private channels to access their messages
                </prerequisites>""",
                "responseFilterType": "jq",
            }
        }
    }

    # Apply x-gram extensions to paths
    if "paths" in openapi_schema:
        for path, path_item in openapi_schema["paths"].items():
            for method, operation in path_item.items():
                if method.lower() in ["get", "post", "put", "delete", "patch"]:
                    operation_id = operation.get("operationId")
                    if operation_id in x_gram_extensions:
                        operation.update(x_gram_extensions[operation_id])

    app.openapi_schema = openapi_schema
    return app.openapi_schema
```

This customization adds structured context and prerequisites to each endpoint, helping Gram generate MCP tools that the AI agent can use more effectively.

Next, override the custom openapi definition from FastAPI and add the logic to run the FastAPI app.

```python
# Override the default OpenAPI function
app.openapi = custom_openapi

if __name__ == "__main__":
    import uvicorn

    uvicorn.run(app, host="0.0.0.0", port=8000)
```

### Generate OpenAPI files

The following script generates JSON and YAML versions of the OpenAPI document. 

At the root of the project, create a file called `generate_openapi.py` and add the following content.

```python
import json
import yaml
from pathlib import Path

# Add the app directory to the path so we can import main
import sys
sys.path.insert(0, str(Path(__file__).parent / "app"))

from main import app


def save_openapi_to_json():
    """Save OpenAPI spec to JSON"""
    
    with open("openapi.json", "w", encoding="utf-8") as json_file:
        json.dump(
            app.openapi(),
            json_file,
            indent=2,
        )
    print("Generated openapi.json")


def save_openapi_to_yaml():
    """Save OpenAPI spec to YAML"""
    
    with open("openapi.yaml", "w", encoding="utf-8") as yaml_file:
        yaml.dump(
            app.openapi(),
            yaml_file,
            default_flow_style=False,
            sort_keys=False,
            indent=2,
        )
    print("Generated openapi.yaml")


if __name__ == "__main__":
    print("🚀 Generating OpenAPI document files...")
    
    save_openapi_to_json()
    save_openapi_to_yaml()

```

This script imports the FastAPI app, and calls the `app.openapi()` method, and returns the dict version of the OpenAPI document. 

Run the generation script:

```bash
python generate_openapi.py
```

This is the output from the command:

```txt
🚀 Generating OpenAPI document files...
Generated openapi.json
Generated openapi.yaml
```

### Run the API

Start the FastAPI server and expose it with ngrok for testing:

```bash
uvicorn app.main:app
```

In another terminal, expose the API:

```bash
ngrok http 127.0.0.1:8000
```

Copy the HTTPS forwarding URL from ngrok – this URL is used when configuring the MCP server in Gram.

<Screenshot
  image={{
    src: "./assets/ngrok-terminal-running.png",
    alt: "Ngrok running in a terminal",
  }}
/>

## Turn the API into an MCP Server

Gram works with three key concepts: 

- **Tool definitions** that describe each API endpoint to LLMs.
- **Tool variations** that allow customizing descriptions.
- **Toolsets** that group related tools together. 

The `x-gram` extensions added previously will define how Gram provides the tools to agents.

### Upload the OpenAPI document

Navigate to the **Toolsets** page in the Gram dashboard.

<Screenshot
  image={{
    src: "./assets/gram-nav-to-toolsets.png",
    alt: "Navigate to Toolsets page",
  }}
/>

Click the **Get Started** button to add an API source. Upload the generated `openapi.json` file and give the API source this name **Slack API**. It is important that you use this name exactly to follow along with this guide.

<Screenshot
  image={{
    src: "./assets/gram-adding-api-source.png",
    alt: "Adding an API source",
  }}
/>

### Create a toolset

On the next page a modal will appear asking to create a toolset – enter "Slack Tools" as the name and click **Continue**. 

On the next page accept the default MCP server slug and click **Continue**.

To see the tool definitions, navigate to **Toolsets** and click on the **Slack Tools**. There the tools listed in the **Slack API** API source will be present: search messages, get threads, get users, list slack channels, get channel messages.

<Screenshot
  image={{
    src: "./assets/gram-adding-toolsets.png",
    alt: "Adding Toolset",
  }}
/>

### Create a new environment

Navigate to the Environments page and click **+ NEW ENVIRONMENT**. Enter "slack" as the environment name. Once created, this redirects to the environment configuration page. Click **NEW VARIABLE** to add environment variables.

<Screenshot
  image={{
    src: "./assets/adding-environment-variable.png",
    alt: "Adding environment variables",
  }}
/>

Add the Slack user token as `SLACK_API_HTTP_BEARER`. And add the ngrok URL as `SLACK_API_SERVER_URL`. These new variables will allow Gram to authenticate with Slack and route requests to the running API.

### Create a Gram API key

A Gram API key is needed to connect the agent to the MCP server. In the Gram dashboard, go to Settings and click **Create API Key**.

<Screenshot
  image={{
    src: "./assets/creating-gram-api-key.png",
    alt: "Creating a Gram API key",
  }}
/>

Copy the API key in the `.env` file of the FastAPI project. The `.env` file should currently look like this:

```txt
SLACK_USER_TOKEN=xoxp-your-slack-user-token-here
GRAM_AI_API_KEY=your-gram-ai-api-key-here
OPENAI_API_KEY=sk-proj-your-openai-api-key-here
```

This creates an MCP server for Slack data access that exposes the endpoints as standardized tools. Next, configure Postiz for social media scheduling, then build the agent that orchestrates the entire workflow.

## Connect to Postiz for social media posts scheduling

While Postiz supports scheduling across multiple social media platforms, this guide uses only X (formerly Twitter). Postiz can connect to LinkedIn, Facebook, or other platforms depending on needs.

First, ensure at least one social media account is connected in Postiz. This example adds an X account, but any platform can be used for scheduling posts.

On the Postiz dashboard, navigate to the **Settings** page and select **Public API**. The MCP server URL is displayed in the MCP section. This URL provides direct access to Postiz's posting and scheduling functionality using the MCP protocol.

<Screenshot
  image={{
    src: "./assets/postiz-mcp-config.png",
    alt: "Postiz MCP configuration",
  }}
/>

Copy the MCP URL and save it to the `.env` file in the FastAPI project as `POSTIZ_MCP_URL`. The environment variables should now look like this:

```txt
SLACK_USER_TOKEN=xoxp-your-slack-user-token-here
GRAM_AI_API_KEY=your-gram-ai-api-key-here
OPENAI_API_KEY=sk-proj-your-openai-api-key-here
POSTIZ_MCP_URL=your-postiz-mcp-url
```

## Create the AI agent workflow

Let's build the AI agent that orchestrates the entire workflow.

This involves creating the agent logic in a separate module, then adding a webhook for emoji reactions.

### Create the agent module

In the `app` folder, create a new file called `social_agent.py`. This file will contain the AI agent code that connects to the Slack and Postiz MCP servers.

Add the following code:

```python
import asyncio
import os
from agents import Agent, Runner, set_default_openai_key
from gram_ai.openai_agents import GramOpenAIAgents
from agents.mcp.server import MCPServerSse

postiz_mcp_server = MCPServerSse(
    name="postiz",
    params={
        "url": os.getenv("POSTIZ_MCP_URL")
    }
)

async def create_social_media_posts(channel_id: str, message_ts: str):
    gram = GramOpenAIAgents(api_key=os.getenv("GRAM_AI_API_KEY"))
    set_default_openai_key(os.getenv("OPENAI_API_KEY"))
    
    agent = Agent(
        name="Assistant",
        tools=gram.tools(
            project="default", 
            toolset="slack-tools",
            environment="slack",
        ),
        mcp_servers=[postiz_mcp_server]
    )
    
    prompt = f"""Hello friend.
    
                A rocket emoji was added to a message in channel {channel_id} at timestamp {message_ts}.

                Please analyze this thread and create Twitter posts based on the content. Then schedule 4 posts on Postiz.
            """
    
    await postiz_mcp_server.connect()
    result = await Runner.run(agent, prompt)
    await postiz_mcp_server.cleanup()
    
    return result.final_output 
```

The code above sets up the AI agent with access to both MCP servers: Gram's tools for Slack access and Postiz's direct MCP server for scheduling. The agent receives the channel and message details, then handles the complete workflow autonomously.

### Add the Slack events webhook

Integrating the agent with FastAPI requires adding a webhook endpoint that Slack's Events API will call when emoji reactions are added.

In the `main.py` file, add the event controller before the `custom_openapi` function:

```python
@app.post("/events", include_in_schema=False)
async def handle_slack_events(request: Request):
    """
    Handle Slack Events API requests.
    
    This endpoint processes various Slack events, starting with url_verification.
    It's excluded from OpenAPI documentation as it's internal to Slack integration.
    """
    try:
        # Parse the request body
        body = await request.json()
                
        # Handle url_verification event
        if body.get("type") == "url_verification":
            # Validate the event structure
            verification_event = SlackUrlVerificationEvent(**body)
            
            # Respond with the challenge value in plain text
            return PlainTextResponse(
                content=verification_event.challenge,
                status_code=200,
                headers={"Content-Type": "text/plain"}
            )
        
        # Handle event callbacks
        elif body.get("type") == "event_callback":
            # Parse the full event wrapper
            event_wrapper = SlackEventWrapper(**body)
            event = event_wrapper.event or {}
            
            # Handle rocket emoji reactions for social media posts
            if (event.get("type") == "reaction_added" and 
                event.get("reaction") == "rocket" and
                event.get("item", {}).get("type") == "message"):
                
                channel_id = event["item"]["channel"]
                message_ts = event["item"]["ts"]
                
                print(f"Rocket reaction: {channel_id}/{message_ts}")
                asyncio.create_task(
                    process_social_media_workflow(channel_id, message_ts)
                )
            
            return JSONResponse(
                content={"status": "ok"},
                status_code=200
            )
        
        # Unknown event type
        else:
            return JSONResponse(
                content={"error": "Unknown event type"},
                status_code=400
            )
            
    except Exception as e:
        print(f"Error: {e}")
        return JSONResponse(
            content={"status": "error", "message": str(e)},
            status_code=200
        )


async def process_social_media_workflow(channel_id: str, message_ts: str):
    """Process rocket reaction for social media content generation"""
    try:
        from app.social_agent import create_social_media_posts
        result = await create_social_media_posts(channel_id, message_ts)
        print(result)
    except Exception as e:
        print(f"Error: {e}")
```

The endpoint above handles two critical Slack events: URL verification (required when setting up the webhook) and emoji reactions.

Restart the FastAPI server to load the new endpoint:

```bash
uvicorn app.main:app
```

The webhook is now ready to receive Slack events and trigger the automated social media workflow.

### Configure the Slack Event URL

Before testing the integration, configure Slack to reach the event endpoint and send emoji reaction notifications.

#### Configure Event Subscriptions

- Go to the Slack app settings at [api.slack.com/apps](https://api.slack.com/apps).

- Select the app.
- Navigate to **Event Subscriptions** in the left sidebar.
- Toggle **Enable Events** to ON.

#### Add the request URL

- In the Request URL field, enter the webhook endpoint:

```txt
https://your-ngrok-url/events
```

- Slack will send a verification challenge and the FastAPI `/events` endpoint should automatically handle the challenge. If the challenge is successful , a green `Verified` text appears.

- Scroll down to **Subscribe to bot events**.

- Click **Add Bot User Event**.

- Add the `reaction_added` event.

<Screenshot
  image={{
    src: "./assets/slack-event-configuration.png",
    alt: "Slack Event Configuration",
  }}
/>

Save the changes, and the integration is ready to test. 

#### Verify bot permissions

Ensure the bot has the required OAuth user scopes under **OAuth & Permissions**:

- `channels:history` – Read channel messages.
- `channels:read` – Access channel information.
- `groups:history` – Access private channel history.
- `groups:read` – View basic information about a user's private channel.
- `reactions:read` – Detect emoji reactions.
- `users:read` – Get user profile details.

If new scopes were added, reinstall the app to the workspace.

With Slack configured to send reaction events, testing the complete automated workflow is ready.

## Test the integration 

Now let's test the complete automated workflow. Find a Slack thread with valuable content, add the bot to the channel by typing `/invite @your-bot-name`, then add a 🚀 reaction to the parent message of that thread.

When adding the rocket emoji, Slack immediately sends an event to the `/events` endpoint. Monitor the activity in the ngrok terminal to see incoming requests from Slack's servers. 

<Screenshot
  image={{
    src: "./assets/ngrok-terminal.png",
    alt: "Ngrok Terminal",
  }}
/>

The agent then makes requests to the Gram-hosted MCP server to retrieve the thread content, analyze it for social media potential, create platform-appropriate posts, and schedule them through the Postiz MCP server. The FastAPI server logs the rocket reaction detection and triggers the AI agent workflow.

```txt
INFO:     34.xxx.xxx.xxx:0 - "POST /events HTTP/1.1" 200 OK
INFO:     35.xxx.xxx.xxx:0 - "GET /messages/C097TXXXXX/1754342272.363509/thread HTTP/1.1" 200 OK
I've analyzed the thread and scheduled 4 Twitter posts based on the content. You can view and manage them [here](https://platform.postiz.com/p/cmdsgxxxxxxxxq).

Here's a summary of the scheduled posts:

1. **Domain Insights**: Highlighting trending tech-related domains and the lucrative potential of .io domains.
   
2. **Domain Sales Tips**: Sharing a successful sale story and recommending platforms like Sedo and Flippa.

3. **Pricing Guide**: Providing a quick guide on pricing different types of domains.

4. **Selling Tips**: Emphasizing the importance of updating listings and approaching potential buyers professionally. 

If you need further adjustments, feel free to ask!
```

Check your Postiz dashboard to see the scheduled posts. The agent will have created Twitter-optimized content based on the thread's key insights and scheduled them for publication.

<Screenshot
  image={{
    src: "./assets/postiz-scheduled-posts.png",
    alt: "Postiz scheduled posts",
  }}
/>

This creates a fully automated system that converts Slack discussions into social media content with just an emoji reaction. The integration uses two MCP servers: a custom Slack server hosted on Gram and Postiz's direct MCP integration, orchestrated by an OpenAI agent that handles the entire workflow intelligently.

## Final thoughts

This builds a complete automated system that transforms Slack discussions into social media content using MCP servers. By combining a custom Slack API with Gram's MCP hosting and Postiz's direct MCP integration, this creates a workflow that requires zero manual intervention: just add a rocket emoji and watch team insights become social posts.

The project demonstrates MCP's core value: standardizing how AI agents interact with different tools. Instead of writing custom integrations for each service, we used MCP servers that any AI agent can understand and use pragmatically.

The integration can be enhanced in several ways:

- **Add more social platforms** by connecting additional accounts in Postiz – LinkedIn for professional content, Facebook for broader reach.

- **Improve content quality** by refining the agent's prompt to include brand voice, specific hashtag strategies, or content formatting preferences.
