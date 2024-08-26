# ngnix-react

wordpress with apache server and mysql database at specified domain

## Ngnix Setup

### Step 1: Install Nginx

#### Update your package index:

```bash
sudo apt update
```

#### Install Nginx:

```bash
sudo apt install nginx
```

#### Start Nginx:

```bash
sudo systemctl start nginx
```

#### Enable Nginx to start at boot:

```bash
sudo systemctl enable nginx
```

#### Check Nginx status:

```bash
sudo systemctl status nginx
```

You should see output that indicates Nginx is running.

### Step 2: Adjust the Firewall (Optional)

If you're using ufw (Uncomplicated Firewall), you need to allow Nginx:

#### Allow Nginx HTTP and HTTPS traffic:

```bash
sudo ufw allow 'Nginx Full'
```

#### Enable the firewall (if not already enabled):

```bash
sudo ufw enable
```

#### Check the firewall status:

```bash
sudo ufw status
```

### Step 3: Basic Configuration

Default Server Block (Optional) Nginx's default server block is located in `/etc/nginx/sites-available/default.`

#### You can edit this file to change the server's root directory, server name, and other settings:

```bash
sudo nano /etc/nginx/sites-available/default
```

Here's an example of what it might look like:

```nginx
server {
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/html;
    index index.html index.htm index.nginx-debian.html;

    server_name your_domain.com www.your_domain.com;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Test Nginx Configuration: After making changes, test your configuration to ensure there are no syntax errors:

```bash
sudo nginx -t
```

#### Reload Nginx: If the test is successful, reload Nginx to apply the changes:

```bash
sudo systemctl reload nginx
```

### Step 4: Setting Up Additional Server Blocks (Optional)

To host multiple sites on your server, you can set up additional server blocks.

#### Create a new server block file:

```bash
sudo nano /etc/nginx/sites-available/your_domain.com
```

Add your server block configuration:

```nginx
server {
    listen 80;
    server_name your_domain.com www.your_domain.com;

    root /var/www/your_domain.com/html;
    index index.html index.htm index.nginx-debian.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Enable the new server block: Link it to the sites-enabled directory:

```bash
sudo ln -s /etc/nginx/sites-available/your_domain.com /etc/nginx/sites-enabled/
```

#### Test and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

### Step 5: Monitoring and Maintenance

Check Nginx logs:

- Access logs: `/var/log/nginx/access.log`
- Error logs: `/var/log/nginx/error.log`

#### Reload or Restart Nginx after changes:

```bash
sudo systemctl reload nginx
sudo systemctl restart nginx
```

This is a basic setup guide, and Nginx has many advanced features like reverse proxying, load balancing, caching, etc., that you can explore as needed.

## Manual Deployment

### Step 1: Clone the Repository

```bash
  git clone
```

### Step 2: Install Dependencies

```bash
  cd apache-react
  npm install
```

### Step 3: Build the Project

```bash
  npm run build
```

## CI/CD Pipeline Deployment

Below is the sample CI/CD pipeline for deploying the frontend on push to the main branch. The pipeline uses SSH to connect to the server and deploy the code.

```yaml
name: 🚀 Deploy frontend on push

on:
  push:
    branches:
      - main

jobs:
  web-deploy:
    name: 🎉 Deploy
    runs-on: ubuntu-latest

    steps:
      - name: 🚚 Get latest code
        uses: actions/checkout@v3

      - name: Use Node.js 20
        uses: actions/setup-node@v2
        with:
          node-version: "20"

      - name: 🔨 Install Packages and Build
        run: |
          npm install
          npm run build

      - name: Set up SSH
        uses: webfactory/ssh-agent@v0.7.0
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: Deploy to Azure VM
        run: |
          ssh -o StrictHostKeyChecking=no azureuser@serverip << 'EOF'
          cd path/for/ngnix-react/
          git pull origin main
          pnpm install
          pnpm build
          sudo systemctl restart apache2
          EOF
```

## Conclusion

You have successfully set up Apache on your server and configured it to serve a website based on a domain name. You can now deploy your website files to the server and access them using your domain name.
