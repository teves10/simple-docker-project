## Deploying a Custom Docker Image to AWS Elastic Beanstalk

This project walks through building a custom Docker image locally, running it in a container, and deploying it to the cloud using **AWS Elastic Beanstalk**.

---

## Step 1 — Build Your Custom Image
### Create Project Directory
We’ll keep everything organized inside a folder called Compute.
```bash
cd ~/Desktop
mkdir Compute
cd Compute
```

### Create a Dockerfile
The Dockerfile defines how your image is built.
```bash
touch Dockerfile
```


Paste the following content inside Dockerfile:

```bash
FROM nginx:latest
COPY index.html /usr/share/nginx/html/
EXPOSE 80
```


**FROM nginx:latest** → Uses the official Nginx image as a base.

**COPY index.html** → Replaces the default Nginx page with your own HTML.

**EXPOSE 80** → Opens port 80 for web traffic.

### Create a Web Page
This HTML file will be served by Nginx inside the container.
```bash
touch index.html
```


Paste the following content (replace enter your name):
```bash
<!doctype html>
<html>
  <head>
    <title>My Web App</title>
  </head>
  <body>
    <h1>Hello from `YOUR-NAME'`s custom Docker image!</h1>
  </body>
</html>
```

Build the Image
Use Docker to create an image from your Dockerfile.
```bash
docker build -t my-web-app .
```


`-t my-web-app` → Names your image.

`.` → Tells Docker to look for the Dockerfile in the current directory.

---


### Step 2 — Run a Container

Run the container from your image:
```bash
docker run -d -p 80:80 my-web-app
```

`-d` → Runs the container in detached mode (background).

`-p 80:80` → Maps port 80 on your computer to port 80 in the container.


Check if a container is already using port 80:

```bash
docker ps --filter "publish=80"
```


Stop a container (replace with actual ID from above):
```bash
docker stop CONTAINER-ID
```

---

## Step 3 — Log in to AWS

Sign in to **AWS using your IAM user** (not root). You’ll need permissions to work with Elastic Beanstalk.

---

## Step 4 — Deploy to Elastic Beanstalk
### Open Elastic Beanstalk

Go to **AWS Console → Elastic Beanstalk**.

Click **Create Application**.

## Configure Application

Keep **Environment tier** → Default.

Enter an **Application name**.

Select **Docker** under Platform.

In **Application code**, choose **Upload your code**.

## Prepare and Upload Code
Elastic Beanstalk needs a ZIP file. Compress your files:

* Zip both files: Dockerfile and index.html.

Upload the ZIP file as **Version One**.

## Configure Service Access

Elastic Beanstalk needs roles to manage AWS resources:

**Service role** → Click **Create role** → accept defaults → refresh → select the new role.

**EC2 instance profile** → Do the same → refresh → select the new role.

## Configure Networking

Use the **default VPC**.

Enable **Public IP address**.

Skip database configuration.

## Instance and Scaling Settings

**Root volume** → General Purpose 3 (SSD), 10 GB.

**Disable IMDSv1** for security.

Leave scaling and security groups as default.

## Review & Launch

Review your setup.

Click **Create**.

Deployment will take a few minutes. Once complete, Elastic Beanstalk provides a public URL where your app is live. 🎉
