pipeline {
    agent { 
        node {
            label 'ubuntu24_worker'
            }
    }
    triggers {
        pollSCM '* * * * *'
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                python3 sample.py
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing N.."
            }
        }
        stage('Deliver') {
            steps {
                echo "${env.JOB_NAME}"
                echo "${currentBuild.getPreviousBuild().result}"
                echo 'Deliver....'
            }
        }
    }
    post {
        failure {
            script {
                def lastSuccessfulBuild = currentBuild.getPreviousBuild()
                
                if (lastSuccessfulBuild != null && lastSuccessfulBuild.result == 'SUCCESS') {
                    echo "Reverting to last successful build: ${lastSuccessfulBuild.number}"
                    build job: env.JOB_NAME, parameters: [[$class: 'StringParameterValue', name: 'BUILD_NUMBER', value: lastSuccessfulBuild.number.toString()]], propagate: false
                    //build(job: "${env.JOB_NAME}", parameters: [[$class: 'IntParameterValue', name: 'BUILD_NUMBER', value: lastSuccessfulBuild.number]], propagate: false, quietPeriod: 0)
                    //build(job: "${env.JOB_NAME}", parameters: [], propagate: false, quietPeriod: 0)
                } else {
                    error "No previous successful build found."
                }
            }
        }
    }
}