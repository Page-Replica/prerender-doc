# Generate a Preview & What Happens Next

## Generate a Preview

- Click **Generate a preview** in your dashboard.
- You can create a preview once every set interval (every 15 minutes).
- Use the preview link to check how your site will appear when prerendered.

## Submit Request to Prerender

After you submit your request, it is placed in a queue and dispatched to Page Replica's servers for processing. Depending on the number of pages and current server load, rendering all pages may take some time. You will be notified in the dashboard once your pages are ready.

## URL format for cached (prerendered) pages

Once your pages are prerendered, you can access the cached versions using the following URL format:

```
https://cache.page-replica.com/[siteId]/[domain]/[path]
```

- **siteId**: Your unique site identifier
- **domain**: Your domain name (e.g., example.com)
- **path**: The path to your page (e.g., doc/case-study)

For example, a prerendered page for `https://page-replica.com/doc/case-study` would be accessible at:

```
https://cache.page-replica.com/XR55Sns7wFNdjxqz3/page-replica.com/doc/case-study
```
