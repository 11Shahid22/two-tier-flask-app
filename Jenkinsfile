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

        stage("Build") {
            steps {
                sh '''
                    cd /home/shahid/shahid-assessment/two-tier-flask-app
                    docker compose build
                '''
            }
        }

        stage("Deploy") {
            steps {
                sh '''
                    cd /home/shahid/shahid-assessment/two-tier-flask-app

                    docker rm -f nginx-cont flask-cont mysql-cont || true

                    docker compose up -d
                '''
            }
        }

        stage("Health Check") {
            steps {
                sh '''
                    cd /home/shahid/shahid-assessment/two-tier-flask-app

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

            sh '''
                cd /home/shahid/shahid-assessment/two-tier-flask-app
                docker compose ps || true
                docker compose logs --tail=100 || true
            '''
        }
    }
}
