# AI research — n8n Workflow

## Overview
A simple conversational AI agent, triggered from n8n's built-in chat UI, that can send emails on request via Gmail.

## Trigger
- **When chat message received** (`@n8n/n8n-nodes-langchain.chatTrigger`) — starts the workflow whenever a message is sent through n8n's chat panel.

## Flow
```
When chat message received → Perception (AI Agent)
                                 ├─ OpenAI Chat Model (gpt-4.1-mini)
                                 ├─ Simple Memory (buffer window)
                                 └─ Gmail (tool)
```

## Nodes
| Node | Type | Role |
|---|---|---|
| When chat message received | chatTrigger | Entry point, receives the user's chat message |
| Perception | langchain.agent | The AI agent; decides what to do and which tool to call |
| OpenAI Chat Model | lmChatOpenAi (gpt-4.1-mini) | Language model powering the agent |
| Simple Memory | memoryBufferWindow | Keeps short-term conversation context |
| Gmail | gmailTool (`operation: send`) | Lets the agent send an email when asked |

## Credentials required
- **OpenAI account** (for the chat model)
- **Gmail account** (OAuth2, for sending mail)

## Notes / things to check
- The Gmail node's `sendTo` is currently hardcoded to a fixed address (`allwyngeorge1993@gmail.com`) rather than driven by `$fromAI`, so the agent can only email that one address — change it to `$fromAI('To', ...)` if you want the agent to send to different recipients on request.
- The **Perception** agent has no `systemMessage` set, so it's running on default behavior. Consider adding a system prompt to define its persona/scope, the way the "Multi agent manage my google" workflow does.
- There's no explicit "reply" step — the chat trigger returns the agent's output directly to the chat UI, so no additional node is needed there.
