pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credential')
    }
    stages {
        stage('build') {
            steps {
                sh 'mvn clean install'
            }
        }
        //docker image create
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devopssteps/java-1:latest .'
            }
        }
        //docker login and push docker image
        stage('Login to dockerhub and push the image') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push devopssteps/java-1:latest'
            }
        }
        //debug kubectl working or not
        stage('Debug kubectl') {
            steps {
                sh 'kubectl get nodes'
            }
        }
        //k8s steps add
        stage('deploy to kubernetes') {
            steps {
                sh './k8s/deploy.sh'
                sh 'kubectl rollout restart deployment.apps/myapp-deployment' // this force to deployment with the new docker image

            }
        }
    }
}