# Redirecting Traffic for a Lovable Site Using Cloudflare Workers

This guide explains how to set up a **Cloudflare Worker** to redirect all traffic to your Lovable site (e.g., `your-lovable-site.lovable.app`), serve prerendered HTML to bots, and ensure media files always load correctly.

---

## Prerequisites

* **Domain Managed by Cloudflare**
  Your domain must be managed by Cloudflare. If it isn’t yet, transfer your domain’s DNS management to Cloudflare.

* **No Paid Plan Required**
  You don’t need a paid Cloudflare or Lovable plan to complete this setup.

---

## Why Use This Setup?

* Ensure bots receive **prerendered HTML** for better SEO and social media previews.
* Proxy all traffic to your Lovable site for **consistent rendering and resource loading**.
* Allow static and media files to load directly without redirect issues.

**Scope**

* This Worker runs on your domain (e.g., `mydomain.com`).
* It detects bots and serves prerendered pages from **Page Replica**, while proxying other requests to your Lovable site (e.g., `your-lovable-site.lovable.app`).

---

## 1. Create a Cloudflare Worker

### Step 1 — Log in to Cloudflare

* Go to the [Cloudflare dashboard](https://dash.cloudflare.com) and log in.

### Step 2 — Create a Worker

* In the sidebar, go to **Workers & Pages**.
* Click **Create Application** → **Create Worker**.
* Give your Worker a name (e.g., `lovable-redirect-worker`).
* Click **Deploy**, then open it in the **Editor**.

### Step 3 — Replace the Default Code

Click **Edit code**, then replace the default script with the following:

```js
export default {
  /**
   * Cloudflare Worker that proxies all traffic to your Lovable site,
   * serves prerendered HTML to bots, and ensures media files always load correctly.
   */

  async fetch(request, env, ctx) {
    const SITE_ID = "your-page-replica-site-id"; // Replace with your Page Replica Site ID
    const targetHost = "your-lovable-site.lovable.app"; // Replace with your Lovable site subdomain

    // --- Bot User Agents ---
    const botUserAgents = [
      "googlebot", "bingbot", "slurp", "duckduckbot", "baiduspider",
      "yandexbot", "sogou", "exabot", "facebot", "ia_archiver",
      "googleother", "google-inspectiontool", "mediapartners-google",
      "adsbot-google-mobile", "adsbot-google", "google-sitemaps",
      "google-favicon", "google-image", "bingpreview", "propellerads",
      "twitterbot", "linkedinbot", "pinterestbot", "telegram",
      "applebot", "semrushbot", "mj12bot", "dotbot", "ahrefsbot",
      "rogerbot", "linkdexbot", "uptimerobot", "ltx71", "archive.org_bot",
    ];

    // --- Static / Media File Extensions ---
    const mediaExtensions = [
      ".jpg", ".jpeg", ".png", ".gif", ".bmp", ".svg", ".webp", ".ico",
      ".tiff", ".tif", ".mp4", ".mp3", ".wav", ".avi", ".mov", ".mkv",
      ".flv", ".wmv", ".css", ".xml", ".json",
    ];

    // --- Request Analysis ---
    const url = new URL(request.url);
    const userAgent = request.headers.get("User-Agent") || "";
    const isMediaFile = mediaExtensions.some(ext =>
      url.pathname.toLowerCase().endsWith(ext)
    );
    const isBot = botUserAgents.some(bot =>
      userAgent.toLowerCase().includes(bot)
    );

    // --- Serve prerendered HTML for bots ---
    if (isBot && !isMediaFile) {
      const domainName = url.hostname;
      const prerenderUrl = `https://cache.page-replica.com/${SITE_ID}/${domainName}${url.pathname}`;
      const response = await fetch(prerenderUrl);
      return new Response(response.body, {
        status: response.status,
        headers: {
          "Content-Type": response.headers.get("Content-Type") || "text/html",
        },
      });
    }

    // --- Proxy normal users and media requests ---
    url.hostname = targetHost;
    return fetch(url.toString(), {
      headers: request.headers,
      method: request.method,
      body: request.body,
      redirect: "follow",
    });
  },
};
```

---

## 2. Deploy the Worker

* Click **Deploy** to publish your Worker.
* Confirm that it shows a “Deployed successfully” message.

---

## 3. Add a Route to the Worker

### Step 1 — Open Your Worker Settings

* From the Cloudflare dashboard, go to **Workers & Pages**.
* Select your Worker (e.g., `lovable-redirect-worker`).
* Click on the **Settings** tab.

### Step 2 — Add a Custom Domain

* In the **Domains & Routes** section, click **+ Add**.
* On the right panel:
  - Select **Custom Domain**.
  - In the **Domain** field, enter your domain (e.g., `mydomain.com`).
* Click **Add Domain** to save.

---

## Troubleshooting & Tips

* **Did you replace `your-page-replica-site-id`?**
  Be sure to use your actual Site ID from your **Page Replica dashboard**.

* **Worker not triggering?**
  Double-check your route pattern (e.g., `mydomain.com/*`) and confirm the Worker is active on that domain.

* **Static or media files not loading?**
  The Worker passes through all common media file types.
  You can add more extensions to the `mediaExtensions` array if needed.

---

## 4. Test Your Worker

You can test if the Worker is correctly serving prerendered HTML to bots by running:

```sh
curl -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" https://yourdomain.com/page
```

If everything is working, you’ll receive the prerendered HTML from **Page Replica’s cache**.