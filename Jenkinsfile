
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out Hoppscotch repository'
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Validating repository structure'

                sh '''
                    test -f package.json
                    test -f pnpm-workspace.yaml
                    echo "Repository validation passed"
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Build stage will be configured next'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deployment to AWS EC2 will be configured later'
            }
        }
    }
}
