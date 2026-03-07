<div align="center">

# Aadhaar-Validation-API: Free Open-Source Aadhaar Number Verification Library

[![Cloudflare Workers](https://img.shields.io/badge/Cloudflare-Workers-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://workers.cloudflare.com/) 
[![Cloudflare D1](https://img.shields.io/badge/Cloudflare_D1-Serverless_SQL-F38020?style=for-the-badge&logo=cloudflare&logoColor=white)](https://developers.cloudflare.com/d1/)
[![TypeScript](https://img.shields.io/badge/TypeScript-007ACC?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

**A blazing-fast, serverless, and privacy-first API for validating Indian Aadhaar numbers using the Verhoeff algorithm.**

[Live API Docs (Swagger)](https://adhaar-validation-api.solvcraft.workers.dev/docs) &middot; [Report Bug](https://github.com/yourusername/Adhaar-Validation-API/issues) &middot; [Request Feature](https://github.com/yourusername/Adhaar-Validation-API/issues)

</div>

<br />

## 🚀 Overview

The **Aadhaar-Validation-API** is a high-performance open-source library and API built on Cloudflare Workers. It provides developers with a free, reliable, and instant way to mathematically verify 12-digit UIDAI Aadhaar numbers in their applications without relying on slow or paid third-party services.

By utilizing the official **Verhoeff algorithm**, this API ensures that any provided Aadhaar number follows the correct checksum logic. Furthermore, it leverages Cloudflare's D1 serverless database to provide you with rich, privacy-compliant usage analytics (Geo, ASN, IP) with zero impact on response latency.

### Why use this API?
- ⚡ **Zero-Latency Analytics:** Database logging is strictly asynchronous, ensuring your users receive validation responses in milliseconds.
- 🔒 **Privacy First:** We *never* store the full Aadhaar number in the database. Aadhaar numbers are masked (e.g., `********1234`) to ensure compliance with data protection standards.
- 🌍 **Edge Computing:** Deployed on Cloudflare Workers, meaning the API runs on edge nodes closest to the user making the request.
- 📖 **Built-in Swagger:** Try out the API interactively directly from edge deployments.

---

## ✨ Key Features

*   ✅ **Verhoeff Algorithm Validation**: Mathematically verifies the 12-digit Aadhaar Checksum.
*   📊 **Comprehensive Analytics**: Automatically collects rich request metadata (City, Country, Region, ASN, IP, User Agent).
*   📚 **Swagger UI Documentation**: Built-in `/docs` endpoint for interactive API testing.
*   💾 **Cloudflare D1 Integration**: Out-of-the-box SQLite serverless database integration for storing analytics.
*   🛡️ **CORS Enabled**: Ready to be consumed from your frontend web applications.

---

## 🛠️ Installation & Deployment

If you want to host your own instance of the Aadhaar-Validation-API, you can easily deploy it using Wrangler.

### Prerequisites

*   [Node.js](https://nodejs.org/en/) installed (v18+)
*   A Cloudflare account
*   [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/install-and-update/) installed globally (`npm install -g wrangler`)

### Local Development

1. **Clone the repository:**
   ```bash
   git clone https://github.com/yourusername/Adhaar-Validation-API.git
   cd Adhaar-Validation-API
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Initialize the local D1 database:**
   ```bash
   npm run db:init
   ```

4. **Start the local development server:**
   ```bash
   npm run dev
   ```
   The API will now be running on `http://localhost:8787`.

### Deploy to Cloudflare

1. **Create a remote D1 database:**
   ```bash
   npx wrangler d1 create avaa_db
   ```
2. **Update your `wrangler.toml`:** 
   Copy the output block from the previous step (containing the new `database_id`) and replace the existing `[[d1_databases]]` block in your `wrangler.toml`.

3. **Initialize the remote database schema:**
   ```bash
   npx wrangler d1 execute avaa_db --remote --file=./schema.sql
   ```

4. **Deploy the Worker:**
   ```bash
   npm run deploy
   ```

---

## 💻 API Reference & Example Usage

### `POST /` - Validate Aadhaar Number

Validates a 12-digit Aadhaar number sequence.

**Request Body (JSON):**
```json
{
  "aadhaar": "123456789012"
}
```

**Example cURL Request:**
```bash
curl -X POST https://adhaar-validation-api.solvcraft.workers.dev \
     -H "Content-Type: application/json" \
     -d '{"aadhaar": "123456789012"}'
```

**Example Node.js / Fetch Request:**
```javascript
const response = await fetch("https://adhaar-validation-api.solvcraft.workers.dev", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ aadhaar: "678901234567" })
});

const data = await response.json();
console.log(data.isValid); // true or false
```

**Response (JSON):**
```json
{
  "isValid": false,
  "executionTimeMs": 1,
  "rayId": "85f5432ab8912c9c-SIN"
}
```

### `GET /docs` - Swagger UI
Visit the `/docs` or `/swagger` route in your browser to interact with the OpenAPI specification visually.

### `GET /openapi.json`
Retrieve the raw OpenAPI 3.0.0 JSON specification for the API.

---

## 📈 Analytics Database Schema

The API automatically records validation attempts into the Cloudflare D1 database. To query your data, use the Cloudflare dashboard or the Wrangler CLI:

```bash
npx wrangler d1 execute avaa_db --remote --command="SELECT * FROM avaa_db ORDER BY created_at DESC LIMIT 5"
```

**Table: `avaa_db`**
* `id` (INTEGER, PRIMARY KEY)
* `ray_id` (TEXT) - Cloudflare request Ray ID
* `ip_address` (TEXT) - Client IP Address
* `user_agent` (TEXT) - Client User Agent
* `country` (TEXT) - GeoIP Country Code
* `city` (TEXT) - GeoIP City
* `region` (TEXT) - GeoIP Region/State
* `asn` (INTEGER) - Autonomous System Number
* `latitude` (REAL) - Approximate Latitude
* `longitude` (REAL) - Approximate Longitude
* `masked_aadhaar` (TEXT) - Last 4 digits visible (e.g., `********1234`)
* `is_valid` (BOOLEAN) - Validation Result
* `execution_time_ms` (REAL) - Algorithm compute time
* `created_at` (TIMESTAMP)

---

## 🤝 Contributing

Contributions make the open-source community an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📜 License

Distributed under the MIT License. See `LICENSE` for more information.

---
<div align="center">
  <sub>Built with ❤️ using Cloudflare Workers and D1.</sub>
</div>
