pipeline {
    agent {
        label 'agent'
    }

    tools {
        maven 'maven_3.9.6'
    }

    stages {
        stage('SCM Checkout') {
            steps {
                checkout changelog: false, poll: false, scm: [
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/Nsha13/java-maven-war-app.git']]
                ]
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan') {
            steps {
                withSonarqubeEnv('Sonarqube') {
                    sh """
                        ${tool('Sonar_5.0.1')}/bin/sonar-scanner \
                        -Dsonar.host.url=http://13.201.83.50:9000/ \
                        -Dsonar.login=sqp_dba6a0ab99570567380ce140718e5a4178fc4618 \
                        -Dsonar.java.binaries=target \
                        -Dsonar.projectKey=java-maven-war-app*
                    """
                }
            }
        }

        stage('Nexus Upload') {
            steps {
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('Deployment') {
            agent {
                label 'agent1'
            }
            steps {
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }
    }
}
