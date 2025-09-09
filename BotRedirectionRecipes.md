# Redirecting Bots with NGINX

In this tutorial, we will configure NGINX to detect bot traffic and redirect it to a prerendered page hosted on page-replica.com. This can improve performance and SEO by serving optimized content to search engine crawlers.

---

## Prerequisites

- NGINX installed on your server
- Basic knowledge of NGINX configuration

---

## NGINX Configuration

### Step 1: Identify Bot Traffic

Add these `map` blocks to your NGINX config to identify search engine and social network bots by User-Agent:

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

### Step 2: NGINX Server Block

Add this to your NGINX server block. Replace `example.com` and backend addresses as needed:

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

## Explanation

### 1. Identify Bot Traffic

- Two `map` blocks identify search engine and social network bots by User-Agent.
- The `$is_bot` variable is set to `1` if the request comes from any identified bot.

### 2. Main Location Block (`location /`)

- The `if ($is_bot)` directive checks if the current request is from a bot.
- If it is a bot, `rewrite (.*) /page-replica last;` rewrites the request URI to `/page-replica`.
- The `last` parameter tells NGINX to stop processing further rewrite directives and search for a new location block.
- `break;` ensures that the request processing is stopped within this block, and the rewritten URI is used.

### 3. Bot-Specific Location Block (`location /page-replica`)

- The `if ($is_bot = 0)` directive ensures that only bot traffic can access this location. If the request is not from a bot, it returns a 404 error.
- The `resolver 8.8.8.8 8.8.4.4;` directive specifies the DNS servers to use for resolving the `page-replica.com` hostname.
- The `set $pageReplicaHost "cache.page-replica.com";` command sets the host for the proxy pass.
- The `proxy_pass https://$pageReplicaHost/$siteId/$domainName$request_uri;` directive proxies the request to the prerendered page hosted on `page-replica.com`, including the original request URI.

### 4. Static Files Handling

- The server block listens on port 80 for HTTP traffic.
- Static files (e.g., images, CSS, JavaScript) are served directly from a backend server (replace `http://127.0.0.1:8082` with your actual backend server).

```nginx
location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|xml|json|txt|webp)$ {
    proxy_pass http://127.0.0.1:8082;  # TODO: Replace with your backend server
}
```

---

## Restart NGINX

After saving the configuration file, remember to restart NGINX in order to apply the changes.
