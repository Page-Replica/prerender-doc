# Error Handling

If your request fails, the API will return a JSON object with `success: false` and a `message` field describing the error. Common reasons for failure include:

- Invalid or missing authentication headers
- Invalid URL format
- Exceeding your quota
- Trying to delete URLs that are already processed

Always check the `success` and `message` fields in the response.
