# Fonema API Overview

Welcome to the Fonema API! This service allows you to programmatically initiate phone calls and receive call‐end webhooks containing details about the conversation. Below is a high‐level overview of the endpoints and how to use them.

## Authentication

* **AuthorizationUser your earer token**

```bash
Authorization: Bearer YOUR_TOKEN
```

***

# Endpoints

## 1. Initiate Call

Use this endpoint to trigger a call to the specified phone number.

**Method:** `POST`
**URL:** [`https://api.fonema.ai/v1/initiate-call`](https://api.fonema.ai/v1/initiate-call)

### Request Body

| Field         | Type     | Required | Description                                          |
| ------------- | -------- | -------- | ---------------------------------------------------- |
| `userName`    | `string` | **Yes**  | The name of the recipient (for logs/records)         |
| `phoneNumber` | `string` | **Yes**  | The phone number to call (include country/area code) |
| `delay`       | `number` | No       | Delay before initiating the call, in seconds         |
| `variables`   | `object` | No       | Additional variables for the call script or logic    |

#### Example Request

```json
{
  "userName": "John Doe",
  "phoneNumber": "5581354511",
  "delay": 30,
  "variables": {
    "additionalVariablesForAgent": "My value"
  }
}
```

### Example Curl

```bash
curl -X POST "https://api.fonema.ai/v1/initiate-call" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "userName": "John Doe",
    "phoneNumber": "5581354511",
    "delay": 30,
    "variables": {
      "additionalVariablesForAgent": "My value"
    }
  }'
```

### Response

```json
{
  "status": "ok",
  "callId": "someUniqueCallIdentifier",
  "estimatedStartTime": "2024-09-24T18:20:22.724Z"
}
```

| Field                | Type   | Description                                               |
| -------------------- | ------ | --------------------------------------------------------- |
| `status`             | string | Indicates if the request was successful                   |
| `callId`             | string | Unique identifier for the call                            |
| `estimatedStartTime` | string | ISO 8601 timestamp for when the call is expected to start |

***

## 2. End Call Webhook

Once a call completes, Fonema sends a webhook request to the URL you specify.

> **Note:** In your screenshot, it’s shown as a `GET` request, but many webhooks are typically `POST`. Adjust accordingly if needed.

**Method:** `GET` or `POST` (depending on your configuration)
**URL:** [`https://your-api.example.com/your-webhook`](https://your-api.example.com/your-webhook)

### Request Body

```json
{
  "id": "cHYqKdGz25Rq",
  "startedAt": "2024-09-24T18:20:22.724Z",
  "messages": [
    {
      "role": "system",
      "message": "You are a helpful assistant"
    },
    {
      "role": "agent",
      "message": "Hello"
    },
    {
      "role": "user",
      "message": "Hi"
    }
  ],
  "durationSeconds": 15,
  "recordingUrl": "https://fonema.ai/api/v1/storage/calls/cHYqKdGz25Rq.mp3",
  "analysis": {
    "successEvaluation": true,
    "structuredData": {
      "myData": "myValueFromCall"
    }
  }
}
```

| Field             | Type   | Description                                                                                            |
| ----------------- | ------ | ------------------------------------------------------------------------------------------------------ |
| `id`              | string | Unique identifier for the call                                                                         |
| `startedAt`       | string | ISO 8601 timestamp for when the call started                                                           |
| `messages`        | array  | An array capturing the conversation flow (roles + text)                                                |
| `durationSeconds` | number | Duration of the call in seconds                                                                        |
| `recordingUrl`    | string | URL to the call recording (if available)                                                               |
| `analysis`        | object | Optional analysis results. May contain custom fields like `successEvaluation` and any structured data. |

#### Handling the Webhook

When you receive this request at your configured URL, process the payload for logging, analytics, or business logic. For example:

```js
// Example Express.js handler:
app.get('/your-webhook', (req, res) => {
  const {
    id,
    startedAt,
    messages,
    durationSeconds,
    recordingUrl,
    analysis
  } = req.body;

  // Process or store the data as needed...
  console.log(`Call ${id} ended. Recording: ${recordingUrl}`);

  // Return a success response
  res.status(200).send({ status: 'received' });
});
```

***

# Next Steps

* **Error handling**

  : Document any HTTP error codes or error responses your API might return (e.g., 400, 401, 404).

* **Security**

  : Protect your webhook endpoint from unauthorized requests (e.g., signature verification).

* **Further customizations**

  : If you have advanced use cases (e.g., passing more complex

  `variables`

  ), expand your docs to include those.

```
```