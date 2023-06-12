pipeline{
    agent{
        label 'Master'
    }

    tools {
        maven 'Maven_3.9.0'
    }

    stages{
        stage('SCM Checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ramya-03/java-maven-war-app.git']])
            }

        }

        stage('Maven-Build'){
            steps{
                sh 'mvn clean install'
            }
        }

        stage('Sonar Scan'){
            steps{
                withSonarQubeEnv("SonarQube_Assessment") {
                    sh "${tool("Sonar_4.8")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://ec2-3-25-255-99.ap-southeast-2.compute.amazonaws.com:9000/ \
                    -Dsonar.login=sqp_207edbc003d313f9fa4572f1dd0ea33fa0d23846 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven-app-war"
                }
            }
        }

        stage('Nexus Upload'){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }

        stage('deployment'){
            agent{
                label 'Ansible'
            }
            steps{
                sh 'ansible-playbook -i inventory deployment_playbook.yml -e "build_number=${BUILD_NUMBER}"'
            }
        }

    }
}
