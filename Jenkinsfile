pipeline {
    agent any

    stages {
        stage('Validate') {
            steps {
                echo 'Validating website files'

                sh '''
                    echo "Current Jenkins workspace:"
                    pwd

                    echo "Files downloaded from GitHub:"
                    ls -lh

                    echo "Checking index.html..."
                    test -s index.html

                    echo "Checking style.css..."
                    test -s style.css

                    echo "File sizes:"
                    wc -c index.html
                    wc -c style.css

                    echo "Required website files are available and not empty"
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying website to Nginx'

                sh '''
                    sudo rm -rf /usr/share/nginx/html/*
                    sudo cp index.html style.css /usr/share/nginx/html/

                    echo "Files deployed to Nginx:"
                    ls -lh /usr/share/nginx/html/

                    echo "Checking deployed file sizes:"
                    wc -c /usr/share/nginx/html/index.html
                    wc -c /usr/share/nginx/html/style.css
                '''
            }
        }

        stage('Restart Nginx') {
            steps {
                echo 'Restarting Nginx'

                sh '''
                    sudo systemctl restart nginx
                    sudo systemctl status nginx --no-pager
                '''
            }
        }

        stage('Test Website') {
            steps {
                echo 'Testing the deployed website'

                sh '''
                    echo "Checking deployed index.html..."
                    test -s /usr/share/nginx/html/index.html

                    echo "Checking HTTP response..."
                    curl --fail --silent --show-error \
                        http://localhost \
                        --output /tmp/nginx-response.html

                    echo "Website response:"
                    cat /tmp/nginx-response.html

                    echo "Checking expected text..."
                    grep -q "Deployment Successful" /tmp/nginx-response.html

                    echo "Website content test successful"
                '''
            }
        }
    }

    post {
        success {
            echo 'Nginx website deployed successfully!'
        }

        failure {
            echo 'Website deployment failed. Check the Jenkins console output.'
        }
    }
}
