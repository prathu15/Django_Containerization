# Challenges Faced During Deployment

While deploying my Django application on an Azure Virtual Machine using Docker, I encountered a few practical challenges. Resolving these issues helped me better understand Docker permissions, Python virtual environments, and Azure networking.

---

## 1. Docker Permission Issue

Initially, I was unable to run Docker commands directly. When I tried commands like:

docker ps

I received a permission denied error and had to use `sudo` with every Docker command.

After investigating the issue, I learned that the user needs to be added to the Docker group to run Docker without sudo. I fixed this by running:

sudo usermod -aG docker azureuser

After reconnecting to the VM, Docker commands started working normally without sudo.

---

## 2. Python Virtual Environment Issue

While installing Django using pip, I encountered an error related to an **externally managed Python environment**. This happens because Ubuntu protects the system Python installation.

To resolve this issue, I created a virtual environment for the project:

python3 -m venv venv  
source venv/bin/activate

This allowed me to install Django and other dependencies without affecting the system Python environment.

---

## 3. Allowing Port 8000 in Azure

After successfully running the Docker container, I tried accessing the Django application from the browser, but it did not open. Even though the container was running, the application was not accessible.

I discovered that Azure blocks incoming traffic by default. To solve this, I added an **inbound rule in the Azure Network Security Group (NSG)** to allow traffic on port **8000**.

Once port 8000 was allowed, the Django application became accessible through the VM’s public IP address.

---

![Screenshot](Screenshot%202026-03-16%20101726.png)

DisallowedHost  
Invalid HTTP_HOST header: '135.235.195.205:8000'

This happens because Django only allows requests from hosts listed in the `ALLOWED_HOSTS` setting.

To fix this issue, I edited the Django settings file:

vim myproject/settings.py

and updated the configuration:

ALLOWED_HOSTS = ['*']

After rebuilding and restarting the Docker container, the application became accessible successfully.

![Screenshot](Screenshot%202026-03-16%20101937.png)
