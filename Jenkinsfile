pipeline {
    agent any

    tools {
        maven 'Maven'
        nodejs "NodeJS"
    }

    stages{
      
        stage ('Initial'){
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

        stage ('Compile'){
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
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=Tarea4 -Dsonar.host.url=http://sonarqube:9000 -Dsonar.login=affe25ffb86ec3efa60b94963ec8179d4f57f70d -Dsonar.java.binaries=**/*"
                    }
                }
           }
	    }

        stage ('SCA') {
            steps {
                echo '========================================='
                echo '                SCA '
                echo '========================================='
                sh 'mvn org.owasp:dependency-check-maven:check'
                //dependencyCheckPublisher failedNewCritical: 5, failedTotalCritical: 10, pattern: 'terget/dad.xml', unstableNewCritical: 3, unstableTotalCritical: 5
            }
        }
        
        stage('ZAP'){
        	steps{
        	    figlet 'Owasp Zap DAST'
        		script{
        			env.DOCKER = tool "Docker"
        			env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        			env.TARGET = 'https://demo.testfire.net/'
        			sh '${DOCKER_EXEC} rm -f zap2'
        			sh '${DOCKER_EXEC} pull owasp/zap2docker-stable'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.100.4" --rm -e LC_ALL=C.UTF-8 -e LANG=C.UTF-8 --name zap2 -u zap -p 8090:8080 -d owasp/zap2docker-stable zap.sh -daemon -port 8080 -host 0.0.0.0 -config api.disablekey=true'
                    sh '${DOCKER_EXEC} run --add-host="localhost:192.168.100.4" -v /Users/asajuro/Documents/BCI/AnalyzeQAS/Jenkins-Practica/USACH/Dockerfile/zap/jenkins_home/tools:/zap/wrk/:rw --rm -i owasp/zap2docker-stable zap-baseline.py -t "https://demo.testfire.net/" -I -r zap_baseline_report2.html -l PASS'
        		}
        	}
        }


        stage('Scan Docker'){
            steps{
                figlet 'Scan Docker'
                script{
                    env.DOCKER = tool "Docker"
        			env.DOCKER_EXEC = "${DOCKER}/bin/docker"
        				sh '''
                        ${DOCKER_EXEC} run --rm -v $(pwd):/root/.cache/ aquasec/trivy python:3.4-alpine
                        '''
                        sh '${DOCKER_EXEC} rmi aquasec/trivy'
    
                }
            }
        }
    }

    post { 
        always {
            script {
                def COLOR_MAP = [
                    'SUCCESS': 'good', 
                    'FAILURE': 'danger',
                ]
                
            }
            publishHTML([
        		allowMissing: false,
        		alwaysLinkToLastBuild: false,
        		keepAll: false,
        		reportDir: '/var/jenkins_home/tools',
        		reportFiles: 'zap_baseline_report2.html',
        		reportName: 'HTML Report',
        		reportTitles: ''
            ])
            
            /*slackSend channel: 'notificacion-jenkins',
                color: 'danger',
                message: "Se ha terminado una ejecucion del pipeline."*/
        }
     
    }
}