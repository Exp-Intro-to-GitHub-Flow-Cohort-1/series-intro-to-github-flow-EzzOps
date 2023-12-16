pipeline {	
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          containers:
          - name: node
            image: node:alpine
            command:
            - cat
            tty: true
        '''
    }
  }
    stages {	
        stage("install dependencies") {	
            steps {
              container('node') {
                sh 'npm remove node_modules'
                sh 'npm remove package-lock.json'
                sh 'npm -v' // sanity check
                sh 'npm install'
        }              
                sh 'npm remove node_modules'
                sh 'npm remove package-lock.json'
                sh 'npm -v' // sanity check
                sh 'npm install'
            }	
        }	
        stage(" "){
          parallel {
            stage("Test") {
              steps {
                sh 'npm run  test:unit'
              }
            }
            
        stage("Build") {
              steps {
                sh 'npm run build'
              }
            }
        }
      }	
        //stage('Scan') {
        //  steps {
        //    withSonarQubeEnv(installationName: 'SonarServer1') { 
        //      sh './mvnw clean org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar'
        //    }
        //  }
        // }
      stage('Login to Docker Hub') {         
        steps{                            
        	sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'                 
        	echo 'Login Completed'                
          }           
        }  
      stage('Building image') {
        steps{
          script {
            dockerImage = docker.build registry + ":$BUILD_NUMBER"
              }
            }
          }
      stage('Deploy Image') {
        steps{
           script {
              docker.withRegistry( '', registryCredential ) {
              dockerImage.push()
            }
          }
        }
      }
    }	
}
