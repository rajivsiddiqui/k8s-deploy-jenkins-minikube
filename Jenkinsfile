pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credential')
        //KUBECONFIG = credentials('kubeconfig-cred')
        //KUBECONFIG = "${WORKSPACE}/kubeconfig"
        //KUBECONFIG = "/var/lib/jenkins/kubeconfig"
        KUBECONFIG = "/var/lib/jenkins/minikube-jenkins/config"
        MINIKUBE_HOME = "/var/lib/jenkins/minikube-jenkins/.minikube"
    } 
    stages {
        // stage('git_clone') {
        //     steps {
        //         git branch: 'main', url: 'https://github.com/rajivsiddiqui/java-project-1.git'
        //     }
        // }
        //build stage 
        stage('build') {
            steps {
                //sh 'mvn clean deploy -Dmaven.test.skip=true'  //-Dmaven.test.skip=true
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
        // stage('Prepare kubeconfig') {
        //     steps {
        //         sh '''
        //         cp /home/rajiv/.kube/config $KUBECONFIG
        //         chmod 600 $KUBECONFIG
        //         # Update paths inside the config to match workspace
        //         sed -i 's|/home/rajiv/.minikube|'"$WORKSPACE"'/minikube|g' $KUBECONFIG
        //         cp -r /home/rajiv/.minikube $WORKSPACE/minikube
        //         '''
        //     }
        // }
        stage('Debug kubectl') {
            steps {
                //sh 'kubectl config view'
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