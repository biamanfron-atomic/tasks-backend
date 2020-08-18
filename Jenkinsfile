pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }
//        stage ('Sonar Analysis'){
//            environment{
//                scannerHome = tool 'SONAR_SCANNER'
//            }
//            steps{
//                withSonarQubeEnv('SONAR_LOCAL'){
//                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.99.100:9000 -Dsonar.login=1e8570fc2de908b9a97c0dd2ab22ef3ca1128212 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java"
//                }
//            }
//        }
//        stage ('Quality Gate'){
//            steps{
//                sleep(5)
//                timeout(time: 1, unit: 'MINUTES'){
//                    waitForQualityGate abortPipeline: true
//                }
//            }
//        }
        stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test') {
            steps {
                dir('api-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/biamanfron-atomic/tasks-api-test'
                    bat 'mvn test'
                }
            }
        }
        stage ('Deploy Frontend') {
            steps {
                dir('frontend') {
                    git credentialsId: 'github_login', url: 'https://github.com/biamanfron-atomic/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage ('Functional Test') {
            steps {
                dir('functional-test') {
                    git credentialsId: 'github_login', url: 'https://github.com/biamanfron-atomic/tasks-functional-tests'
                    bat 'mvn test'
                }
            }
        }
    }
}

