pipeline {

    agent any

    triggers {
        githubPush()
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage("Code Clone") {
            steps {
                git branch: "main",
                    url: "https://github.com/11Shahid22/two-tier-flask-app"
            }
        }

        stage("Build") {
            steps {
                sh "docker compose build"
            }
        }

        stage("Deploy") {
            steps {
                sh "docker compose up -d"
            }
        }

        stage("Health Check") {
            steps {
                sh '''
                    sleep 10
                    docker compose ps
                    curl -f http://localhost/ || exit 1
                '''
            }
        }
    }

    post {

        success {
            echo "CI/CD Pipeline completed successfully!"
        }

        failure {
            echo "CI/CD Pipeline failed!"
            sh "docker compose ps || true"
            sh "docker compose logs --tail=100 || true"
        }
    }
}
