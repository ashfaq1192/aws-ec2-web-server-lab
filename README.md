Here is the complete, comprehensive `README.md` tailored for a cloud architecture portfolio. It documents the exact steps you took, includes the application code, and explains the underlying AWS networking concepts you mastered along the way.

You can copy and paste everything inside the code block below directly into your `README.md` file.

```markdown
# AWS EC2 Web Server & Network Debugging Lab

**Author:** Muhammad Ashfaq  
**Objective:** Provision an internet-facing Linux web server on AWS, configure stateful network firewalls, and demonstrate failure domain isolation during troubleshooting.

---

## 🏗️ Architecture & Infrastructure

*   **Compute:** Amazon EC2 instance running Ubuntu Linux.
*   **Web Server:** Apache HTTP Server (`apache2`).
*   **Networking:** Deployed within a Public Subnet inside an AWS Virtual Private Cloud (VPC) with an auto-assigned Public IPv4 address.
*   **Security:** Guarded by an AWS Security Group acting as an instance-level firewall.

---

## 🧠 Core Cloud Concepts

Before diving into the deployment steps, it is critical to understand the infrastructure primitives at play:

*   **Security Groups vs. OS Firewalls:** AWS manages inbound and outbound traffic at the hypervisor level via Security Groups. While the Ubuntu OS has its own firewall (`ufw`), it is standard practice in AWS to leave `ufw` inactive and rely entirely on Security Groups for centralized network security.
*   **CIDR Notation (`0.0.0.0/0`):** This notation represents the entire IPv4 internet. Applying this as a source rule means the server will accept traffic from any public IP address.
*   **Daemon Management (`systemd`):** Enterprise Linux distributions require a system manager to handle background services. Registering Apache with `systemd` ensures the web server automatically starts if the EC2 instance reboots.
*   **Failure Domain Isolation:** When a web application fails to load, architects must test network boundaries sequentially—starting from the internal application layer (`localhost`) and moving outward to the external AWS firewall boundary.

---

## 🚀 Deployment Runbook

### Step 1: AWS Security Group Configuration
To allow public web traffic to reach the instance, we must explicitly open Port 80.

1. Navigate to the **EC2 Management Console** > **Security Groups**.
2. Select the Security Group attached to the EC2 instance.
3. Edit **Inbound Rules** and add the following:
   * **Type:** `HTTP`
   * **Port Range:** `80`
   * **Source:** `Anywhere-IPv4` (`0.0.0.0/0`)
4. Save the rules.

### Step 2: Server Provisioning & Initialization
Connect to the EC2 instance via SSH and execute the following commands to install and start the Apache web server.

```bash
# Update the Ubuntu package lists to ensure we pull the latest software versions
sudo apt update -y

# Install the Apache web server
sudo apt install apache2 -y

# Start the Apache service and enable it to launch automatically on system boot
sudo systemctl enable apache2 --now

```

### Step 3: Directory Permissions

By default, the Apache web directory (`/var/www/html`) is owned by the `root` user. We must adjust permissions so the standard `ubuntu` user can modify the website files.

```bash
# Add the 'ubuntu' user to the 'www-data' group (the default Apache user group)
sudo usermod -a -G www-data ubuntu

# Change ownership of the web directory to the 'ubuntu' user and 'www-data' group
sudo chown -R ubuntu:www-data /var/www/html

# Grant write permissions to the group
sudo chmod -R 2775 /var/www/html

```

### Step 4: Application Deployment

Replace the default Apache welcome page with custom application code. Create a new file or edit the existing `index.html`:

```bash
nano /var/www/html/index.html

```

**`src/index.html`**

```html
<!DOCTYPE html>
<html>
  <head>
    <title>AWS Web Server</title>
    <style>
      body {
        font-family: Arial, sans-serif;
        text-align: center;
        padding-top: 50px;
        background-color: #f4f6f9;
      }
      h1 { color: #232f3e; }
      p { color: #555; }
    </style>
  </head>
  <body>
    <h1>Hello from my AWS EC2 Web Server!</h1>
    <p>Deployed successfully on Ubuntu with Apache.</p>
  </body>
</html>

```

---

## 🔍 Troubleshooting & Network Isolation

During deployment, if the browser returns a "Site cannot be reached" error, use the following command-line methodology to isolate the root cause.

**Test 1: Internal Loopback**
Verify if the application is running correctly locally, bypassing AWS networking.

```bash
curl http://localhost

```

> *Result Analysis:* If the HTML is returned, the Apache server and OS are functioning perfectly. The failure domain is external to the server.

**Test 2: External Network Route**
Verify if traffic can leave the server, hit the internet, and successfully pass back through the AWS Security Group.

```bash
curl -v http://<YOUR_EC2_PUBLIC_IP>

```

> *Result Analysis:* If this command hangs or times out, the AWS Security Group is actively blocking the traffic.

**Common Pitfall (Security Group Mismatch):**
It is common to accidentally edit the *Default* VPC Security Group rather than the specific group attached to the EC2 instance (e.g., `launch-wizard-1`). Always verify the active Security Group ID mapped to the instance via the EC2 Console > Instance Details > Security tab.

```

***

Once you have this committed alongside your `src/index.html` file, you will have a solid, professional artifact. What module or AWS service are we tackling next?

```
