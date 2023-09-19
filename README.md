# Configuring Jenkins with Docker and GitHub Integration via AWS EC2 for Pipeline Creation

![Infrastructure](./documentation/xtra/infra.png)

If you are new to Jenkins, you may find it beneficial to run Jenkins on a server accessible to other applications. We will deploy Jenkins inside an AWS EC2 instance, encapsulated in a Docker container. Using Docker is ideal as it provides an isolated environment, and it aligns with learning Docker alongside Jenkins.

## Step 1: Connect to Your EC2 Instance via SSH

![Connect to EC2](./documentation/1.png)

---

## Step 2: Install Docker

```bash
> sudo yum install -y docker
```

![Install Docker](./documentation/2.png)

---

## Step 3: Start Docker Services and Check Status

```bash
> sudo service docker start
> sudo service docker status
```

![Start Docker](./documentation/3.png)

---

## Step 4: Grant Docker Sudo Privileges

```bash
> sudo usermod -a -G docker ec2-user
```

![Grant Docker Privileges](./documentation/4.png)

**Note:** Remember to re-login to your instance after executing the command.

---

## Step 5: Run Jenkins via Docker

```bash
> docker run -d --name jenkins -p 8080:8080 -p 50000:50000 -v /var/jenkins_home jenkins/jenkins:lts
```

![Run Jenkins](./documentation/5.png)

---

## Step 6: Check Jenkins Status

Access Jenkins by using your instance's public IPv4 address and port 8080.

![Access Jenkins](./documentation/7.png)

---

## Step 7: Unlock Jenkins

Jenkins is locked initially. Retrieve the unlock key from the Docker logs of the Jenkins container.

```bash
> docker container ls
> docker logs -f "container-id"
```

![Unlock Jenkins](./documentation/8.png)
![Unlock Jenkins](./documentation/9.png)

---

## Step 8: Install Suggested Plugins

After unlocking Jenkins, choose to install the suggested plugins.

![Install Plugins](./documentation/10.png)
![Install Plugins](./documentation/11.png)

---

## Step 9: Create First Admin User

Create the first admin user.

![Create Admin User](./documentation/12.png)

---

## Step 10: Save Jenkins URL

After creating the admin user, save the Jenkins URL provided for instance configuration.

![Save Jenkins URL](./documentation/14.png)

---

## Step 11: Jenkins is Ready to Use

Jenkins is now ready for use.

![Jenkins Ready](./documentation/15.png)
![Jenkins Ready](./documentation/16.png)

---

## Create a Pipeline and Connect to GitHub

## Step 12: Create a New Pipeline

From your Jenkins dashboard, select "New Item" to create a new pipeline.

![Create Pipeline](./documentation/17.png)

---

## Step 13: Define Pipeline Script

In the pipeline section, paste the provided script and click "Save."

```bash
pipeline {
    agent any

    stages {
        stage('Clone Repo') {
            steps {
                echo '[INFO] Cloning Repository'
                sh 'git clone --depth 1 --single-branch https://github.com/WonderCMS/wondercms.git'
                sh 'ls wondercms'
            }
        }
        stage('Provision AWS Instance') {
            steps {
                echo '[INFO] Deploying to AWS'
                // sh 'scp -r web_app user@ip_add:/var/www/html'
            }
        }
        stage('Deploy') {
            steps {
                echo '[INFO] Sending Notifications'
                // sh 'sh notif.sh'
            }
        }
        stage('Notification') {
            steps {
                echo '[INFO] Sending Notifications'
                slackSend channel: '#random', message: 'test', teamDomain: 'randomresearchinc.slack.com', tokenCredentialId: 'slack'
                cleanWs()
            }
        }
    }
}
```

![Define Pipeline Script](./documentation/18.png)

---

## Step 14: Build the Pipeline

After saving the pipeline, select "Build Now" and wait for it to finish building.

![Build Pipeline](./documentation/19.png)
![Build Pipeline](./documentation/20.png)

---

## Jenkins Pipeline with GitHub Credentials

## Step 15: Define Pipeline Script with GitHub Credentials

Use this script to build your pipeline using GitHub credentials:

```bash
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                // Get code from a GitHub repository
                git url: 'https://github.com/9QIX/jenkins-to-github-credentials', branch: 'main',
                 credentialsId: 'github_creds'
            }
        }
    }
}
```

![GitHub Credentials](./documentation/21.png)

---

## Step 16: Create a GitHub Token

To create a GitHub token, log in to your GitHub account, go to settings, and click on "Developer settings."

![Create GitHub Token](./documentation/23.png)

---

## Step 17: Generate a Personal Access Token

Navigate to "Personal access tokens," click "Generate new token," provide necessary permissions, and click "Generate token" to obtain the token for GitHub authentication.

![Generate Token](./documentation/24.png)
![Generate Token](./documentation/25.png)
![Generate Token](./documentation/26.png)

---

## Step 18: Add GitHub Credentials in Jenkins

In Jenkins, go to "Manage Jenkins" > "Manage Credentials" > "System" > "Global credentials (unrestricted)" > "Add Credentials."

![Add GitHub Credentials](./documentation/27.png)
![Add GitHub Credentials](./documentation/28.png)
![Add GitHub Credentials](./documentation/29.png)
![Add GitHub Credentials](./documentation/30.png)

---

## Step 19: Provide Credentials

Provide your GitHub username and the generated GitHub token in place of the password, then click "CREATE."

![Provide Credentials](./documentation/32.png)
![Provide Credentials](./documentation/31.png)

---

## Step 20: Build the Pipeline with GitHub Credentials

Return to your Jenkins Pipeline with GitHub credentials and build it.

![Build Pipeline](./documentation/34.png)
![Build Pipeline](./documentation/35.png)

---

## Step 21: Verify Pipeline Status

Check the status of both items on your Jenkins dashboard.

![Verify Pipeline](./documentation/37.png)
