# ReverseProxy

### Prerequisites:
- I had an AWS S3 bucket with a static website configured.
- I had an Nginx server (or another web server) set up and running on a Linux server.
- My S3 bucket was publicly accessible for serving the static content (e.g., HTML, CSS, JavaScript, and images).

### Step 1: Set Up My Amazon S3 Bucket for Static Website Hosting

1. **Logged in to AWS S3 Console**:
   - I went to the [Amazon S3 console](https://console.aws.amazon.com/s3/).

2. **Created an S3 Bucket** (if I didn’t have one already):
   - I clicked `Create bucket`.
   - I provided a unique bucket name (e.g., `my-static-website-bucket`).
   - I chose a region and followed the rest of the setup steps.

3. **Enabled Static Website Hosting**:
   - I opened the bucket I had just created.
   - I went to the **Properties** tab.
   - I scrolled down to **Static website hosting** and enabled it.
   - I set the **Index document** to `index.html` (or whatever my home page was named).
   - I set the **Error document** (optional) to `error.html`.
   - I noted the **Endpoint URL** (e.g., `http://my-static-website-bucket.s3-website-us-east-1.amazonaws.com`).

4. **Uploaded My Website Files**:
   - I went to the **Objects** tab in the S3 bucket.
   - I uploaded my static files (HTML, CSS, JavaScript, images, etc.).

5. **Set Permissions**:
   - I ensured that my files were publicly accessible by setting proper permissions.
   - I clicked on each file in my bucket and ensured that "Public Read" permission was granted.

### Step 2: Set Up Nginx as a Reverse Proxy

1. **Installed Nginx** on my server:
   - If Nginx wasn’t installed on my server yet, I installed it with:
     ```bash
     sudo apt update
     sudo apt install nginx
     ```

2. **Configured Nginx for Reverse Proxy**:
   - I edited the Nginx configuration file to set up a reverse proxy that pointed to my S3 static website.
   - I opened the Nginx configuration file (`/etc/nginx/sites-available/default` or a new file in `/etc/nginx/sites-available/`).
     ```bash
     sudo nano /etc/nginx/sites-available/default
     ```

3. **Added the Reverse Proxy Configuration**:
   - I added the following configuration to my Nginx config:
   
     ```nginx
     server {
         listen 80;
         server_name yourdomain.com;  # Replaced with my domain or IP address

         location / {
             proxy_pass http://my-static-website-bucket.s3-website-us-east-1.amazonaws.com;  # Replaced with my S3 static website URL
             proxy_set_header Host my-static-website-bucket.s3-website-us-east-1.amazonaws.com;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
         }

         # Optionally handle images separately, if needed
         location /images/ {
             proxy_pass http://my-static-website-bucket.s3-website-us-east-1.amazonaws.com/images/;
             proxy_set_header Host my-static-website-bucket.s3-website-us-east-1.amazonaws.com;
         }
     }
     ```

   - In this configuration:
     - I replaced `yourdomain.com` with my actual domain or IP address.
     - I replaced the `proxy_pass` URL with the URL of my S3 bucket's static website endpoint.
     - The location `/images/` was optional; it was for handling image resources separately if needed.

4. **Enabled the Configuration**:
   - If I created a new file in `/etc/nginx/sites-available/`, I created a symlink to it in `/etc/nginx/sites-enabled/`:
     ```bash
     sudo ln -s /etc/nginx/sites-available/your-site /etc/nginx/sites-enabled/
     ```
   - If I edited the default file, I was good to go.

5. **Tested the Nginx Configuration**:
   - I ran the following command to check if the Nginx config was valid:
     ```bash
     sudo nginx -t
     ```
   - If the output said "syntax is okay" and "test is successful," I proceeded.

6. **Restarted Nginx**:
   - I restarted Nginx to apply the changes:
     ```bash
     sudo systemctl restart nginx
     ```

### Step 3: Test the Reverse Proxy

1. **Accessed the Website**:
   - I opened a web browser and navigated to my domain (e.g., `http://yourdomain.com`).
   - Nginx now served the static website hosted on my S3 bucket.

2. **Checked Image Loading**:
   - I ensured images and other static files were loading correctly. Since I had set up a special location for images, I verified that images were served properly by Nginx through the reverse proxy.

### Step 4: Troubleshooting (if needed)

- **Checked Nginx Logs**:
   - If anything wasn’t working, I checked Nginx’s error log for clues:
     ```bash
     sudo tail -f /var/log/nginx/error.log
     ```

- **Checked S3 Permissions**:
   - I double-checked that my S3 bucket’s permissions were set to allow public access to the static files.

- **DNS Setup**:
   - I ensured that my domain (if I was using one) pointed to the correct server IP in my DNS settings.

### Step 5: Optional - Configured HTTPS (SSL) with Let’s Encrypt

If I wanted to serve my site securely with HTTPS, I set up SSL with Let’s Encrypt:

1. **Installed Certbot**:
   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

2. **Obtained SSL Certificate**:
   ```bash
   sudo certbot --nginx
   ```

   I followed the prompts to configure SSL for my domain.

3. **Set Up Auto-Renewal**:
   Let’s Encrypt certificates need to be renewed every 90 days. Certbot set up a cron job for automatic renewal, but I tested it with:
   ```bash
   sudo certbot renew --dry-run
   ```

### Conclusion

I successfully set up a reverse proxy on my web server using Nginx to serve a static website hosted on Amazon S3, including images. I also had the option to configure SSL for secure access. This setup ensured that my static content was served efficiently while allowing flexibility for future changes.
```
