pipeline{
    agent any
    tools{
        jdk 'java-17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'master', url: 'https://github.com/devops-with-Git/Amazon-Front-Page.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Amazon \
                    -Dsonar.projectKey=Amazon '''
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
	stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build -t amazon ."
                       sh "docker tag amazon swapnilhub/amazon:latest "
                       sh "docker push swapnilhub/amazon:latest "
                    }
                }
            }
        }
    stage("TRIVY") {
    	steps {
        	sh """
            	trivy image --timeout 10m --skip-version-check swapnilhub/amazon:latest > trivyimage.txt 2>&1
        		"""
    	}
	}
	stage('Deploy to container'){
            steps{
                sh 'docker run -d --name amazon -p 3000:3000 swapnilhub/amazon:latest'
            }
        }
    }
}
