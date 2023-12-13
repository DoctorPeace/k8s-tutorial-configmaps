pipeline {
    agent any
    stages {
        stage('Init') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        docker rmi nginx:alpine || echo "nginx:alpine image does not exist"
                        docker network create project || echo "network already exists"
                        echo "Main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker rmi nginx:alpine || echo "nginx:alpine image does not exist"
                        docker network create project || echo "network already exists"
                        echo "Dev:Build successful"
                        '''
                    } else {
                        sh '''
                        echo "Init - Unrecognised branch"
                        '''
                    }    
                }   
            }
        }
        stage('Build') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "Build not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker build -t nginx:alpine -t nginx:alpine:v${BUILD_NUMBER} .
                        echo "Build successful"
                        '''
                    } else {
                        sh '''
                        echo "Build - Unrecognised branch"
                        '''
                    }
                }
            }
        }
        stage('Push') {
            steps {
                script {
			        if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "Push not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker push nginx:alpine
                        docker push nginx:alpine:v${BUILD_NUMBER}
                        echo "Push successful"
                        '''
                    } else {
                        sh '''
                        echo "Push - Unrecognised branch"
                        '''
                    }
                }    
           }
        }
        stage('Deploy') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl apply -f ./kubernetes
                        kubectl rollout restart deployment nginx
                        echo "Main:Build successful"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        kubectl apply -f .
                        echo "Dev:Build successful"
                        '''
                    } else {
                        echo "Deploy - Unrecognised branch"
                    }
                }
            }
        }
        stage('Cleanup') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        echo "rmi not required in main"
                        '''
                    } else if (env.GIT_BRANCH == 'origin/dev') {
                        sh '''
                        docker system prune -f
                        docker rmi nginx:alpine:v${BUILD_NUMBER}
                        '''
                    } else {
                        echo "Cleanup - Unrecognised branch"
                    }
                } 
            }
        }
    }
}