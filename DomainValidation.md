# Verify Domain Ownership

After adding your domain, you need to prove that you own it. This is required to prevent unauthorized use and ensure security.

**How to validate your ownership:**

1. Page Replica will provide a verification method (such as adding a DNS record, uploading a file, or adding a meta tag to your site).
2. Follow the instructions for your chosen method.
3. Once completed, click "Validate" in your dashboard.
4. Page Replica will check for the verification and confirm your ownership.

---

## Step-by-step validation process

### **Option 1: File Upload Validation**

1. **Create a TXT file**  
   Name the file:

```
page-replica.txt
```

2. **Add your validation token**  
   Copy and paste the provided token into the file:

```
[your public token here]
```

3. **Upload the file**  
   Place `page-replica.txt` in the root directory of your website.  
   The file should be accessible at:

```
https://yourdomain.com/page-replica.txt
```

4. **Complete validation**  
   Once the file is uploaded, return to the dashboard and click the **Validate** button.  
   If you wish to remove the site, click **Delete**.

---

### **Option 2: DNS Record Validation**

1. **Add a TXT record to your DNS settings**  
   Log in to your domain registrar or DNS provider and navigate to the DNS settings.  
   Create a new TXT record with the following details:

   - **Host/Name**: `@` or your domain name (e.g., `yourdomain.com`)
   - **Type**: `TXT`
   - **Value**: `[your public token here]`
   - **TTL**: Default or 3600 seconds

2. **Save the changes**  
   Once the TXT record is added, save the changes in your DNS settings.

3. **Complete validation**  
   Return to the dashboard and click the **Validate** button.  
   If you wish to remove the site, click **Delete**.

---

If there are any errors, they will be displayed in the dashboard.  
After successful validation, you can start creating requests to prerender your pages with Page Replica pre-rendering service.
