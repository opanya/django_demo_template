pipeline{
    agent none
        stages {
            stage("tests"){
                agent {
                    docker{
                        image 'python:3.10'
                    }
                }
                steps {
                    sh '''
                        python -m venv .venv
                        . .venv/bin/activate
                        pip install -r requirements.txt
                        python manage.py test
                        '''
                }
        }
        stage("build"){
            agent any
            steps{
                sh 'docker build . -t queydi/django_demo_jenkins:${GIT_COMMIT} -t queydi/django_demo_jenkins:latest'
            }
        }
        stage ('push'){
            agent any
            steps {
                    withCredentials([usernamePassword(credentialsId: 'docker', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh 'docker login -u ${USERNAME} -p ${PASSWORD}'
                        sh 'docker push queydi/django_demo_jenkins:${GIT_COMMIT}'
                        }
                }
        }
        stage("deploy") {
            agent any
            steps {
                    withCredentials([sshUserPrivateKey(credentialsId: 'deploy_server', keyFileVariable: 'KEY_FILE')]) {
                        sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me mkdir -p ~${WORKSPACE}'
                        sh 'scp -o StrictHostKeyChecking=no -i ${KEY_FILE} docker-compose.yaml ${USERNAME}@devops.io12.me:~${WORKSPACE}'
                        sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me docker-compose -f ~${WORKSPACE}/docker-compose.yaml pull'
                        sh 'ssh -o StrictHostKeyChecking=no -i ${KEY_FILE} ${USERNAME}@devops.io12.me docker-compose -f ~${WORKSPACE}/docker-compose.yaml up -d'
                    }
                }
            }
        }
  }
