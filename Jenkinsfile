pipeline {
    agent any
    stages {
        stage('Greeting and niceties') {
            steps {
                sh '''
                echo "Good Morning Nadia, let's get building!"
                '''
            }
        }        
        stage('Build') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                sh '''
                docker build -t scribral/trio-flask:latest .
                docker build -t scribral/trio-flask:v$BUILD_NUMBER .
                docker build -t scribral/trio-mysql:latest .
                docker build -t scribral/trio-mysql:v$BUILD_NUMBER .
                docker build -t scribral/trio-nginx:latest ./nginx
                docker build -t scribral/trio-nginx:v$BUILD_NUMBER ./nginx
               
                '''                      
                    } else {
                        sh '''
                        echo "I don't need to do this"
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
                docker push scribral/trio-flask:latest
                docker push scribral/trio-flask:v$BUILD_NUMBER
                docker build -t scribral/trio-mysql:latest .
                docker build -t scribral/trio-mysql:v$BUILD_NUMBER .
                docker push scribral/trio-nginx:latest
                docker push scribral/trio-nginx:v$BUILD_NUMBER
                '''
                    } else {
                        sh '''
                        echo "I don't need this negativity in my life"
                        '''

                      }
                }               
            }
        }
        stage('Deploy and rollout restart') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
                            
                sh '''
                kubectl apply -f ./k8s --namespace=development
                kubectl rollout restart deployment --namespace=development flask-deployment
                '''
                    } else if (env.GIT_BRANCH == 'origin/main') {
                        sh '''
                        kubectl apply -f ./k8s --namespace=production
                        kubectl rollout restart deployment --namespace=production flask-deployment
                        '''

                    }  else {
                        sh '''
                        echo "I don't need this negativity in my life"
                        '''

                      }              
                }                
            }
        }  
        stage('Clean Up') { 
            steps {
                sh '''
                docker system prune -a --force
                '''
            }
        }
    }
}

