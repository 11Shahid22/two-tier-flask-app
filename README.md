 
# Flask App with MySQL Docker Setup

This is a simple Flask app that interacts with a MySQL database. The app allows users to submit messages, which are then stored in the database and displayed on the frontend.

## Prerequisites

Before you begin, make sure you have the following:

- Linux base OS
- Docker
- Git (optional, for cloning the repository)

## Setup

1. Clone this repository (if you haven't already):

   ```bash
   git clone https://github.com/11Shahid22/two-tier-flask-app.git

   ```

2. Navigate to the project directory:

   ```bash
   cd your-repo-name  (in my case: cd /home/shahid/shahid-assessment/two-tier-flask-app)
   ```

3. Create a `.env` file in the project directory to store your MySQL environment variables:

   ```bash
   touch .env  (store secret credentials like MYSQL_PASSWORD, DATABASE NAME etc)
   ```

4. Open the `.env` file and add your MySQL configuration:

   ```
   MYSQL_HOST=mysql
   MYSQL_USER=your_username
   MYSQL_PASSWORD=your_password
   MYSQL_DB=your_database
   ```

## Usage

1. Start the containers using Docker Compose:

   ```bash
   docker-compose up --build
   ```

2. Access the Flask app in your web browser:

   - Frontend: http://localhost
   - Backend: http://localhost:5000    (here you can expose another port instead of 5000 as you want)

3. Create the `messages` table in your MySQL database:

   - Use a MySQL client or tool (e.g., phpMyAdmin) to execute the following SQL commands:
   
     ```sql
     CREATE TABLE messages (
         id INT AUTO_INCREMENT PRIMARY KEY,
         message TEXT
     );
     ```

4. Interact with the app:

   - Visit http://localhost to see the frontend. You can submit new messages using the form.
   - Visit http://localhost:5000/insert_sql to insert a message directly into the `messages` table via an SQL query.

## Cleaning Up

To stop and remove the Docker containers, press `Ctrl+C` in the terminal where the containers are running, or use the following command:

```bash
docker-compose down
```

- Now, make sure that you have created a network using following command
```bash
docker network create task-net
```

- Attach both the containers in the same network, so that they can communicate with each other

i) MySQL container 
```bash
docker run -d \
    --name mysql \
    -v mysql-data:/var/lib/mysql \
    --network=tast-net \
    -e MYSQL_DATABASE=mydb \
    -e MYSQL_ROOT_PASSWORD=admin \
    -p 3306:3306 \
    mysql:5.7

```
ii) Backend container
```bash
docker run -d \
    --name flaskapp \
    --network=tast-net \
    -e MYSQL_HOST=mysql \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=admin \
    -e MYSQL_DB=mydb \
    -p 5000:5000 \
    flaskapp:latest

```

## Notes

- Make sure to replace placeholders (e.g., `your_username`, `your_password`, `your_database`) with your actual MySQL configuration.

- This is a basic setup for demonstration purposes. In a production environment, you should follow best practices for security and performance.

- Be cautious when executing SQL queries directly. Validate and sanitize user inputs to prevent vulnerabilities like SQL injection.

- If you encounter issues, check Docker logs and error messages for troubleshooting.

```


-------Install Jenkins on your Linux-------
sudo apt update
sudo apt upgrade -y

sudo apt install fontconfig openjdk-17-jre -y  (java installation)

java -version

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt update

sudo apt install jenkins -y

sudo systemctl enable jenkins
sudo systemctl start jenkins

sudo systemctl status jenkins --no-pager   (by default jenkins runs on port 8080)

sudo systemctl edit jenkins (if you need to change its port, open this file to edit and write this line  "[Service]
Environment="JENKINS_PORT=8081"
" in the end of this file )

sudo systemctl daemon-reload
sudo systemctl restart jenkins

sudo ss -lntp | grep 8081

sudo cat /var/lib/jenkins/secrets/initialAdminPassword   (by default its username will be "admin" and you will see a default password) 

http://localhost:8080  (access your jenkins)


---------Write a CI/CD Pipeline--------

Make sure your **Jenkinsfile is already available in your GitHub repository**, follow these steps,

1. Open Jenkins at `http://localhost:8080`.
2. Click **New Item**.
3. Enter a name, for example `two-tier-flask-app`.
4. Select **Pipeline** and click **OK**.
5. Scroll down to the **Pipeline** section.
6. Set **Definition** to `Pipeline script from SCM`.
7. Set **SCM** to `Git`.
8. Enter your **GitHub Repository URL**.
9. Set **Branch Specifier** to `*/main`.
10. Set **Script Path** to `Jenkinsfile`.
11. If the repository is private, add your **GitHub credentials/PAT** if it is Public then no need to enter credentials.
12. Click **Save**.
13. Click **Build Now**.
14. Open **Console Output** to check whether the pipeline completed successfully.

