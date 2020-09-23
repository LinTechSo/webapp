pipeline {
    agent none
    tools {
        maven 'maven'
    }
    stages {
        stage ('initialize maven') {
            agent {label 'master'}
            steps {
                sh '''
                        echo "PATH = ${PATH}"
                        echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
        stage ('Check-Git-Secrets') {
            agent {label 'master'}
            steps {
                sh 'rm trufflehog || true'
                sh 'docker run gesellix/trufflehog --json https://github.com/lintechso/webapp.git > trufflehog'
                sh 'cat trufflehog'
            }
        }
        stage ('Source Composition Analysis') {
            agent {label 'parham'}
            steps {
                sh 'rm owasp* || true'
                sh 'wget "https://raw.githubusercontent.com/lintechso/webapp/master/owasp-dependency-check.sh" '
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh'
                sh 'cat /home/parham/OWASP-Dependency-Check/reports'
                
            }
        }
        stage ('SAST') {
            agent {label 'master'}
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                    sh 'cat target/sonar/report-task.txt'
                }
            }
        }
        stage ('Build'){
            agent {label 'master'}
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
