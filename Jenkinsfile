def doErrorHandling(func, err) {
    echo err.getMessage()
    echo "Error detected, pipeline stopped."
    currentBuild.result = 'ERROR'
    // inform github that the deployment ended


        wait: false
}

        def ghPullComment       // must be built up in this pipeline
        def ghStatusState       // must be built up in this pipeline
        def ghStatusDescription // must be built up in this pipeline

        def build_stage

properties([pipelineTriggers([githubPush()])])
pipeline {
    agent {
        docker { image 'enricoproietti/sonar-scanner:test' }
    }
     environment {
          scannerHome = tool 'sonarqube-scanner'
     }
    stages {
        stage('Prep') {
            when { buildingTag() }
            steps {
             git credentialsId: 'github-app', poll: false, url: "https://github.com/enrico-proietti/shapez.io.git", branch: "master"
             sh 'echo ciaobuuu'
             writeFile file: ".npmrc", text: "//registry.npmjs.org/:_authToken=$authToken"
            }
        }

stage('Test') {
            steps {
                script {
                try {
                sh 'node --version'
                sh "yarn install"
                if (fileExists('test')) {
                    build_stage = "yarn test"
                    sh "yarn test --coverage"
                } else {
                    echo 'No test file found, pipeline continues'
                }
                build_stage = "SQ scan"

                withSonarQubeEnv('sonarqube-scanner') {

                       sq_host = SONAR_HOST_URL
                       sh 'echo $SONAR_HOST_URL'
                       sh "sonar-scanner -X -Dsonar.projectKey=test -Dsonar.projectName=test  -Dsonar.login=05170e8c0619718a3516982543c2fd8ec082e266 -Dsonar.host.url=http://172.25.87.14:9000  -Dsonar.sources=./src -Dsonar.javascript.lcov.reportPaths=./coverage/lcov.info -Dsonar.sourceEncoding=UTF-8 -Dsonar.scm.provider=git -Dsonar.language=js -Dsonar.exclusions=**/node_modules/**,**/*.spec.js,src/mocks/** -Dsonar.coverage.exclusions=**/*.spec.js,src/mocks/**"
                    }
                } catch (err) {

                doErrorHandling(build_stage, err)
                sh 'echo ciaociao'
                doDeploy = false
                //return
                }

              }

            }
        }

stage('create an artifactory') {

  steps {
            script {
                try {
                    sh 'echo Creating an artifactory'

                } catch (err) {

                   doErrorHandling(build_stage, err)
                   sh 'echo ciaociao'
                   // doDeploy = false
                   //return
                }

             }

      }

  }
}
}

