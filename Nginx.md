# Redirecting Bots with NGINX

This tutorial shows how to configure NGINX to detect bot traffic and redirect it to a prerendered page hosted on page-replica.com. This improves performance and SEO by serving optimized content to search engine crawlers.

---

## Prerequisites

- NGINX installed on your server
- Basic knowledge of NGINX configuration

---

## NGINX Configuration

### Step 1: Identify Bot Traffic

Add these `map` blocks to your NGINX config, ideally at the http level (outside any server block). They detect search engine and social network bots by User-Agent and set the `$is_bot` variable to `1` if a match is found:

```nginx
map $http_user_agent $search_engines {
    "~bingbot" 1;
    "~BingPreview" 1;
    "~Googlebot" 1;
    "~GoogleOther" 1;
    "~Google-InspectionTool" 1;
    "~Mediapartners-Google" 1;
    "~AdsBot-Google-Mobile" 1;
    "~AdsBot-Google" 1;
    "~Slurp" 1;
    "~DuckDuckBot" 1;
    "~Baiduspider" 1;
    "~YandexBot" 1;
    "~Sogou" 1;
    "~Exabot" 1;
    "~ia_archiver" 1;
    "~propellerads" 1;
    # Add more search engines here
}
map $http_user_agent $social_networks {
    "~*facebook" 1;
    "~*twitter" 1;
    "~*linkedin" 1;
    "~*reddit" 1;
    "~*telegram" 1;
    # Add more social networks here
}
map $search_engines$social_networks $is_bot {
    ""      "";
    default 1;
}
```

---

---

### Step 2: NGINX Server Block

Add this to your NGINX server block. Replace `example.com` and backend addresses as needed. If you use HTTPS, adjust the `listen` and `proxy_pass` directives accordingly.

```nginx
server {
    listen                *:80;
    server_name           example.com www.example.com;  # TODO: Replace with your domain
    access_log            /var/log/nginx/app.dev.access.log;
    error_log             /var/log/nginx/app.dev.error.log;


    # Static files handling
    location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|xml|json|txt|webp)$ {
        proxy_pass http://127.0.0.1:8082;  # TODO: Replace with your backend server
    }


    location / {
        # Step 3: Redirect Bot Traffic
        if ($is_bot) {
            rewrite (.*) /page-replica last;
            break;
        }
        set $proxyPass http://127.0.0.1:8082;  # TODO: Replace with your backend server
        proxy_pass $proxyPass;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header X-Forwarded-For $remote_addr;
    }

    location /page-replica {
        if ($is_bot = 0) {
            return 404;
        }
        resolver 8.8.8.8 8.8.4.4;  # Define DNS resolver
        set $pageReplicaHost "cache.page-replica.com";
        set $domainName "example.com";  # TODO: Replace with your domain
        set $siteId "your-site-id";  # TODO: Replace with your site ID
        proxy_pass https://$pageReplicaHost/$siteId/$domainName$request_uri;  # TODO: Replace with your cache URL
    }
}
```

---

---

## Explanation

### 1. Identify Bot Traffic

- The `map` blocks set `$is_bot` to `1` for known bots based on User-Agent.
- Place these at the `http` level, not inside a `server` block.

### 2. Main Location Block (`location /`)

- `if ($is_bot)` checks if the request is from a bot.
- If true, `rewrite (.*) /page-replica last;` rewrites the URI and stops further processing.
- `break;` ensures the rewritten URI is used immediately.
- All other requests are proxied to your backend.

### 3. Bot-Specific Location Block (`location /page-replica`)

- Only bots can access this location; others get a 404.
- The request is proxied to the prerendered cache on page-replica.com.

### 4. Static Files Handling

- Static files (images, CSS, JS, etc.) are served directly from your backend.

---

## Tips

- Always test your NGINX configuration before reloading:
  ```sh
  nginx -t
  ```
- Reload NGINX to apply changes:
  ```sh
  sudo systemctl reload nginx
  ```
- If you use HTTPS, adjust the `listen` and `proxy_pass` directives accordingly.

---

## Restart NGINX

After saving the configuration file, remember to restart (or reload) NGINX in order to apply the changes.
