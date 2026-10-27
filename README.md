# 🔐 Secrets API - Full-Stack Authentication & RESTful Architecture

A production-style RESTful API built with **Node.js** and **Express.js** designed to demonstrate and practice the **4 core levels of web API authentication**, pagination patterns, and client consumption via **Axios** and **Postman**.

---

## 📌 Project Overview

This repository provides a complete, self-hosted mock backend inspired by the App Brewery Secrets API. It serves as an educational and practical reference for understanding how authentication mechanisms work on both:
1. **The Server Side (Express.js):** Parsing headers, handling Basic Auth Base64 decoding, verifying API keys, and authorizing Bearer tokens.
2. **The Client Side (Axios & Postman):** Configuring `auth`, custom `headers`, and URL `params` properly.

---

## 🚀 Key Features

* **Authentication Level 1 (Public / No Auth):** Open endpoints accessible by any client without credentials.
* **Authentication Level 2 (Basic Auth):** Standard HTTP `Authorization: Basic <base64>` header verification with automatic base64 decoding on the server.
* **Authentication Level 3 (API Key):** Access-level authorization via custom query parameters (`?apiKey=...`) and HTTP headers (`x-api-key`).
* **Authentication Level 4 (Bearer Token):** Protected resource endpoints using `Authorization: Bearer <token>` tokenization.
* **Dynamic User Credential Lifecycle:** Dedicated `/register`, `/generate-api-key`, and `/get-auth-token` routes.
* **Server-Side Pagination:** Query-based pagination on collections (`?page=1`) with a fixed server limit and informative response envelopes.

---

## 🛠️ Tech Stack

* **Runtime:** Node.js (ES Modules)
* **Framework:** Express.js
* **HTTP Client:** Axios (for testing and integration examples)
* **Middleware:** Body-Parser, Express JSON
* **API Testing:** Postman

---

## ⚙️ Installation & Getting Started

### Prerequisites
* [Node.js](https://nodejs.org/) (v16.0.0 or higher recommended)
* [npm](https://www.npmjs.com/)

### 1. Clone the repository
```bash
git clone https://github.com/your-username/secrets-api.git
cd secrets-api
```

### 2. Install dependencies
```bash
npm install
```

### 3. Start the server
For standard run:
```bash
node index.js
```
Or with auto-restart on changes:
```bash
npx nodemon index.js
```

The server starts at `http://localhost:4000`.

---

## 📖 API Documentation & Endpoints

### 1. Authentication Lifecycle Endpoints

#### `POST /register`
Register a new user account.
* **Body:** `x-www-form-urlencoded` or `application/json`
  ```json
  {
    "username": "developer",
    "password": "securepassword123"
  }
  ```
* **Response (`201 Created`):**
  ```json
  {
    "message": "User registered successfully! You can now generate an API key at GET /generate-api-key",
    "username": "developer"
  }
  ```

#### `GET /generate-api-key`
Generate an API Key for an existing registered user.
* **Auth:** Basic Auth (`developer:securepassword123`) OR Query (`?username=...&password=...`)
* **Response (`200 OK`):**
  ```json
  {
    "message": "API key generated successfully!",
    "username": "developer",
    "apiKey": "api-key-k9x2m4p8"
  }
  ```

#### `GET /get-auth-token`
Issue an active Bearer Token for an existing registered user.
* **Auth:** Basic Auth, Query parameters, or Request Body
* **Response (`200 OK`):**
  ```json
  {
    "message": "Bearer Token generated successfully!",
    "username": "developer",
    "token": "bearer-token-9xk3m2p8d7",
    "tokenType": "Bearer"
  }
  ```

---

### 2. Protected Data Endpoints

#### 🟢 Level 1: No Authentication
**`GET /random`**
* Retrieve a random secret. Open to anyone.
* **Response:**
  ```json
  {
    "id": 1,
    "secret": "Jack Bauer's favorite programming language is raw binary.",
    "score": 5,
    "username": "jackbauer",
    "timestamp": "2024-01-01"
  }
  ```

---

#### 🟡 Level 2: Basic Authentication
**`GET /all?page=1`**
* Retrieve paginated secrets (6 items per page).
* **Headers:** `Authorization: Basic <Base64-encoded user:pass>`
* **Query Parameters:** `page` (optional, defaults to `1`)
* **Response (`200 OK`):**
  ```json
  {
    "pagination": {
      "currentPage": 1,
      "limit": 6,
      "totalItems": 25,
      "totalPages": 5,
      "hasNextPage": true,
      "hasPrevPage": false
    },
    "data": [ ... ]
  }
  ```

---

#### 🟠 Level 3: API Key Authentication
**`GET /filter?score=7&apiKey=<YOUR_API_KEY>`**
* Filter secrets with a score $\ge$ query value.
* **Headers / Query:** Pass via `?apiKey=...` or Header `x-api-key: <key>`
* **Response (`200 OK`):** Returns array of matching secret items.

---

#### 🔴 Level 4: Bearer Token Authentication
**`GET /secrets/:id`**
* Retrieve a single secret by its resource ID.
* **Headers:** `Authorization: Bearer <YOUR_BEARER_TOKEN>`
* **Response (`200 OK`):** Single secret object.

**`POST /secrets`**
* Create a new secret attached to the authenticated user.
* **Headers:** `Authorization: Bearer <YOUR_BEARER_TOKEN>`
* **Body (`application/json`):**
  ```json
  {
    "secret": "My custom secret message",
    "score": 9
  }
  ```
* **Response (`201 Created`):** Newly created secret object with assigned `id`.

---

## 💻 Client Integration Examples (Axios)

### 1. Basic Auth with Axios
```javascript
import axios from "axios";

const response = await axios.get("http://localhost:4000/all", {
  auth: {
    username: "developer",
    password: "securepassword123"
  },
  params: {
    page: 2 // Server paginates 6 items per page
  }
});

console.log(response.data);
```

### 2. API Key with Axios
```javascript
const response = await axios.get("http://localhost:4000/filter", {
  params: {
    apiKey: "api-key-k9x2m4p8",
    score: 8
  }
});

console.log(response.data);
```

### 3. Bearer Token with Axios
```javascript
const response = await axios.get("http://localhost:4000/secrets/2", {
  headers: {
    Authorization: `Bearer bearer-token-9xk3m2p8d7`
  }
});

console.log(response.data);
```

---

## 🧪 Testing with Postman

1. **Importing / Setting up Requests:**
   * Create a collection named `Secrets API`.
   * Under the **Authorization** tab, select the auth type corresponding to the route:
     * `Basic Auth` for `/all`, `/generate-api-key`, `/get-auth-token`.
     * `API Key` (Add to `Query Params`) for `/filter`.
     * `Bearer Token` for `/secrets/:id` and `POST /secrets`.
2. **Path vs Query Parameters:**
   * Use path variables (`/secrets/2`) to fetch specific resources.
   * Use query variables (`/all?page=1`) for pagination and filtering.

---

## 🏷️ Suggested GitHub Repository Metadata

* **Repository Name:** `secrets-api-authentication` or `rest-api-authentication-lab`
* **Short Description:** *A complete Node.js & Express RESTful API showcasing the 4 core levels of web authentication (No Auth, Basic Auth, API Key, Bearer Token), pagination, and Axios consumption.*
* **GitHub Topics / Tags:**
  `nodejs` `expressjs` `rest-api` `api-authentication` `basic-auth` `api-key` `bearer-token` `axios` `postman` `backend` `fullstack` `web-development`

---

## 📜 License
This project is open-source and available under the [MIT License](LICENSE).
