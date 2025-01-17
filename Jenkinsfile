pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
  

     
    stages{
        
        stage("Git Checkout"){
            steps{
                git branch: 'master', changelog: false, poll: false, url: 'https://github.com/priya241302/Secreat-Santa.git'
        }
        }
        stage("Compile"){
            steps{
                sh "mvn clean compile"
            }
        }
        
         stage("Test Cases"){
            steps{
                sh "mvn test"
            }
            
        
    }
        
        
         
   
      
        stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }  
        
        stage("Docker Build"){
        steps{
            sh "docker build -t secreatsanta ."
        }
        }

          
         
       stage("Docker Push"){
            steps{
               script{
                     withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {
                      
                        sh "docker tag secreatsanta priya247/secreatsanta:${env.BUILD_NUMBER} "
                        sh "docker push priya247/secreatsanta:${env.BUILD_NUMBER} "
                    
                    }
                }
            }
       }
        stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "Secreat-Santa"
            GIT_USER_NAME = "priya241302"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "haripriyapogu@gmail.com"
                    git config user.name "priya241302"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" manifests/deploymentservice.yml
                    git add manifests/deploymentservice.yml
                    git add -A
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:master
                    git rebase master
                '''
            }
        }
    }

    
 

        
       
    }
}


