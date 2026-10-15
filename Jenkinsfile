pipeline {
    agent any

    stages {
        stage('1. Install Apache2 (httpd)') {
            steps {
                echo '=== Updating packages and installing Apache2 ==='
                sh '''
                    apt-get update -y
                    apt-get install -y apache2 curl
                '''
            }
        }

        stage('2. Deploy Sample Site & Start Server') {
            steps {
                echo '=== Deploying sample webpage and starting Apache ==='
                sh '''
                    echo "<h1>Deployed via Jenkins Pipeline by Maksym Protsak</h1>" > /var/www/html/index.html
                    service apache2 restart || service apache2 start
                    service apache2 status
                '''
            }
        }

        stage('3. Generate Fake Requests (4xx Errors)') {
            steps {
                echo '=== Generating normal 200 and fake 4xx/5xx requests ==='
                sh '''
                    # Normal request (200 OK)
                    curl -I http://localhost/ || true

                    # Fake requests to trigger 404 Not Found error
                    curl -I http://localhost/non-existent-page-1 || true
                    curl -I http://localhost/fake-api-error-404 || true
                '''
            }
        }

        stage('4. Read and Check Logs for 4xx and 5xx') {
            steps {
                echo '=== Inspecting Apache Access and Error Logs ==='
                sh '''
                    echo "--- Checking 4xx and 5xx HTTP errors in /var/log/apache2/access.log ---"
                    grep -E ' (4[0-9]{2}|5[0-9]{2}) ' /var/log/apache2/access.log || echo "No 4xx/5xx lines found"

                    echo "--- Checking /var/log/apache2/error.log ---"
                    if [ -f /var/log/apache2/error.log ]; then
                        tail -n 20 /var/log/apache2/error.log
                    fi
                '''
            }
        }
    }

    post {
        always {
            echo 'Jenkins Pipeline completed successfully!'
        }
    }
}
