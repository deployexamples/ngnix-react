# apache-wordpress

wordpress with apache server and mysql database at specified domain

## Apache Installation

### Step 1: Install Apache

```bash
sudo apt update
sudo apt install apache2
```

### Step 2: Verify the Installation

After installing Apache, you can verify that the service is running by typing:

```bash
sudo systemctl status apache2
```

You should see output similar to the following:

```bash
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2021-06-21 15:00:00 UTC; 1min 30s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 12345 (apache2)
      Tasks: 55 (limit: 1137)
     Memory: 6.0M
     CGroup: /system.slice/apache2.service
             ├─12345 /usr/sbin/apache2 -k start
             ├─12346 /usr/sbin/apache2 -k start
             └─12347 /usr/sbin/apache2 -k start
```

The output shows that Apache is active and running. The "active (running)" part of the output indicates that Apache is running. If Apache is not running, you can start it using:

```bash
sudo systemctl start apache2
```

### Step 3: Configure Apache to Start on Boot

To ensure that Apache starts automatically when your server boots, you can enable the service using the following command:

```bash
sudo systemctl enable apache2
```

## Configure Apache

To configure Apache to serve a website based on a domain name, you'll need to set up a Virtual Host. Here's how you can do it on Ubuntu:

### Step 1: Enable the rewrite module (optional but recommended)

This module allows URL rewriting, which is often needed for domain-based routing.

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

### Step 2: Create a Virtual Host Configuration File

#### Navigate to the sites-available directory:

```bash
cd /etc/apache2/sites-available/
```

#### Create a new configuration file for your domain:

- #### Replace yourdomain.com with your actual domain name.

```bash
sudo nano yourdomain.com.conf
```

- #### Add the following content to the file:

```apache
<VirtualHost \*:80>
ServerAdmin admin@yourdomain.com
ServerName yourdomain.com
ServerAlias www.yourdomain.com

    DocumentRoot /var/www/yourdomain.com

    <Directory /var/www/yourdomain.com>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/yourdomain.com_error.log
    CustomLog ${APACHE_LOG_DIR}/yourdomain.com_access.log combined

</VirtualHost>
```

- ServerAdmin: Your email address for administrative purposes.
- ServerName: The primary domain name.
- ServerAlias: Additional domain names (e.g., www.yourdomain.com).
- DocumentRoot: The directory where your website files are located.

### Step 3: Create the Document Root Directory

#### Create the directory:

```bash
sudo mkdir -p /var/www/yourdomain.com
```

#### Set the appropriate permissions:

```bash
sudo chown -R $USER:$USER /var/www/yourdomain.com
sudo chmod -R 755 /var/www/yourdomain.com
```

#### Add an index.html file (for testing):

```bash
echo "<html><h1>Welcome to yourdomain.com</h1></html>" | sudo tee /var/www/yourdomain.com/index.html
```

### Step 4: Enable the Virtual Host Configuration

#### Enable the new virtual host:

```bash
sudo a2ensite yourdomain.com.conf
```

#### Disable the default virtual host (optional but recommended):

```bash
sudo a2dissite 000-default.conf
```

#### Restart Apache to apply the changes:

```bash
sudo systemctl restart apache2
```

### Step 5: Update DNS Records

Make sure your domain's DNS records point to your server's IP address. This is done through your domain registrar's control panel.

### Step 6: Test the Configuration

Open a web browser and go to http://yourdomain.com. You should see the "Welcome to yourdomain.com" message you added in the index.html file.
If you need to use SSL (HTTPS), you can obtain and configure an SSL certificate using Let's Encrypt with Certbot. This is often required for modern web standards.

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

### Step 4: Deploy the Project

#### Change Directory and Remove Files:

```bash
  cd /var/www/apache-react.bimash.com.np/
  rm -rf *
```

or

#### Remove the existing files and Create a new Directory:

```bash
  sudo rm -rf /var/www/yourdomain.com/*
  sudo mkdir -p /var/www/yourdomain.com/
```

#### Move the Build Files to the Directory:

```bash
  sudo mv your/path/of/apache-react/dist/* /var/www/yourdomain.com/
  sudo systemctl restart apache2
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
          ssh -o StrictHostKeyChecking=no azureuser@20.51.207.28 << 'EOF'
          cd path/for/apache-react/
          git pull origin main
          pnpm install
          pnpm build
          sudo rm -rf /var/www/<yourdomain.com>/
          sudo mkdir -p /var/www/<yourdomain.com>/
          cd
          sudo mv path/for/apache-react/dist/* /var/www/<yourdomain.com>/
          sudo systemctl restart apache2
          EOF
```

## Conclusion

You have successfully set up Apache on your server and configured it to serve a website based on a domain name. You can now deploy your website files to the server and access them using your domain name.
