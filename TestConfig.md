# Test Your Setup: How Bots See Your Site

Ensure that Googlebot and other bots see your website as intended. This guide covers three reliable methods: Google Search Console, Chrome DevTools, and curl.

---

## Method 1: Google Search Console

1. **Verify Your Site**

   - Go to [Google Search Console](https://search.google.com/search-console) and verify your site if you haven't already.

2. **Inspect a URL**

   - Enter the URL you want to check in the search bar at the top.
   - Click **Test Live URL**.
   - After the test, click **View Crawled Page**.

3. **Review the Results**
   - In the **Rendered HTML** tab, see how Googlebot sees your page’s HTML.
   - In the **Screenshot** tab, see how Googlebot renders your page visually.

This ensures Googlebot is seeing and indexing your page as expected.

---

---

## Method 2: Emulate Googlebot in Chrome DevTools

1. Open Google Chrome and go to your website.
2. Right-click and select **Inspect** (or press <kbd>Ctrl+Shift+I</kbd> / <kbd>Cmd+Opt+I</kbd>).
3. In DevTools, click the three vertical dots (top right).
4. Go to **More tools** > **Network conditions**.
5. Uncheck **Select automatically** under "User agent".
6. Choose **Googlebot** from the drop-down, or enter the Googlebot user agent manually.

---

---

## Method 3: Using curl to Emulate Googlebot

1. Open your terminal or command prompt and run:

   ```sh
   curl -A "Mozilla/5.0 (compatible; Googlebot/2.1; +http://www.google.com/bot.html)" https://yourdomain.com
   ```

2. Replace `https://yourdomain.com` with the URL you want to inspect.
3. Press <kbd>Enter</kbd> to see the output as Googlebot would.

This is a quick way to fetch a webpage as Googlebot sees it, directly from the command line. Adjust the URL and user agent string as needed for different scenarios.
