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
                        git config user.name "$GIT_USERNAME"
                        git config user.email "${GIT_USERNAME}@example.com"
                        git config credential.helper '!f() { echo username=$GIT_USERNAME; echo password=$GIT_PASSWORD; }; f'
                        cat deploy.yaml
                        sed -i 's/13/${BUILD_NUMBER}/g' deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m "Updated the deploy yaml | Jenkins Pipeline"
                        git remote set-url origin https://github.com/etechteam6/cicd-manifest-repo.git 
                        git push origin HEAD:main
                        '''                     
                    }
                }
            }
        }
    }
}
