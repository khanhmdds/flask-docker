pipeline {
    agent none

    environment {
        DOCKER_IMAGE = "khanhmd/flask-docker"
    } 
    
    stages {
        stage('Test') {
            agent {
                docker {
                    image 'python:3.8-slim-buster'
                    args '-u 0:0 -v /tmp:/root/.cache'
                }
            }   
            steps {
                sh "pip install poetry"
                sh "poetry install"
                sh "poetry run pytest"
            }
        }
        
        stage('build') {
            agent { node {label 'master'}}
            
            environment {
                DOCKER_TAG="${GIT_BRANCH.tokenize('/').pop()}-${GIT_COMMIT.substring(0,7)}"
            }
            
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} . "
                sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"
                sh "docker image ls | grep ${DOCKER_IMAGE}"
//              withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
//                  sh 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
//                  sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
//                  sh "docker push ${DOCKER_IMAGE}:latest"
//              }
                sh "id"
                sh "pwd"
                sh "docker login --username khanhmdds --password bokhanh271298"
                sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                sh "docker push ${DOCKER_IMAGE}:latest"
                
                //clean to save disk
                sh "docker image rm ${DOCKER_IMAGE}:${DOCKER_TAG}"
                sh "docker image rm ${DOCKER_IMAGE}:latest"
            }
            
            post {
                success {
                    echo "SUCCESSFUL"
                }
                failure {
                    echo "FAILED"
                }
            }
        }
        
//         stage('deploy') {
//             agent { node {label 'master'}}
//             steps {
// //                 withCredentials([usernamePassword(credentialsId: 'ssh-uername-passwd', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
// //                     sh "sshpass -p $PASSWORD ssh -o StrictHostKeyChecking=no minhmd@10.0.0.4 "
// // 					sh "'./deploy.sh'"
// //                 }
		    
// 		    sshagent(['ssh-remote']) {
//     			// some block
// 			    sh 'ssh -o StrictHostKeyChecking=no -l root 10.0.0.11 docker run -p 5000:5000 -d minhmd3011/flask-docker:latest'
// 			    //sh 'docker run -p 5000:5000 -d minhmd3011/flask-docker:latest'
// 		    }
//             }
//         }
    }
}
