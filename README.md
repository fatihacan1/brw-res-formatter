<p align="center">
  <img src="https://i.ibb.co/LDfLcNZg/brw-res-formatter.jpg" alt="brw-res-formatter banner" width="100%" />
</p>

<h1 align="center">brw-res-formatter</h1>

<p align="center">
  <strong>A lightweight, type-safe response formatter for Express.js applications.</strong>
</p>

<p align="center">
  <a href="https://www.npmjs.com/package/brw-res-formatter"><img src="https://img.shields.io/npm/v/brw-res-formatter?color=cb3837&logo=npm&logoColor=white&label=npm" alt="npm version" /></a>
  <a href="https://www.npmjs.com/package/brw-res-formatter"><img src="https://img.shields.io/npm/dm/brw-res-formatter?color=333333&logo=npm" alt="npm downloads" /></a>
  <a href="https://github.com/scaryBrownie/brw-res-formatter/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT" /></a>
  <a href="https://www.typescriptlang.org/"><img src="https://img.shields.io/badge/TypeScript-Ready-3178c6?logo=typescript&logoColor=white" alt="TypeScript" /></a>
</p>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [API Reference](#api-reference)
  - [sendResponse](#sendresponse)
  - [sendErrorResponse](#senderrorresponse)
  - [sendSuccessResponse](#sendsuccessresponse)
- [Response Format](#response-format)
- [TypeScript Support](#typescript-support)
- [Examples](#examples)
- [Compatibility](#compatibility)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

**brw-res-formatter** standardizes your Express.js API responses into a consistent JSON structure. Instead of manually constructing response objects throughout your codebase, this package provides a clean, predictable interface that ensures every endpoint returns responses in the same format — making both development and client-side consumption significantly easier.

```
┌─────────────────────────────────────────────────────┐
│                  API Response                       │
│                                                     │
│  {                                                  │
│    "success": true | false,                         │
│    "status":  <HTTP Status Code>,                   │
│    "message": "<Human-readable description>",       │
│    "data":    <Payload | null>                      │
│  }                                                  │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## Features

| Feature | Description |
|:--------|:------------|
| 🎯 **Standardized Responses** | Every API response follows the same `{ success, status, message, data }` structure |
| 🔒 **Type-Safe** | Written in TypeScript with full generic support for response data payloads |
| 📦 **Dual Module Support** | Ships both CommonJS and ES Module builds — works everywhere |
| ⚡ **Zero Dependencies** | Express is a peer dependency, not bundled — keeps your `node_modules` lean |
| 🧪 **Fully Tested** | 20 tests covering all functions, edge cases, and response structure validation |
| 📐 **Lightweight** | ~5.6 kB packed — minimal footprint on your project |

---

## Installation

```bash
npm install brw-res-formatter
```

```bash
yarn add brw-res-formatter
```

```bash
pnpm add brw-res-formatter
```

> [!IMPORTANT]
> This package requires **Express.js ≥ 4.0.0** as a peer dependency. Ensure Express is installed in your project.

---

## Quick Start

```typescript
import express from "express";
import { sendSuccessResponse, sendErrorResponse } from "brw-res-formatter";

const app = express();

app.get("/users/:id", (req, res) => {
  const user = { id: 1, name: "John Doe", email: "john@example.com" };
  sendSuccessResponse(res, 200, "User retrieved successfully", user);
});

app.get("/protected", (req, res) => {
  sendErrorResponse(res, 401, "Authentication required");
});

app.listen(3000);
```

That's it. Every response from your API will now follow a predictable, standardized format.

---

## API Reference

### `sendResponse`

The base function used internally by all other functions. Use this when you need full control over the response.

```typescript
sendResponse<T>(res: Response, statusCode: number, success: boolean, message: string, data?: T): void
```

**Parameters:**

| Parameter | Type | Required | Description |
|:----------|:-----|:--------:|:------------|
| `res` | `express.Response` | ✅ | The Express response object |
| `statusCode` | `number` | ✅ | HTTP status code (e.g., `200`, `404`, `500`) |
| `success` | `boolean` | ✅ | Indicates whether the operation succeeded |
| `message` | `string` | ✅ | A human-readable description of the result |
| `data` | `T` | ❌ | Optional payload. Defaults to `null` when omitted |

**Example:**

```typescript
import { sendResponse } from "brw-res-formatter";

app.patch("/users/:id", (req, res) => {
  const updatedUser = { id: 1, name: "Jane Doe" };
  sendResponse(res, 200, true, "User updated", updatedUser);
});
```

---

### `sendErrorResponse`

A convenience wrapper for error responses. Automatically sets `success: false` and `data: null`.

```typescript
sendErrorResponse(res: Response, statusCode?: number, message?: string): void
```

**Parameters:**

| Parameter | Type | Default | Description |
|:----------|:-----|:--------|:------------|
| `res` | `express.Response` | — | The Express response object |
| `statusCode` | `number` | `500` | HTTP error status code |
| `message` | `string` | `"Server error"` | Error description |

**Example:**

```typescript
import { sendErrorResponse } from "brw-res-formatter";

// With custom status and message
app.delete("/users/:id", (req, res) => {
  sendErrorResponse(res, 403, "You do not have permission to delete this user");
});

// With defaults (500, "Server error")
app.get("/data", (req, res) => {
  try {
    // ... some operation
  } catch (error) {
    sendErrorResponse(res);
  }
});
```

---

### `sendSuccessResponse`

A convenience wrapper for success responses. Automatically sets `success: true`.

```typescript
sendSuccessResponse<T>(res: Response, statusCode?: number, message?: string, data?: T): void
```

**Parameters:**

| Parameter | Type | Default | Description |
|:----------|:-----|:--------|:------------|
| `res` | `express.Response` | — | The Express response object |
| `statusCode` | `number` | `200` | HTTP success status code |
| `message` | `string` | `"Operation successful"` | Success description |
| `data` | `T` | `null` | Optional response payload |

**Example:**

```typescript
import { sendSuccessResponse } from "brw-res-formatter";

app.get("/products", (req, res) => {
  const products = [
    { id: 1, name: "Widget", price: 9.99 },
    { id: 2, name: "Gadget", price: 24.99 },
  ];
  sendSuccessResponse(res, 200, "Products retrieved", products);
});

app.post("/orders", (req, res) => {
  const order = { id: 42, status: "confirmed" };
  sendSuccessResponse(res, 201, "Order created successfully", order);
});
```

---

## Response Format

All responses adhere to the `ApiResponse<T>` interface:

```typescript
interface ApiResponse<T = unknown> {
  success: boolean;   // Whether the operation succeeded
  status:  number;    // HTTP status code (mirrors the actual HTTP status)
  message: string;    // Human-readable result description
  data:    T | null;  // Payload data, or null if absent
}
```

### ✅ Success Response

```
HTTP/1.1 200 OK
Content-Type: application/json
```

```json
{
  "success": true,
  "status": 200,
  "message": "Data retrieved successfully",
  "data": {
    "user": {
      "id": 1,
      "name": "John Doe",
      "email": "john@example.com"
    },
    "items": ["item1", "item2", "item3"]
  }
}
```

### ❌ Error Response

```
HTTP/1.1 400 Bad Request
Content-Type: application/json
```

```json
{
  "success": false,
  "status": 400,
  "message": "Validation failed: email is required",
  "data": null
}
```

### Common HTTP Status Codes Reference

| Code | Meaning | Typical Usage |
|:----:|:--------|:--------------|
| `200` | OK | Successful GET, PUT, PATCH |
| `201` | Created | Successful POST (resource created) |
| `204` | No Content | Successful DELETE |
| `400` | Bad Request | Validation errors, malformed input |
| `401` | Unauthorized | Missing or invalid authentication |
| `403` | Forbidden | Authenticated but insufficient permissions |
| `404` | Not Found | Resource does not exist |
| `409` | Conflict | Duplicate resource or state conflict |
| `422` | Unprocessable Entity | Semantic validation errors |
| `429` | Too Many Requests | Rate limit exceeded |
| `500` | Internal Server Error | Unhandled server-side failures |

---

## TypeScript Support

This package is written entirely in TypeScript and ships with full type declarations. You can import the types directly to use in your own code:

```typescript
import type { ApiResponse, ResponseOptions } from "brw-res-formatter";
```

### Generic Type Safety

Use generics to get compile-time type checking on your response data:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

// ✅ TypeScript validates the data matches the User interface
sendSuccessResponse<User>(res, 200, "User found", {
  id: 1,
  name: "John Doe",
  email: "john@example.com",
});

// ❌ TypeScript error: 'age' does not exist in type 'User'
sendSuccessResponse<User>(res, 200, "User found", {
  id: 1,
  name: "John Doe",
  age: 30, // Error!
});
```

### Using `ApiResponse` in Client Code

The exported `ApiResponse` type is useful on the client side for typing API responses:

```typescript
import type { ApiResponse } from "brw-res-formatter";

interface Product {
  id: number;
  name: string;
  price: number;
}

async function fetchProducts(): Promise<ApiResponse<Product[]>> {
  const response = await fetch("/api/products");
  return response.json();
}

const result = await fetchProducts();
if (result.success) {
  console.log(result.data); // Product[] | null — fully typed
}
```

---

## Examples

<details>
<summary><strong>🔐 Authentication Middleware</strong></summary>

```typescript
import { sendErrorResponse } from "brw-res-formatter";

function authMiddleware(req, res, next) {
  const token = req.headers.authorization?.split(" ")[1];

  if (!token) {
    return sendErrorResponse(res, 401, "No token provided");
  }

  try {
    const decoded = verifyToken(token);
    req.user = decoded;
    next();
  } catch {
    sendErrorResponse(res, 401, "Invalid or expired token");
  }
}
```

</details>

<details>
<summary><strong>📝 CRUD Operations</strong></summary>

```typescript
import { sendSuccessResponse, sendErrorResponse } from "brw-res-formatter";

// CREATE
app.post("/api/posts", async (req, res) => {
  try {
    const post = await Post.create(req.body);
    sendSuccessResponse(res, 201, "Post created successfully", post);
  } catch (error) {
    sendErrorResponse(res, 400, "Failed to create post");
  }
});

// READ
app.get("/api/posts/:id", async (req, res) => {
  const post = await Post.findById(req.params.id);
  if (!post) {
    return sendErrorResponse(res, 404, "Post not found");
  }
  sendSuccessResponse(res, 200, "Post retrieved", post);
});

// UPDATE
app.put("/api/posts/:id", async (req, res) => {
  const post = await Post.findByIdAndUpdate(req.params.id, req.body, { new: true });
  if (!post) {
    return sendErrorResponse(res, 404, "Post not found");
  }
  sendSuccessResponse(res, 200, "Post updated", post);
});

// DELETE
app.delete("/api/posts/:id", async (req, res) => {
  const post = await Post.findByIdAndDelete(req.params.id);
  if (!post) {
    return sendErrorResponse(res, 404, "Post not found");
  }
  sendSuccessResponse(res, 200, "Post deleted");
});
```

</details>

<details>
<summary><strong>🛡️ Global Error Handler</strong></summary>

```typescript
import { sendErrorResponse } from "brw-res-formatter";

// Express global error-handling middleware (must have 4 parameters)
app.use((err, req, res, next) => {
  console.error(err.stack);

  const statusCode = err.statusCode || 500;
  const message =
    process.env.NODE_ENV === "production"
      ? "An unexpected error occurred"
      : err.message;

  sendErrorResponse(res, statusCode, message);
});
```

</details>

<details>
<summary><strong>📄 Paginated Response</strong></summary>

```typescript
import { sendSuccessResponse } from "brw-res-formatter";

app.get("/api/users", async (req, res) => {
  const page = parseInt(req.query.page as string) || 1;
  const limit = parseInt(req.query.limit as string) || 10;
  const skip = (page - 1) * limit;

  const [users, total] = await Promise.all([
    User.find().skip(skip).limit(limit),
    User.countDocuments(),
  ]);

  sendSuccessResponse(res, 200, "Users retrieved", {
    users,
    pagination: {
      page,
      limit,
      total,
      totalPages: Math.ceil(total / limit),
    },
  });
});
```

</details>

---

## Compatibility

| Environment | Supported |
|:------------|:---------:|
| Node.js ≥ 16 | ✅ |
| Express ≥ 4.x | ✅ |
| Express 5.x | ✅ |
| CommonJS (`require`) | ✅ |
| ES Modules (`import`) | ✅ |
| TypeScript | ✅ |
| JavaScript | ✅ |

---

## Contributing

Contributions are welcome! To get started:

```bash
# 1. Clone the repository
git clone https://github.com/scaryBrownie/brw-res-formatter.git
cd brw-res-formatter

# 2. Install dependencies
npm install

# 3. Run the test suite
npm test

# 4. Build the package
npm run build
```

Please ensure all tests pass before submitting a pull request.

---

## License

This project is licensed under the [MIT License](LICENSE).

Copyright © 2026 **Fatih Acan**

---

<p align="center">
  Maintained by <a href="https://github.com/fatihacan1">fatihacan1</a>
</p>
