pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('DOCKER_HUB_CREDENTIAL')
    VERSION = "${env.BUILD_ID}"

  }

  tools {
    maven "Maven"
  }

  stages {

    stage('Maven Build'){
        steps{
        sh 'mvn clean package  -DskipTests'
        }
    }

     stage('Run Tests') {
      steps {
        sh 'mvn test'
      }
    }

    stage('SonarQube Analysis') {
  steps {
    sh 'mvn clean org.jacoco:jacoco-maven-plugin:prepare-agent install sonar:sonar -Dsonar.host.url=http://34.207.212.79:9000/ -Dsonar.login=squ_1a3e8f94c11c7ff17dee79d880e33f59c10a348d'
  }
}


   stage('Check code coverage') {
            steps {
                script {
                    def token = "squ_1a3e8f94c11c7ff17dee79d880e33f59c10a348d"
                    def sonarQubeUrl = "http://34.207.212.79:9000/api"
                    def componentKey = "com.codeddecode:restaurantlisting"
                    def coverageThreshold = 80.0

                    def response = sh (
                        script: "curl -H 'Authorization: Bearer ${token}' '${sonarQubeUrl}/measures/component?component=${componentKey}&metricKeys=coverage'",
                        returnStdout: true
                    ).trim()
					

                    def coverage = sh (
					
                        script: "echo '${response}' | jq -r '.component.measures[0].value'",
                        returnStdout: true
                        //script: "echo '${response}'"
					)
					.trim().toDouble()

                    echo "Coverage: ${coverage}"

                    if (coverage < coverageThreshold) {
                        error "Coverage is below the threshold of ${coverageThreshold}%. Aborting the pipeline."
                    }
                }
            }
        } 

  }

}


