pipeline {
    agent {
        label 'slave'
    }
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "auto_maven"
    }
    environment {
        IMAGE = readMavenPom().getArtifactId()
        VERSION = readMavenPom().getVersion()
    }
    stages {
        stage('Clear running apps') {
            steps {
                // Get some code from a GitHub repository
                sh 'docker rm -f pandaapp || true'
            }
        }
        stage('Pull') {
            steps {
                // Get some code from a GitHub repository
                checkout scm
            }
        }
        stage('Build') {  
                // Run Maven on a Unix agent.
            steps {
                sh 'mvn clean install'

            }
        }    
        stage('Test') {
            steps {
                // testy
                sh 'mvn test'
            }
        }
        stage('Build docker image') {
            steps {
                // testy
                sh 'mvn package -Pdocker'
            }
        }
        stage('Run docker app') {
            steps {
                // testy
                sh 'docker run -d -p 0.0.0.0:8080:8080 --name pandaapp -t ${IMAGE}:${VERSION}'
            }
        }
        stage('Test Selenium') {
            steps {
                // testy
                sh 'mvn test -Pselenium'
            }
        }
        stage('Deploy'){
            steps {
                configFileProvider([configFile(fileId: 'b69e43cc-4d77-4155-a1ef-7e3537cac9e1', variable: 'MAVEN_SETTINGS')]) {
                sh 'mvn -s $MAVEN_SETTINGS deploy -Dmaven.test.skip=true -e'
            }
        }
    
}
}
    post {
        always {
            sh 'docker stop pandaapp'
            deleteDir()
        }
    }
}
