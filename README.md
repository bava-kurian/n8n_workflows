# 📨 Order Summary Email Generator (N8N + OpenRouter)

This N8N workflow automatically formats incoming order information into a clean summary email using an AI language model from [OpenRouter](https://openrouter.ai). The response includes a subject and a well-formatted email body, ready to send to your team.

---

## 📦 Features

- Accepts new order data (e.g., via webhook or manual trigger)
- Sends order info to an AI model (like `deepseek-ai/deepseek-chat`) via OpenRouter
- Parses and separates `subject` and `body` from the AI response
- Prepares the email using N8N’s native tools (e.g., Gmail, SMTP, or other integrations)

---

## 🧠 Powered By

- [N8N](https://n8n.io/) — Workflow Automation Platform
- [OpenRouter.ai](https://openrouter.ai) — Unified API for multiple AI models (including free-tier support)
- [DeepSeek Chat](https://deepseek.com/) — High-quality AI model for natural language generation

---

## ⚙️ Setup Instructions

### 1. Clone or Import the Workflow

- Download or copy the `.json` workflow file from this repository
- Go to your N8N instance → Import → Upload `.json`

---

### 2. Create an OpenRouter API Key

1. Sign in at [https://openrouter.ai](https://openrouter.ai)
2. Go to **API Keys**
3. Create a new key and copy it
4. Store it in N8N as an environment variable:  
   `OPENROUTER_API_KEY`

---

### 3. Configure the HTTP Node

Ensure your HTTP Request node has the following:

- **URL**: `https://openrouter.ai/api/v1/chat/completions`
- **Method**: `POST`
- **Headers**:
  ```json
  {
    "Authorization": "Bearer {{$env.OPENROUTER_API_KEY}}",
    "Content-Type": "application/json"
  }
  ```

````

* **Body (RAW JSON)**:

  ```json
  {
    "model": "deepseek-ai/deepseek-chat",
    "messages": [
      {
        "role": "system",
        "content": "You are an assistant that formats client order information into an email. Return only JSON with two fields: subject and body."
      },
      {
        "role": "user",
        "content": "Order 10:\nCustomer Name: John Doe\nProduct: Green Tea\nQuantity: 5\nPrice: $25\nOrder Date: 15 June 2025\nStatus: Pending"
      }
    ]
  }
  ```

---

### 4. Parse AI Response

If the response is a JSON string inside `content`, use a `Function` node:

```js
const raw = $json["choices"][0]["message"]["content"];
const parsed = JSON.parse(raw);

return [{
  json: {
    subject: parsed.subject,
    body: parsed.body
  }
}];
```

---

### 5. Send the Email

Use the `Email`, `Gmail`, or any email-compatible node:

* **Subject**: `{{$json["subject"]}}`
* **Body**: `{{$json["body"]}}`

---

## 🛡️ Security Note

* Never hardcode your API key in the JSON
* Always use environment variables or credentials securely

---

## ✨ Example Use Case

When a new order comes in via a webhook or form, the workflow:

1. Sends the order details to an AI model
2. AI returns:

   ```json
   {
     "subject": "New Order from John Doe - Order #10",
     "body": "Hi Team,\n\nWe received a new order from John Doe for 5 Green Tea packs..."
   }
   ```
3. Sends the email internally to the sales or logistics team

---

## 📂 Files

* `order-summary-email.json` – The main N8N workflow file

---

## 👨‍💼 Created by

**Customer Success Automation Team**

````
