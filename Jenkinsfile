pipeline {
    agent any

    environment {
        CLOUDSDK_CORE_PROJECT = 'nice-storm-457303-e8'
        GCS_BUCKET = 'gs://aeternveritas-git'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "--- Cleaning workspace and checking out SCM ---"
                // It's good practice to clean the workspace before checkout
                cleanWs() 
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
                        gcloud config list

                        echo "--- [DEBUG] Listing contents of workspace before sync ---"
                        # This command will list all files and folders recursively.
                        # Check your Jenkins build log for its output.
                        ls -laR

                        echo "--- Syncing files to GCS bucket: ${GCS_BUCKET} ---"
                        # This command syncs the current directory to your GCS bucket.
                        gcloud storage rsync . "${GCS_BUCKET}" --delete-unmatched-destination-objects
                    '''
                }
            }
        }
    }

    post {
        always {
            script {
                echo "--- Build finished. Final workspace state will be cleaned by cleanWs() if not already done. ---"
                // The cleanWs() in the 'Checkout' stage makes this redundant, but it's safe to keep.
                cleanWs()
            }
        }
    }
}
