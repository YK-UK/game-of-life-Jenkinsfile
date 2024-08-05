pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven 'MVN'
        jdk 'Java8_U202'
    }

    stages {
        stage('Build') {
            steps {
                // Get some code from a GitHub repository
                git 'https://github.com/YK-UK/game-of-life-test.git'

                // Run Maven on a Unix agent.
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
                sh 'mvn javadoc:javadoc'
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    //junit 'build/reports/**/*.xml'
                    //junit '**/reports/junit/*.xml'
                    junit '**/target/surefire-reports/*.xml'
                    archiveArtifacts '**/target/*.jar'
                    javadoc javadocDir: 'gameoflife-core/target/site/apidocs', keepAll: false
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: '', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }
        stage('Test') {
            steps {
                // Maven test
                sh 'mvn test'
            }

        }
        stage('Deploy') {
            steps {
                // deploy on Tomcat9
                deploy adapters: [tomcat9(credentialsId: 'f9b6fb37-46cc-49a0-ae83-fd1443204cc7', path: '', url: 'http://127.0.0.1:8081/')], contextPath: 'game-of-life-test', war: '**/*.war'
            }
            
            post {
                success {
                    //notifyEvents attachBuildLog: true, message: 'Build <b>$BUILD_ID</b> - Deployed <b>successfully</b>', token: '2aqw9mosj_uudtpo6_fxrxofrxdmetph'
                    notifyEvents attachBuildLog: true, message: '$PROJECT_NAME - Build # $BUILD_NUMBER - Deploy $BUILD_STATUS!', token: '2aqw9mosj_uudtpo6_fxrxofrxdmetph'
                }
                
            }

        }
    }
}
