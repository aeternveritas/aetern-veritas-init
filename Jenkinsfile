pipeline {
    agent any
    environment {
        CLOUDSDK_CORE_PROJECT = 'nice-storm-457303-e8'
        CLIENT_EMAIL = 'jenkins-gcloud@nice-storm-457303-e8.iam.gserviceaccount.com'
    }
    stages {
        stage('test') {
            steps {
                // Use withCredentials to expose the secret file as an environment variable
                // The variable will contain the path to the temporary file holding the credential.
                // It's safer and more reliable than directly binding in the 'environment' block.
                withCredentials([file(credentialsId: 'gcloud-creds', variable: 'GCLOUD_KEY_FILE')]) {
                    sh '''
                        gcloud auth activate-service-account --key-file="${GCLOUD_KEY_FILE}"
                        gcloud version
                        gcloud compute zones list --project="${CLOUDSDK_CORE_PROJECT}"
                    '''
                }
            }
        }
    }
    post {
        always {
            // Revoke the service account after the pipeline finishes (success or failure)
            // Ensure CLIENT_EMAIL is visible here.
            // If gcloud is not in the PATH, use its full path as discussed before.
            sh "gcloud auth revoke ${CLIENT_EMAIL}"
        }
    }
}
