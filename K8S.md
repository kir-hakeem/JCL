pipeline
    
    pipeline {
        agent any
    
        environment {
            GKE_CLUSTER = 'your-cluster-name'          // Replace with your GKE cluster name
            GKE_ZONE = 'your-cluster-zone'            // Replace with your cluster zone
            GKE_PROJECT = 'your-project-id'           // Replace with your project ID
            GKE_NAMESPACE = 'default'                // Change if using a custom namespace
            DOCKER_IMAGE = 'gcr.io/your-project-id/your-app' // Docker image name
        }
    
        stages {
            stage('Checkout Code') {
                steps {
                    // Checkout code from GitHub
                    git 'https://github.com/your-repo/your-app.git'  // Replace with your repo
                }
            }
    
            stage('Build Docker Image') {
                steps {
                    script {
                        // Build Docker image
                        sh 'docker build -t $DOCKER_IMAGE:$BUILD_NUMBER .'
                    }
                }
            }
    
            stage('Push Docker Image to GCR') {
                steps {
                    withCredentials([file(credentialsId: 'gcloud-service-key', variable: 'GOOGLE_CREDENTIALS')]) {
                        script {
                            // Authenticate with Google Cloud using a service account
                            sh 'gcloud auth activate-service-account --key-file=$GOOGLE_CREDENTIALS'
                            sh 'gcloud config set project $GKE_PROJECT'
                            sh 'gcloud auth configure-docker'
    
                            // Push the Docker image to Google Container Registry
                            sh "docker push $DOCKER_IMAGE:$BUILD_NUMBER"
                        }
                    }
                }
            }
    
            stage('Deploy to GKE') {
                steps {
                    withCredentials([file(credentialsId: 'gcloud-service-key', variable: 'GOOGLE_CREDENTIALS')]) {
                        script {
                            // Authenticate with Google Cloud and configure kubectl
                            sh 'gcloud auth activate-service-account --key-file=$GOOGLE_CREDENTIALS'
                            sh 'gcloud container clusters get-credentials $GKE_CLUSTER --zone $GKE_ZONE --project $GKE_PROJECT'
    
                            // Deploy the application to GKE
                            sh "kubectl apply -f k8s/deployment.yaml"
                        }
                    }
                }
            }
    
            stage('Test Deployment') {
                steps {
                    script {
                        // Perform tests, e.g., health checks
                        sh 'kubectl rollout status deployment/your-app -n $GKE_NAMESPACE'
                        sh 'curl -f http://your-app.your-domain.com/health'  // Replace with actual health endpoint
                    }
                }
            }
        }
    
        post {
            always {
                // Actions to always perform, e.g., clean-up
                echo 'Pipeline execution completed.'
            }
            success {
                // Actions on success
                echo 'Build and deployment succeeded!'
            }
            failure {
                // Actions on failure
                echo 'Build or deployment failed!'
            }
        }
    }
