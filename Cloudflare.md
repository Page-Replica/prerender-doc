# Redirecting Bots with Cloudflare Workers

Redirecting bot traffic with Cloudflare helps you serve optimized, prerendered content to search engines and social media bots, improving your site's SEO and performance. This guide shows how to set up a Cloudflare Worker to detect bots and redirect them to Page Replica's cache, while letting regular users and static file requests pass through normally.

**Why do this?**

- Bots often can't render JavaScript-heavy sites, which can hurt your SEO and link previews.
- Serving prerendered content to bots ensures your site is indexed and shared correctly.

**Scope:**

- This worker should be deployed on the domain serving your public site.
- Only bot requests for HTML/content are redirected; static/media files are served as usual.

---

## 1. Create a Cloudflare Worker

### Login to Cloudflare

- Log in to your Cloudflare account.

### Navigate to Workers

- In the main navigation menu, click on **Workers & Pages**.
- Click on the **Create** button, then **Create Worker**.

### Set Up Your Worker

- Give your worker a name (e.g., `bot-redirect-worker`).
- Click **Save** and then **Finish**.

### Edit Worker Code

- Find and click on the **Edit code** button.
- Replace the default code with the following script (replace `YOUR_SITE_ID` with your actual Site ID):

```js
/**
 * @typedef {Object} Env
 */

export default {
  /**
   * @param {Request} request
   * @param {Env} env
   * @param {ExecutionContext} ctx
   * @returns {Promise<Response>}
   */
  async fetch(request, env, ctx) {
    const SITE_ID = "YOUR_SITE_ID"; // TODO: Replace with your Site ID
    // List of common bot user agents
    const botUserAgents = [
      // Search Engine Bots
      "googlebot",
      "bingbot",
      "slurp",
      "duckduckbot",
      "baiduspider",
      "yandexbot",
      "sogou",
      "exabot",
      "facebot",
      "ia_archiver",
      // Google Bots
      "googleother",
      "google-inspectiontool",
      "mediapartners-google",
      "adsbot-google-mobile",
      "adsbot-google",
      "google-sitemaps",
      "google-favicon",
      "google-image",
      // Bing Bots
      "bingpreview",
      // Advertising Bots
      "adsbot-google",
      "propellerads",
      "mediapartners-google",
      // Social Media Bots
      "twitterbot",
      "linkedinbot",
      "pinterestbot",
      "telegram",
      // Other Bots
      "applebot",
      "semrushbot",
      "mj12bot",
      "dotbot",
      "ahrefsbot",
      "rogerbot",
      "linkdexbot",
      "uptimerobot",
      "ltx71",
      "archive.org_bot",
    ];
    // List of common media file extensions
    const mediaExtensions = [
      ".jpg",
      ".jpeg",
      ".png",
      ".gif",
      ".bmp",
      ".svg",
      ".webp",
      ".ico",
      ".tiff",
      ".tif",
      ".mp4",
      ".mp3",
      ".wav",
      ".avi",
      ".mov",
      ".mkv",
      ".flv",
      ".wmv",
      ".css",
      ".xml",
      ".json",
    ];
    const url = new URL(request.url);
    const isMediaFile = mediaExtensions.some((ext) =>
      url.pathname.toLowerCase().endsWith(ext),
    );
    // If it's a media file, serve the request as is
    if (isMediaFile) {
      return fetch(request);
    }
    // Check if the User-Agent header contains any bot user agent strings
    const userAgent = request.headers.get("User-Agent") || "";
    const isBot = botUserAgents.some((bot) =>
      userAgent.toLowerCase().includes(bot),
    );
    // If the request is from a bot, fetch the content from another URL and return it
    if (isBot) {
      const domainName = url.hostname;
      const targetUrl = `https://cache.page-replica.com/${SITE_ID}/${domainName}${url.pathname}`;
      const response = await fetch(targetUrl);
      const contentType = response.headers.get("Content-Type");
      return new Response(response.body, {
        status: response.status,
        statusText: response.statusText,
        headers: {
          "Content-Type": contentType,
        },
      });
    }
    // For all other requests, serve the request as is
    return fetch(request);
  },
};
```

---

## 2. Deploy the Worker

- Click on the **Deploy** button to save and deploy the worker.

---

## 3. Add a Route to the Worker

### Select Your Domain

- Go to the dashboard and select your domain.

### Configure the Worker Route

- In the main navigation menu, click on **Workers Routes**.
- Click on the **Add Route** button.
- Select the worker you created from the dropdown list.
- Enter the route you want to apply the worker to (e.g., `example.com/*`).

### Save the Route

- Click on the **Save** button.

---

---

## Troubleshooting & Tips

- **Did you replace `YOUR_SITE_ID`?**
  Make sure to use your actual Site ID from the Page Replica dashboard.
- **Worker not triggering?**
  Double-check your route (e.g., `example.com/*`) and that the worker is deployed to the correct domain.
- **Static files not working?**
  The script is designed to let static/media files pass through. If you have custom file types, add them to the `mediaExtensions` array.

## How to Test Your Worker

You can test if the worker is redirecting bots by using `curl` with a bot user-agent:

```sh
curl -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" https://yourdomain.com/page
```

You should see a response from the Page Replica cache if everything is set up correctly.

---

And that's it! Your Cloudflare Worker is now set up to redirect bot traffic to prerendered content from Page Replica.
