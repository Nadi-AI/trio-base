pipeline {
    agent any
    stages {
        stage('Greeting') {
            steps {
                sh '''
                echo "Good Morning Nadia, let's get building!"
                '''
            }
        }
        stage('SCM') {
          checkout scm
        }
        stage('SonarQube Analysis') {
          def scannerHome = tool 'SonarScanner';
          withSonarQubeEnv() {
            sh "${scannerHome}/bin/sonar-scanner"
          }
        }
                
        stage('Build') {
            steps {
                sh '''
                docker build -t scribral/trio-flask:latest ./flask-app
                docker build -t scribral/trio-flask:v$BUILD_NUMBER ./flask-app
                docker build -t scribral/trio-mysql:latest ./db
                docker build -t scribral/trio-mysql:v$BUILD_NUMBER ./db
                docker build -t scribral/trio-nginx:latest ./nginx
                docker build -t scribral/trio-nginx:v$BUILD_NUMBER ./nginx
               
                '''                                    

            }
        }
        stage('Push') {
            steps {
                script {
                    if (env.GIT_BRANCH == 'origin/main') {
            
                sh '''
                docker push scribral/trio-flask:latest
                docker push scribral/trio-flask:v$BUILD_NUMBER
                docker push scribral/trio-mysql:latest
                docker push scribral/trio-mysql:v$BUILD_NUMBER
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
        stage('Clean Up') { 
            steps {
                sh '''
                docker system prune -a --force
                '''
            }
        }       
        stage('End of automation msg') { 
            steps {
                sh '''
                echo "Now do an apply manually, and rollout an update if required"
                '''
            }
        }
    }
}

