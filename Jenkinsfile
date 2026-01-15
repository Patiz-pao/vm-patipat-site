pipeline {
    agent any

    environment {
        SAFE_BRANCH_NAME = "${env.BRANCH_NAME.toLowerCase().replaceAll('[^a-z0-9-]', '-')}"
        
        IMAGE_NAME = "season_snap:${SAFE_BRANCH_NAME}"
        CONTAINER_NAME = "app-${SAFE_BRANCH_NAME}"
        NETWORK_NAME = "app_default"
    }

    stages {
        stage('Build Image') {
            steps {
                script {
                    echo "Building for branch: ${env.BRANCH_NAME}"
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Deploy Preview') {
            steps {
                script {
                    echo "🚀 Deploying to http://${SAFE_BRANCH_NAME}.napatipat.site"
                    
                    sh "docker rm -f ${CONTAINER_NAME} || true"

                    sh """
                        docker run -d \
                        --name ${CONTAINER_NAME} \
                        --restart always \
                        --network ${NETWORK_NAME} \
                        --label "traefik.enable=true" \
                        --label "traefik.http.routers.${SAFE_BRANCH_NAME}.rule=Host(\`${SAFE_BRANCH_NAME}.napatipat.site\`)" \
                        --label "traefik.http.services.${SAFE_BRANCH_NAME}.loadbalancer.server.port=80" \
                        ${IMAGE_NAME}
                    """
                }
            }
        }
    }
}