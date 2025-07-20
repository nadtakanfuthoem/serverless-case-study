#Rest API + PostgreSQL

## ✅ Prerequisites

* [AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html)
* Docker (required for `sam local`)
* PostgreSQL installed locally or accessible remotely
* Node.js / Python / other runtime depending on your app

---

## 🛠️ Step 1: Set up your SAM app

You can initialize a project with:

```bash
sam init
```

Choose a runtime (e.g., Node.js, Python, etc.).

---

## 🐘 Step 2: Connect to PostgreSQL in your Lambda function

**Node.js Example (using `pg` package):**

Install dependencies:

```bash
npm install pg
```

In your handler (e.g., `app.js`):

```js
const { Pool } = require('pg');

const pool = new Pool({
  host: process.env.PGHOST,
  user: process.env.PGUSER,
  password: process.env.PGPASSWORD,
  database: process.env.PGDATABASE,
  port: process.env.PGPORT,
});

exports.handler = async (event) => {
  const client = await pool.connect();
  try {
    const res = await client.query('SELECT NOW()');
    return {
      statusCode: 200,
      body: JSON.stringify(res.rows[0]),
    };
  } finally {
    client.release();
  }
};
```

---

## ⚙️ Step 3: Add environment variables

In `template.yaml`:

```yaml
Globals:
  Function:
    Environment:
      Variables:
        PGHOST: localhost
        PGPORT: 5432
        PGUSER: postgres
        PGPASSWORD: your_password
        PGDATABASE: your_db
```

---

## 🧪 Step 4: Run PostgreSQL locally (optional)

If running locally via Docker:

```bash
docker run --name pg -e POSTGRES_PASSWORD=your_password -p 5432:5432 -d postgres
```

---

## ▶️ Step 5: Run Lambda locally and test connection

```bash
sam local invoke MyFunctionName
```

Or use the API:

```bash
sam local start-api
```

Then hit the endpoint (e.g., `http://localhost:3000/hello`) to trigger the function.

---

## 🧩 Common Tips

* **Use `host.docker.internal`** instead of `localhost` for `PGHOST` when Docker is being used on Mac/Windows.
* You can inject env vars with `.env` files or pass `--env-vars env.json` to `sam local invoke`.

---

Would you like a working example project (e.g., with Node.js or Python) set up and ready to test?
