# Bot Redirection: Why & How

## Why redirect bots?

Search engines and social media bots often struggle to render JavaScript-heavy websites. By redirecting these bots to a static, prerendered version of your site (hosted on Page Replica), you ensure:

- Better SEO and indexing
- Correct previews on social media
- Faster response times for bots

## How does it work?

1. **Detect bot traffic** using the User-Agent header (e.g., Googlebot, Bingbot, Facebook, Twitter, etc.).
2. **Redirect bots** to the prerendered cache URL (e.g., `https://cache.page-replica.com/[siteId]/[domain]/[path]`).
3. **Serve normal users** your regular dynamic site as usual.

## Implementation examples

- [NGINX configuration](Nginx.md): Step-by-step guide for detecting bots and redirecting them to Page Replica cache.
- [Cloudflare Workers](Cloudflare.md): Example for serverless environments.
- [Meteor.js](Meteor.md): Example for Meteor-based apps.

## Best practices

- Keep your bot detection rules up to date.
- Test your configuration with real bot User-Agents.
- Never redirect real users to the prerendered cache.
- Monitor your logs for unexpected bot or user behavior.

For detailed NGINX, Cloudflare, or Meteor.js setup, see the linked guides above.
