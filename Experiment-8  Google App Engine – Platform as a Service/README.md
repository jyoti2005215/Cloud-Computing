# Cloud Computing Lab 8 -- Implementing Applications Using PaaS

## Experiment Details

-   **Experiment:** 8
-   **Title:** Implementing Applications Using Platform as a Service
    (PaaS)
-   **Cloud platform:** Render
-   **Application framework:** Python Flask
-   **Deployment method:** GitHub integration with Render Web Service
-   **Repository:** https://github.com/suvankar200/cloud-lab8-render
-   **Live application:** https://cloud-lab8-render.onrender.com

## Aim

To understand the Platform as a Service (PaaS) model and develop,
deploy, access, and monitor a simple web application using a
cloud-hosted PaaS platform.

## Platform Overview

Render is used as the PaaS provider for this implementation. It hosts
the Flask application and manages the application runtime and deployment
infrastructure.

The application is connected to a GitHub repository. Render retrieves
the source code from the `main` branch, installs the Python
dependencies, and starts the application using Gunicorn.

> **Platform substitution:** The supplied lab manual describes Google
> App Engine. This implementation uses Render Web Services as the PaaS
> platform while retaining the experiment's core activities: application
> development, deployment, access, deployment-history inspection, and
> log monitoring.

## Project Structure

``` text
cloud-lab8-render/
├── main.py
├── requirements.txt
└── .gitignore
```

The local Python virtual environment (`venv/`) is excluded from Git
using `.gitignore`.

## Technologies Used

-   Python 3
-   Flask
-   Gunicorn
-   Git
-   GitHub
-   Render Web Services

## Application

The Flask application defines a home route (`/`) that returns an HTML
page identifying the experiment and the hosting platform.

### `main.py`

``` python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def home():
    return """
    <html>
    <head>
        <title>Cloud Computing Lab 8</title>
    </head>
    <body>
        <h1>Cloud Computing Lab 8</h1>
        <h2>Application Successfully Deployed</h2>
        <p>This application demonstrates Platform as a Service (PaaS).</p>
        <p>Cloud Platform: Render</p>
        <p>Framework: Python Flask</p>
    </body>
    </html>
    """

if __name__ == "__main__":
    app.run(host="127.0.0.1", port=8080)
```

### `requirements.txt`

``` text
Flask
gunicorn
```

### `.gitignore`

``` text
venv/
__pycache__/
*.pyc
.env
```

## Task 1 -- PaaS Setup and Configuration

1.  Created and accessed a Render account.
2.  Opened the Render dashboard.
3.  Connected the GitHub account.
4.  Selected the `cloud-lab8-render` repository as the source for a new
    Web Service.
5.  Selected Python 3 and the `main` branch.
6.  Selected the Free instance for the lab deployment.

**Observation:** Render provides a managed web-service environment for
deploying the application without manually configuring the underlying
server.

## Task 2 -- Application Development and Local Testing

Created the project directory and application files in Ubuntu.

``` bash
mkdir cloud-lab8-render
cd cloud-lab8-render
```

Created a virtual environment, installed the dependencies, and ran the
application locally:

``` bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python main.py
```

Opened the local application at:

``` text
http://127.0.0.1:8080
```

**Observation:** The Flask application displayed the Cloud Computing Lab
8 page in the browser. The local server returned an HTTP `200` response
for the home-page request.

## Task 3 -- GitHub Integration and Cloud Deployment

Initialized the Git repository and uploaded the source files to GitHub.

``` bash
git init
git add main.py requirements.txt .gitignore
git commit -m "Initial commit - Cloud Computing Lab 8"
git branch -M main
```

The repository was created and pushed using GitHub CLI:

``` bash
gh repo create cloud-lab8-render --public --source=. --remote=origin --push
```

### Render deployment configuration

  Setting         Value
  --------------- -----------------------------------
  Service type    Web Service
  Repository      `suvankar200/cloud-lab8-render`
  Branch          `main`
  Runtime         Python 3
  Region          Singapore
  Instance        Free
  Build command   `pip install -r requirements.txt`
  Start command   `gunicorn main:app`

The correct Gunicorn command is `gunicorn main:app` because the Flask
application object named `app` is defined in the `main.py` module.

**Deployment URL:** https://cloud-lab8-render.onrender.com

**Observation:** The deployment completed successfully and the service
status was shown as Live. The deployed page was accessible through the
public HTTPS URL.

## Task 4 -- Application Monitoring and Management

### 4A. Deployment History

The Render Deploys page was inspected to view deployment history.

Observed deployment information:

-   Service name: `cloud-lab8-render`
-   Branch: `main`
-   Source commit: `e66279b`
-   Successful deployment duration: `42.6 seconds`
-   Deployment trigger: Start command updated
-   Deployment status: Deploy succeeded / Live

The earlier first deployment failed because the Gunicorn start command
attempted to import the wrong module. Updating the command to
`gunicorn main:app` resolved the issue.

Render's deployment history is used for this experiment in place of
Google App Engine's version list. Render's interface does not provide
the same App Engine traffic-allocation view.

### 4B. Application Logs

The Render deployment logs were inspected. The logs showed Gunicorn
starting, the application listening on the assigned service port, and
successful HTTP requests.

Example log messages observed:

``` text
Starting gunicorn
Listening at: http://0.0.0.0:10000
Your service is live
GET / HTTP/1.1 200
```

An HTTP `200` status indicates that the request was successfully
handled.

### 4C. Application Update and Redeployment

To demonstrate an application update, modify the HTML message in
`main.py`, commit the change, and push it to the GitHub `main` branch:

``` bash
git add main.py
git commit -m "Update application to version 2"
git push origin main
```

Render can automatically start a new deployment when a new commit is
pushed to the connected branch. After deployment, open the public URL
and verify the updated page.

**Completion note:** Record this activity as completed only after
pushing the updated code and verifying the new deployment and page. The
initial successful deployment is verified; a Version 2 redeployment is
not confirmed in the current project evidence.

### 4D. Application Management

The Render service dashboard provides sections for:

-   Overview and service status
-   Deployments and deployment history
-   Logs
-   Metrics
-   Events
-   Settings and environment configuration

These sections provide access to deployment, monitoring, and
service-management information.

## Result

A simple Python Flask web application was developed, tested locally,
uploaded to GitHub, and successfully deployed as a Render Web Service
using the Free instance. The public application URL was accessed, and
deployment history and application logs were inspected.

## Important Notes

-   Render Free instances can spin down after a period of inactivity.
    The first request after inactivity may take longer.
-   The Free service has resource and usage limitations.
-   Do not add passwords, API keys, access tokens, or other secrets to
    the Git repository.
-   Check the Render dashboard and current plan details before deploying
    additional resources.
-   This experiment uses Render instead of Google App Engine; the two
    platforms have different dashboards and feature sets.

## References

-   Render: https://render.com/
-   Render Flask deployment guide: https://render.com/docs/deploy-flask
-   GitHub repository: https://github.com/suvankar200/cloud-lab8-render
-   Deployed application: https://cloud-lab8-render.onrender.com
