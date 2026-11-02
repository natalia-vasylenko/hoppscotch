
pipeline {
    agent any

    environment {
        VITE_BASE_URL = 'http://localhost:3000'
    }

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Repository checked out successfully'
            }
        }

        stage('Environment') {
            steps {
                sh '''
                    node --version
                    corepack --version
                    corepack pnpm --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    corepack pnpm install --frozen-lockfile
                '''
            }
        }

        stage('Lint') {
            steps {
                echo 'Running Hoppscotch lint checks'
        
                sh '''
                    corepack pnpm lint
                '''
            }
        }

       stage('Typecheck') {
            steps {
                echo 'Running TypeScript type checks'
        
                sh '''
                    corepack pnpm typecheck
                '''
            }
        }
      
       stage('Test') {
          steps {
              echo 'Running Hoppscotch tests'
      
              sh '''
                  CI=true corepack pnpm \
                    --filter '!@hoppscotch/js-sandbox' \
                    --filter '!@hoppscotch/common' \
                    -r --if-present run do-test
      
                  CI=true corepack pnpm \
                    --filter @hoppscotch/common \
                    exec vitest run \
                    --maxWorkers=1 \
                    --no-file-parallelism \
                    --testTimeout=30000
      
                  CI=true corepack pnpm \
                    --filter @hoppscotch/js-sandbox \
                    exec vitest run \
                    --maxWorkers=2 \
                    --no-file-parallelism
              '''
          }
      }
      
        stage('Build') {
            steps {
                echo 'Building Hoppscotch for production'
        
                sh '''
                    CI=true corepack pnpm \
                      -r \
                      --workspace-concurrency=2 \
                      run do-build-prod
                '''
            }
        }
      
        stage('Validate') {
            steps {
                sh '''
                    test -f package.json
                    test -f pnpm-lock.yaml
                    echo "Repository validation passed"
                '''
            }
        }

      stage('Docker Build') {
          steps {
              echo 'Building Hoppscotch Docker image'
      
              sh '''
                  docker buildx build \
                    --platform linux/amd64 \
                    -f prod.Dockerfile \
                    --target aio \
                    -t hoppscotch-custom:${BUILD_NUMBER} \
                    --load \
                    .
              '''
          }
      }
    }

    post {
        success {
            echo 'CI Pipeline completed successfully!'
        }
        failure {
            echo 'CI Pipeline failed. Check console output.'
        }
    }
}
