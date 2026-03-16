Deployment Steps – Django Application on Azure VM using Docker

1. Azure VM Setup
   I connected to my Azure Virtual Machine and updated the system packages.

sudo apt update
sudo apt upgrade -y

2. Install Docker
   I installed Docker to containerize the Django application.

sudo apt install docker.io -y
docker --version

Then I started and enabled the Docker service.

sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker

To run Docker without sudo, I added my user to the Docker group and reconnected to the VM.

sudo usermod -aG docker azureuser
exit

After reconnecting, I verified that Docker was working.

docker ps

3. Install Python and Pip
   I installed Python and pip to run the Django application.

sudo apt install python3 python3-pip -y
python3 --version
pip3 --version

4. Create Virtual Environment
   To isolate the project dependencies, I created and activated a virtual environment.

sudo apt install python3-venv -y
python3 -m venv venv
source venv/bin/activate

5. Install Django
   I installed Django inside the virtual environment.

pip install django
django-admin --version



6. Create Django Project
   I created a Django project and moved into the project directory.

django-admin startproject myproject
cd myproject

I verified that the application worked by running the development server.

python manage.py runserver 0.0.0.0:8000

7. Capture Dependencies
   I captured the project dependencies so they could be installed inside the Docker container.

pip freeze > requirements.txt
cat requirements.txt

8. Create Dockerfile
   I created a Dockerfile to containerize the Django application.

vim Dockerfile

Dockerfile content:

FROM python:3.12
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

![Dockerfile Screenshot](Screenshot%202026-03-16%20100703.png)

9. Build Docker Image
   I built the Docker image for the application.

docker build -t django-app .

I verified that the image was created.

docker images

10. Run Docker Container
    I started the container and mapped port 8000 from the container to the VM.

docker run -d -p 8000:8000 django-app

Then I verified that the container was running.

docker ps

11. Configure Django Allowed Hosts
    Django blocked external access initially, so I updated the settings file.

vim myproject/settings.py

I changed the ALLOWED_HOSTS setting.

ALLOWED_HOSTS = ['*']

12. Restart Container
    After modifying the configuration, I rebuilt the image and ran the container again.

docker stop <container_id>
docker rm <container_id>
docker build -t django-app .
docker run -d -p 8000:8000 django-app

13. Configure Azure Networking
    I added an inbound rule in the Azure Network Security Group to allow incoming traffic on port 8000.

14. Access Application
    Finally, I accessed the Django application in the browser using the VM public IP.

http://<public-ip>:8000

Result
The Django application was successfully deployed on an Azure Virtual Machine using Docker and became accessible through the VM’s public IP address.
