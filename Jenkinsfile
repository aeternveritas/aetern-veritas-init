pipeline {
    agent any

    environment {
        // Your Google Cloud Project ID
        CLOUDSDK_CORE_PROJECT = 'nice-storm-457303-e8'
        // The email of the service account you are using
        CLIENT_EMAIL = 'jenkins-gcloud@nice-storm-457303-e8.iam.gserviceaccount.com'
        // The GCS bucket you are uploading to
        GCS_BUCKET = 'gs://aeternveritas-git'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // This is the standard way to check out your code from the
                // Git repository configured in the Jenkins job.
                checkout scm
            }
        }

        stage('Authenticate and Upload to GCS') {
            steps {
                // Use withCredentials to securely load your 'gcloud-creds' file.
                // The path to this secret file will be stored in the GCLOUD_KEY_FILE variable.
                sh '''
                        echo "--- Authenticating with service account ---"
                        gcloud config set account 'gcs-master@nice-storm-457303-e8.iam.gserviceaccount.com'

                        echo "--- Verifying gcloud configuration ---"
                        gcloud --version
                        gcloud config list

                        echo "--- Syncing files to GCS bucket: ${GCS_BUCKET} ---"
                        # Use 'rsync' to efficiently upload files. It only copies changed files
                        # and the --delete-unmatched-destination-objects flag ensures the bucket
                        # perfectly mirrors your repository.
                        gcloud storage rsync . "${GCS_BUCKET}" --delete-unmatched-destination-objects
                    '''
            }
        }
    }

    post {
        always {
            // This block runs after every build, regardless of whether it succeeded or failed.
            script {
                echo "--- Finalizing build ---"
                // Revoke the credentials as a security best practice.
                // Clean up the workspace to save disk space.
                cleanWs()
            }
        }
    }
}
