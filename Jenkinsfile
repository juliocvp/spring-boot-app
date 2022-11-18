def versionPom = ""
pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: shell
    image: juliocvp/jenkins-nodo-java-bootcamp:1.0
    command:
    - sleep
    args:
    - infinity
'''
            defaultContainer 'shell'
        }
    }

    environment {
        DOCKER_CREDENTIALS = credentials("docker-hub-credentials")
        DOCKER_IMAGE_NAME = "juliocvp/spring-boot-app"
    }

    stages {
        stage("build") {
            steps {
                sh "mvn clean package -DskipTest"
            }
        }
        stage("Build image and push to DockerHub") {
            steps {
                sh 'echo $DOCKER_CREDENTIALS_PSW | docker login -u $DOCKER_CREDENTIALS_USR --password-stdin'
                sh "docker build -t $DOCKER_IMAGE_NAME:latest ."
                sh "docker push $DOCKER_IMAGE_NAME:latest"
            }
        }
        stage("Deploy to KBs") {
            steps {
                sh "git clone https://github.com/juliocvp/kubernetes-helm-docker-config.git configuracion --branch test-implementation"
                sh "kubectl apply -f configuracion/kubernetes-deployments/spring-boot-app/deployment.yaml --kubeconfig=configuracion/kubernetes-config/config"
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}