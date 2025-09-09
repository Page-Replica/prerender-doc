# Meteor.js Bot Traffic Handler Package

Easily serve prerendered content to search engines and social media bots in your Meteor.js app using the official Page Replica package. This improves SEO and sharing by ensuring bots see your site's content, even if it's JavaScript-heavy.

---

## Prerequisites

1. [Sign up for a free Page Replica account](https://page-replica.com) and get your unique site ID.
2. Register your website's domain in the Page Replica dashboard.
3. Request pre-rendering of your website's pages from the dashboard.

---

## Installation

Add the Page Replica package to your Meteor project:

```sh
meteor add pagereplica:prerender
```

---

## Configuration

Check the [Atmosphere package page](https://atmospherejs.com/pagereplica/prerender) for the latest version and options.

Add the following to your Meteor project's `settings.json` file (replace values as needed):

```json
{
  "page_replica": {
    "site_id": "YOUR_SITE_ID",
    "domain": "yourdomain.com",
    "bots": [
      "google",
      "bing",
      "yahoo",
      "baidu",
      "yandex",
      "duckduck",
      "sogou",
      "exabot",
      "facebot",
      "twitter",
      "linkedin",
      "pinterest",
      "telegram",
      "whatsapp",
      "archive.org",
      "ia_archiver",
      "semrush",
      "mj12bot",
      "ahrefs",
      "dotbot",
      "rogerbot",
      "linkdexbot",
      "ltx71",
      "uptimerobot",
      "seznambot",
      "qwant",
      "screaming frog",
      "uptime",
      "semalt",
      "barkrowler",
      "sitebulb",
      "zoominfobot",
      "linkfluence",
      "serpstatbot",
      "netcraftsurvey",
      "siteexplorer",
      "trendictionbot",
      "crawler",
      "python-requests",
      "curl",
      "adsbot-google",
      "mediapartners",
      "propellerads",
      "serpmetrics",
      "buysellads",
      "adsbot",
      "duckduckbot",
      "yisouspider",
      "mail.ru",
      "coccocbot",
      "panscient",
      "magpie-crawler",
      "telegrambot",
      "showyoubot",
      "visionutils",
      "applebot",
      "zyborg",
      "baiduspider",
      "pinterest",
      "httrack",
      "wget"
    ]
  }
}
```

---

## Running Locally

Start your Meteor app with your settings file:

```sh
meteor --settings settings.json
```

---

## Usage

- The package works automatically—no manual NGINX or Cloudflare setup needed.
- It detects bots and fetches prerendered/cached responses from Page Replica for them.
- Human visitors and regular browsers see your normal Meteor app.

---

## Testing Your Implementation

To verify your setup, you can:

- Visit the [Page Replica testing guide](https://page-replica.com/doc/test-config).
- Use a tool like curl to simulate a bot:
  ```sh
  curl -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" https://yourdomain.com
  ```
  You should see the prerendered HTML if everything is working.

---

## Contributing

Contributions are welcome! See the [GitHub repository](https://github.com/Page-Replica/meteorjs-prerender) for details.
