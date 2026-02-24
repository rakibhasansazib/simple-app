pipeline {
    agent any

    environment {
        IMAGE_NAME = "rakibhasansazib/simple-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Clone') {
            steps {
                git 'https://github.com/rakibhasansazib/simple-app.git'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Update Manifest Repo') {
            steps {
                sh '''
                    git clone https://github.com/rakibhasansazib/simple-app-manifests.git
                    cd simple-app-manifests
                    sed -i "s|image:.*|image: $IMAGE_NAME:$IMAGE_TAG|g" deployment.yaml
                    git config user.email "jenkins@local"
                    git config user.name "jenkins"
                    git commit -am "Update image to $IMAGE_TAG"
                    git push
                '''
            }
        }
    }
}
