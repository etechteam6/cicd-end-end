pipeline {
    
    agent any 

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'github-creds', 
                url: 'https://github.com/etechteam6/cicd-end-end.git',
                branch: 'main'
           }
        }

        stage('docker-hub-login') {
            steps {
                script {
                    // Login to Docker using credentials
                    withCredentials([usernamePassword(credentialsId: '93e13185-a4ce-4588-9a94-912686e48a1f', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t kingmoses33/job-cicd:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push kingmoses33/job-cicd:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'github-creds', 
                url: 'https://github.com/etechteam6/cicd-manifest-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'github-creds', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        if [ -f deploy.yaml ]; then
                            cat deploy.yaml
                            sed -i "s/4/${BUILD_NUMBER}/g" deploy.yaml
                        else
                            echo "Error: deploy.yaml does not exist in the current directory."
                            exit 1
                        fi
                        '''
                        sh '''
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/KingMoses33/manifest-cicd.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
