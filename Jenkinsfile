pipeline {
    agent any

    tools {
        maven 'Maven'
        nodejs "NodeJS"
    }

    stages {
      stage ('Initial') {
            steps {
              echo '========================================='
              echo '                INITIAL '
              echo '========================================='
              sh '''
                   echo "PATH = ${PATH}"
                   echo "M2_HOME = ${M2_HOME}"
               '''
            }
        }
        stage ('Compile') {
            steps {
                echo '========================================='
                echo '                COMPILE '
                echo '========================================='
                 sh 'mvn clean compile -e'
            }
        }
        stage ('Test') {
            steps {
                echo '========================================='
                echo '                TEST '
                echo '========================================='
                 sh 'mvn clean test -e'
            }
        }

        stage('SonarQube analysis') {
           steps{
               echo '========================================='
              echo '                SONARQUBE '
              echo '========================================='
                script {
                    def scannerHome = tool 'SonarQube Scanner';//def scannerHome = tool name: 'SonarQube Scanner', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
                    withSonarQubeEnv('Sonar Server') {
                      sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Tarea4-5 -Dsonar.sources=target/ -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=846e04454f7215b4084d56668d84bcb4f838ce0c"
                    }
                }
           }
        }
        stage ('SCA') {
            steps {
                echo '========================================='
                echo '          SCA DEPENDENCY CHECK '
                echo '========================================='
                 sh 'mvn org.owasp:dependency-check-maven:check'
                dependencyCheckPublisher failedNewCritical: 5, failedTotalCritical: 10, pattern: 'terget/dad.xml', unstableNewCritical: 3, unstableTotalCritical: 5
            }
        }
    }
}
