pipeline{
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'node12'
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
                git branch: 'main', url: 'https://github.com/kaustika940/Node.js-ToDoApp.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=To-Do-App \
                    -Dsonar.projectKey=To-Do-App'''
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }
        
        
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build -t to-do-app ."
                       sh "docker tag to-do-app kaustika1997/to-do-app:latest "
                       sh "docker push kaustika1997/to-do-app:latest"
                    }
                }
            }
        }
        stage('Docker Scout Image') {
            steps {
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh 'docker-scout quickview kaustika1997/to-do-app:latest'
                       sh 'docker-scout cves kaustika1997/to-do-app:latest'
                       sh 'docker-scout recommendations kaustika1997/to-do-app:latest'
                   }
                }
            }
        }
        stage("deploy_docker"){
            steps{
                sh "docker run -d --name To-Do-App -p 8000:8000 kaustika1997/to-do-app:latest"
            }
        }
    }
}
