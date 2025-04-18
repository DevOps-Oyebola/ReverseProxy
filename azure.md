# How I Hosted a Static Website on Azure Using Reverse Proxy

This is how I successfully set up a static website hosted on Azure Blob Storage and made it accessible through a virtual machine's public IP address using a reverse proxy. Here's my step-by-step journey:

---

## Step 1: Set Up Static Website Hosting in Azure Blob Storage

1. **Created or Selected a Storage Account**:
   - I logged into the Azure portal and either created a new storage account or used an existing one.

2. **Enabled Static Website Hosting**:
   - I navigated to the **Static Website** section under **Data Management** in the storage account settings.
   - I enabled static website hosting, specified the **index document name** as `index.html`, and chose `404.html` for the error page.

3. **Uploaded My Website Files**:
   - Using the Azure portal, I uploaded my static website files (HTML, CSS, JavaScript) to the `$web` container.

4. **Verified the Website Endpoint**:
   - Azure provided me with a public URL for my static website: `https://boaxyl.z13.web.core.windows.net/index.html`. Here's what my website looked like after the setup:

   ![Static Website Screenshot](https://boaxyl.z13.web.core.windows.net/index.html)

   As you can see, the webpage displayed the lines: _"Hello World!"_ and _"Today is a beautiful World."_ It felt amazing to see my work live!

---

## Step 2: Set Up a Virtual Machine for the Reverse Proxy

1. **Created a Virtual Machine**:
   - I created a virtual machine in Azure, ensuring it had a public IP address.

2. **Opened Firewall Ports**:
   - In the virtual machine's **Network Security Group** settings, I added a rule to allow inbound traffic on port 80.

3. **Logged Into the VM**:
   - Using SSH, I accessed the virtual machine to configure the reverse proxy.

---

## Step 3: Installed and Configured a Web Server

1. **Installed Nginx**:
   - Inside the VM, I installed the Nginx web server by running:
     ```bash
     sudo apt update
     sudo apt install nginx
     ```

2. **Configured Nginx as a Reverse Proxy**:
   - I edited the Nginx configuration file at `/etc/nginx/sites-available/default`. Here’s what I added:
     ```nginx
     server {
         listen 80;
         server_name _;

         location / {
             proxy_pass https://boaxyl.z13.web.core.windows.net;
             proxy_set_header Host boaxyl.z13.web.core.windows.net;
             proxy_http_version 1.1;
         }
     }
     ```

3. **Restarted Nginx**:
   - To apply the changes, I restarted the Nginx service:
     ```bash
     sudo systemctl restart nginx
     ```

---

## Step 4: Tested My Setup

1. **Accessed the Public IP**:
   - I opened my browser and entered the public IP address of the VM. My static website successfully loaded through the VM!

2. **Troubleshooted Issues**:
   - Initially, I encountered errors but fixed them by double-checking my Nginx configuration, Azure Blob Storage settings, and firewall rules.

---

## Step 5: Final Touches

With everything working perfectly, I celebrated my success and considered enhancing the setup by:
- Configuring HTTPS for secure access.
- Mapping a custom domain to my virtual machine.

---

Seeing the webpage live gave me immense satisfaction—it truly felt like a job well done! 😊 Let me know if you want me to tweak or expand this guide further.
