pipeline {
    agent any

    environment {
        CLOUDSDK_CORE_PROJECT = 'nice-storm-457303-e8'
        GCS_BUCKET = 'gs://aeternveritas-git'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Authenticate and Upload to GCS') {
            steps {
                withCredentials([file(credentialsId: 'gcs-sa-master', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                        echo "--- Authenticating with service account ---"
                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                        gcloud config set project ${CLOUDSDK_CORE_PROJECT}

                        echo "--- Verifying gcloud configuration ---"
                        gcloud --version
                        gcloud config list

                        echo "--- Syncing files to GCS bucket: ${GCS_BUCKET} ---"
                        gcloud storage rsync . "${GCS_BUCKET}" --delete-unmatched-destination-objects
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "--- Finalizing build ---"
                cleanWs()
            }
        }
    }
}
