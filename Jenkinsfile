pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Pull the Node.js image using Podman
                sh '/opt/homebrew/bin/podman pull docker.io/library/node:18-alpine'

                // Run the build process inside a Podman container
                sh '''
                WORKSPACE=$(pwd)
                /opt/homebrew/bin/podman run --rm \
                    -v "$WORKSPACE:/workspace" \
                    -w /workspace \
                    docker.io/library/node:18-alpine \
                    sh -c "
                        echo 'Running build process inside Podman container';
                        ls -la;
                        node --version;
                        npm --version;
                        npm ci;
                        npm run build;
                        ls -la
                    "
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                WORKSPACE=$(pwd)
                /opt/homebrew/bin/podman run --rm \
                    -v "$WORKSPACE:/workspace" \
                    -w /workspace \
                    docker.io/library/node:18-alpine \
                    sh -c "
                        test -f build/index.html
                        npm test
                    "
                '''
            }
        }
    }

    post {
        always {
            junit 'rest-results/junit.xml'
        }
    }
}