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
                      sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Tarea4 -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=affe25ffb86ec3efa60b94963ec8179d4f57f70d"
                      sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Tarea5 -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=67fe8d62793e199f57f8856105217cba95757e7d -Dsonar.dependencyCheck.jsonReportPath=target/dependency-check-report.json -Dsonar.dependencyCheck.xmlReportPath=target/dependency-check-report.xml -Dsonar.dependencyCheck.htmlReportPath=target/dependency-check-report.html"
                    }
                }
           }
        }

    }
}
