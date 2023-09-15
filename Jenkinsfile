pipeline {
    agent any 
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
     environment {
        SCANNER_HOME=tool 'sonar-scanner'
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
         stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Secreat-Santa \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Secreat-Santa '''
    
                }
            }
        }
         
        
         stage("OWASP Dependency Check"){
            steps{
                dependencyCheck additionalArguments: '--scan ./ ' , odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
      
        stage("Build"){
            steps{
                sh " mvn clean install"
            }
        }  
          
         
       stage("Docker Build & Push"){
            steps{
               script{
                         withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {
                        sh "docker build -t secreatsanta ."
                        sh "docker tag secreatsanta priya247/secreatsanta:${env.BUILD_NUMBER} "
                        sh "docker push priya247/secreatsanta:${env.BUILD_NUMBER} "
                    
                    }
                }
            }
       }
        
    
        
        stage('Trigger ManifestUpdate') {
            steps{
                echo "triggering updatemanifestjob"
                build job: 'Updating-manifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
        }
}
        
       
    }
}


