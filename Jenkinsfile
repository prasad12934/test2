pipeline {
    agent any
    tools {
        maven 'Maven3'
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build & Deploy') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'anypoint-connected-app',  // matches Jenkins credential ID
                    usernameVariable: 'CONNECTED_APP_CLIENT_ID',
                    passwordVariable: 'CONNECTED_APP_CLIENT_SECRET'
                )]) {
                    writeFile file: 'settings.xml', text: """
<settings>
  <servers>
    <server>
      <id>exchange</id>
      <username>~~~Client~~~</username>
      <password>${CONNECTED_APP_CLIENT_ID}~?~${CONNECTED_APP_CLIENT_SECRET}</password>
    </server>
    <server>
      <id>anypoint-exchange-v3</id>
      <username>~~~Client~~~</username>
      <password>${CONNECTED_APP_CLIENT_ID}~?~${CONNECTED_APP_CLIENT_SECRET}</password>
    </server>
  </servers>
</settings>
"""
                    bat """
                        mvn clean deploy mule:deploy -s settings.xml ^
                            -DskipTests ^
                            -DconnectedAppClientId=%CONNECTED_APP_CLIENT_ID% ^
                            -DconnectedAppClientSecret=%CONNECTED_APP_CLIENT_SECRET% ^
                            -DapplicationName=test-p-d-d-jenkins
                    """
                }
            }
        }
    }
    post {
        always {
            bat 'del settings.xml'
        }
        success {
            echo 'Deployment Successful'
        }
        failure {
            echo 'Deployment Failed'
        }
    }
}