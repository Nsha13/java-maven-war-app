pipeline{
    agent{
        label 'agent'
    }

    tools {
        maven 'Maven_3.9.6'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout changelog: false, poll: false, scm: scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Nsha13/java-maven-war-app.git']])

            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan'){
            steps{
                withSonarQubeEnv("Sonarqube") {
                    sh "${tool("Sonar_5.0.1")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://3.26.29.70:9000/ \
                    -Dsonar.login=sqp_05d5ccb88388c9a0a8d6042881d250641d726ad0 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven-war-app"
                }
            }
        } 

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
