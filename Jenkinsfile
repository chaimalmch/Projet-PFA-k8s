def commit_id
pipeline {
    agent any

    stages {
        stage ('Preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage ('Build') {
            steps {
                echo 'Building....'
                sh 'scp -r -i $(minikube ssh-key) ./* docker@$(minikube ip):~/'
                sh "minikube ssh 'docker build -t position-tracker:${commit_id} .'"
                echo 'build complete'

            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to kubernetes'
                sh "sed -i -r 's|position-tracker|position-tracker:${commit_id}|' position-tracker:yaml"
                sh 'kubectl get all'
                sh 'kubectl apply -f .'
                sh 'kubectl get all'
                echo 'deployment complete'
            
            }
        }

    }
}