# Add URLs to Queue (`POST`)

Use the POST method to add URLs to the pre‑rendering queue. The API processes valid URLs and enqueues them, up to your quota. Only absolute URLs under your validated domain are accepted.

## Example: Using Curl

```bash
curl -X POST \
  -H "x-secret-token: SITE_SECRET_TOKEN" \
  -H "x-site-id: SITE_ID" \
  -H "Content-Type: application/json" \
  -d '["https://example.com/page1", "https://example.com/page2"]' \
  'https://your-app-domain.com/api/request/'
```

## Example: Using Axios

```javascript
const axios = require("axios");
axios
  .post(
    "https://your-app-domain.com/api/request/",
    ["https://example.com/page1", "https://example.com/page2"],
    {
      headers: {
        "x-secret-token": "SITE_SECRET_TOKEN",
        "x-site-id": "SITE_ID",
        "Content-Type": "application/json",
      },
    },
  )
  .then((response) => {
    console.log("Success:", response.data.success);
    console.log("Message:", response.data.message);
    console.log("Processed URLs:", response.data.urls);
    console.log("Updated Quota:", response.data.quota);
  })
  .catch((error) => {
    console.error("Error:", error.response.data.message);
  });
```

## Example: Using fetch API

```javascript
fetch("https://your-app-domain.com/api/request/", {
  method: "POST",
  headers: {
    "x-secret-token": "SITE_SECRET_TOKEN",
    "x-site-id": "SITE_ID",
    "Content-Type": "application/json",
  },
  body: JSON.stringify([
    "https://example.com/page1",
    "https://example.com/page2",
  ]),
})
  .then((response) => response.json())
  .then((data) => {
    console.log("Success:", data.success);
    console.log("Message:", data.message);
    console.log("Processed URLs:", data.urls);
    console.log("Updated Quota:", data.quota);
  })
  .catch((error) => {
    console.error("Error:", error);
  });
```
