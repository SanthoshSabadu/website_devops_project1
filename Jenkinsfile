pipeline {
    agent any

    triggers {
        pollSCM('@daily') // daily check for changes
        cron('H H 25 * *') // run on the 25th of every month
    }

    environment {
        DOCKER_USER = 'santhoshsabadu'
        DOCKER_PASS = 'DMET08X99ZM8E'
        REMOTE_HOST = 'ubuntu@3.139.239.151'
        PROJECT_DIR = '/home/user/website_devops_project1'
    }

    stages {
        stage('Build & Push Docker Image on Server B') {
            steps {
                sh """
                ssh $REMOTE_HOST '
                    cd $PROJECT_DIR &&
                    docker build -t $DOCKER_USER/apache2-app:${env.BUILD_ID} . &&
                    docker tag $DOCKER_USER/apache2-app:${env.BUILD_ID} $DOCKER_USER/apache2-app:latest &&
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin &&
                    docker push $DOCKER_USER/apache2-app:${env.BUILD_ID} &&
                    docker push $DOCKER_USER/apache2-app:latest
                '
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                ssh $REMOTE_HOST '
                    cd $PROJECT_DIR &&
                    kubectl apply -f k8s-deployment.yaml &&
                    kubectl apply -f k8s-service.yaml
                '
                """
            }
        }
    }
}
