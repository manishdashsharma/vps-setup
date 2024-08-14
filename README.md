
### Comprehensive Deployment Guide to Ubuntu VPS

This guide is designed for anyone who want to deploy a Dockerized application on an Ubuntu VPS. We will start from the basics, such as logging into your server via SSH, and move step-by-step through setting up your VPS, installing Docker, Docker Compose, setting up SSL with Certbot, and deploying your application using Docker Compose.

#### **Step 1: Access Your VPS via SSH**

Before you can do anything on your VPS, you need to access it. Here’s how to log in:

1. **Open your terminal** (on MacOS or Linux) or use an SSH client like PuTTY (on Windows).
2. **Type the SSH command:**

   ```bash
   ssh your_username@your_server_ip
   ```

   - Replace `your_username` with your actual username on the server.
   - Replace `your_server_ip` with the IP address of your VPS.
   
   You may be prompted to enter your password. Type it in (note that it won’t show on the screen) and press Enter.

#### **Step 2: Update Your Server**

Once logged in, it's crucial to ensure that your server's package list is up-to-date.

1. **Update the package list:**

   ```bash
   sudo apt-get update
   ```

2. **Upgrade the installed packages:**

   ```bash
   sudo apt-get upgrade
   ```

   This command may prompt you to confirm the upgrade. Press `Y` and then Enter to proceed.

3. **Install any missing dependencies:**

   ```bash
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
   ```

   This ensures that your system has all necessary tools to proceed with the installation.

#### **Step 3: Install Docker**

Docker simplifies the deployment of applications by packaging them in containers, which are lightweight, portable, and consistent across environments.

1. **Download Docker’s installation script:**

   ```bash
   curl -fsSL https://get.docker.com -o get-docker.sh
   ```

2. **Run the Docker installation script:**

   ```bash
   sudo sh get-docker.sh
   ```

   This script will automatically detect your system configuration and install the latest version of Docker.

3. **Verify Docker installation:**

   ```bash
   docker --version
   ```

   You should see the Docker version output, confirming that Docker is installed correctly.

4. **Add your user to the Docker group:**

   ```bash
   sudo usermod -aG docker $USER
   ```

   This allows you to run Docker commands without using `sudo`. Log out and back in again to apply this change:

   ```bash
   exit
   ```

   Then, log back into your VPS with SSH.

#### **Step 4: Install Docker Compose**

Docker Compose is a tool for defining and running multi-container Docker applications. You can use it to manage your application stack with a single command.

1. **Download Docker Compose:**

   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.6/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

   This command downloads the specified version of Docker Compose to your system.

2. **Make the Docker Compose file executable:**

   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. **Verify the installation:**

   ```bash
   docker-compose --version
   ```

   You should see the Docker Compose version output, confirming that it’s installed correctly.

#### **Step 5: Set Up SSL with Certbot**

SSL (Secure Sockets Layer) is essential for securing your website. Certbot is a free tool to easily obtain and renew SSL certificates from Let’s Encrypt.

1. **Install Certbot:**

   ```bash
   sudo apt-get install certbot
   ```

2. **Generate an SSL certificate:**

   Replace `www.example.com` with your actual domain:

   ```bash
   sudo certbot certonly --standalone -d www.example.com
   ```

   Follow the prompts to complete the setup. Certbot will automatically verify your domain and install the certificate.

3. **Set up automatic renewal:**

   SSL certificates expire every 90 days, so it’s crucial to renew them automatically:

   ```bash
   sudo certbot renew --dry-run
   ```

   This command simulates the renewal process and ensures everything is set up correctly.

#### **Step 6: Prepare Your Application for Deployment**

1. **Navigate to the home directory:**

   ```bash
   cd /home
   ```

2. **Create a new directory for your application:**

   ```bash
   mkdir your_directory_name
   cd your_directory_name
   ```

   Replace `your_directory_name` with your preferred directory name.

3. **Clone your Git repository:**

   ```bash
   git clone your_repo_url
   ```

   Replace `your_repo_url` with the URL of your Git repository.

4. **Navigate to your project directory:**

   ```bash
   cd your_project_directory
   ```

   Replace `your_project_directory` with the directory where your `docker-compose.yml` file is located.

#### **Step 7: Deploy Your Application with Docker Compose**

1. **Build and start your containers in detached mode:**

   ```bash
   docker-compose up -d --build
   ```

   This command builds your containers and starts them in the background.

2. **If you need to rebuild the containers without starting them:**

   ```bash
   docker-compose build
   ```

3. **To start the containers after they’ve been built:**

   ```bash
   docker-compose up -d
   ```

4. **Verify your deployment:**

   You can check the status of your running containers with:

   ```bash
   docker ps
   ```

   This will list all active containers, confirming that your application is up and running.

#### **Conclusion**

This guide should provide you with a comprehensive understanding of how to deploy a Dockerized application on an Ubuntu VPS, even if you are an absolute novice. By following these steps, you can securely set up your VPS, install Docker and Docker Compose, obtain an SSL certificate, and deploy your application with ease.
