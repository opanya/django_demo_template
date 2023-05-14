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
    }
  }
