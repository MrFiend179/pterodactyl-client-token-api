# Pterodactyl Admin Client Token Generator

This is a small extension for the [Pterodactyl Panel](https://pterodactyl.io/) that adds an **admin-only API endpoint** for generating client API tokens on behalf of users.

With this feature, administrators can generate tokens using either a **user’s email** or **user ID (UUID)** without requiring the user to log in.

---

## Features

* Generate client tokens for any user by `email` or `user_id`.
* Enforces a maximum of 25 active API keys per user.
* Admin-only endpoint for secure use.
* Returns both the token identifier and secret (plain-text token shown once).

---

## API Endpoint

**URL:**

```
POST /api/application/admin/generate-client-token
```

**Request body (JSON):**

```json
{
  "email": "user@example.com"
}
```

or

```json
{
  "user_id": 42
}
```

**Successful response:**

```json
{
  "identifier": "abcd1234efgh5678",
  "secret_token": "ptlc_XXXXXXXXXXXXXXXXXXXXXXXX"
}
```
> `ptlc` is indicator of client token while `ptla` is indicator of admin token.

**Error responses:**

```json
// User not found
{ "error": "User not found" }

// Too many keys
{ "error": "User has too many API keys" }
```

---

## How Tokens Are Made

Each generated token consists of two parts:

- **Identifier** → A unique reference stored in the database, used internally by Pterodactyl to identify the API key.

- **Secret Token** → The actual API key string (prefixed with ptla_) that is required to authenticate requests. This is only displayed once at creation time and cannot be retrieved later.

Together, these two components form the token record. The identifier is for tracking, while the secret token is what clients use for authentication.

## Example 

node.js
```js
const axios = require('axios');

async function generateClientApiKey(email) {
    try {
        const response = await axios.post(
            'https://<panel-url>/api/application/admin/generate-client-token',
            { email },
            {
                headers: {
                    'Authorization': 'Bearer <your-admin-token>',
                    'Content-Type': 'application/json'
                }
            }
        );

        console.log('Client API Key:', response.data.identifier + response.data.secret_token);
        return response.data;
    } catch (error) {
        console.error('Error generating client API key:', error.response?.data || error.message);
        return null;
    }
}

generateClientApiKey('user@example.com');
```

## Installation

*To be added soon…*

---

## Security Notice

This route should only be available to **administrators**.


---

## License

© Flubel. All rights reserved.
This project is distributed under the **Flubel General Public License (FGPL)**.

Full license text: [FGPL](https://flubel.com/license)
