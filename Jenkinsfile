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
                    build(job: "${env.JOB_NAME}", parameters: [], propagate: false, quietPeriod: 0)
                } else {
                    error "No previous successful build found."
                }
            }
        }
    }
}