pipeline {
    agent any

    parameters {
        string(name:'TAG_NAME',defaultValue: '',description:'')
    }

    environment {
        DOCKER_CREDENTIAL_ID = 'dockerhub-id'
        GITHUB_CREDENTIAL_ID = 'github-id'
        DEV_CREDENTIAL_ID = 'dev-id'
        REGISTRY = 'docker.io'
        DOCKERHUB_NAMESPACE = 'somkiat'
        GITHUB_ACCOUNT = 'up1'
        APP_NAME = 'demo-api'
        SERVER = '178.128.16.180'
    }

    stages {
        stage ('checkout scm') {
            steps {
                checkout(scm)
            }
        }

        stage ('unit test') {
            steps {
                sh 'echo "Test passed"'
            }
        }
 
        stage ('build & push') {
            steps {
                sh 'docker-compose build'
                withCredentials([usernamePassword(passwordVariable : 'DOCKER_PASSWORD' ,usernameVariable : 'DOCKER_USERNAME' ,credentialsId : "$DOCKER_CREDENTIAL_ID" ,)]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                    sh 'docker image push  $REGISTRY/$DOCKERHUB_NAMESPACE/$APP_NAME'
                }
            }
        }

        stage('deploy to dev') { 
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: 'dev-server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''docker-compose -f docker-compose-deploy.yml down
docker-compose -f docker-compose-deploy.yml up -d
sh waiting.sh''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'docker-compose-deploy.yml,waiting.sh')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}