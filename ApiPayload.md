# Request & Response Structure

## Request Payload

Send a JSON array of URLs in the request body for both POST and DELETE methods. Example:

```json
["https://example.com/page1", "https://example.com/page2"]
```

## Response Structure

All responses are JSON objects with the following fields:

- `success` (`boolean`): Whether the request was successful
- `message` (`string`): A message describing the result
- `urls` (`array`): List of processed URLs
- `quota` (`number`): Your remaining quota

## Quota Management

- Each POST request checks your quota and only processes as many URLs as allowed.
- If you submit more URLs than your quota, only up to your quota will be processed.
- Quota decreases with each successfully queued URL.
- DELETE requests restore quota for each URL successfully removed from the queue.
- You can view your current quota in the API response or your dashboard.
