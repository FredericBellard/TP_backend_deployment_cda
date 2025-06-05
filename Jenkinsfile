pipeline {
    agent {
        label 'agent-php'
        
    }
    stages {
        stage('Installer dépendances') {
            steps {
                git branch: 'main', url: 'https://github.com/FredericBellard/TP_backend_deployment_cda.git'
            }
        }
        stage('Contrôle qualité'){
            steps{
                sh """
                sonar-scanner \
                -Dsonar.projectKey=fred_TP_backend_deployment_cda \
                -Dsonar.sources=. \
                -Dsonar.host.url=https://669b-212-114-26-208.ngrok-free.app \
                -Dsonar.token=${SonarToken}
                """
            }
        }       
        stage('Déploiement') {
            steps {
                   sh """
                    lftp -d -u ${Login},${Mdp} ${Lienftp} -e "
                        mirror -R /home/jenkins/workspace/fred_TP_backend_deployment_cda/ www/;
                        bye
                    "
                """             
            }
        }
        stage('composer'){
            steps{
                sh """
                    sshpass -p ${Mdp} ssh -o StrictHostKeyChecking=no ${Lienssh} '
                    cd www/ &&
                    composer install
                    '
                """   
            }
        }
        stage('migrate'){
            steps{
                sh """
                    sshpass -p ${Mdp} ssh -o StrictHostKeyChecking=no ${Lienssh} '
                    cd www/ &&
                    cat > www/.env <<EOF 
                    ${credentials} 
                    EOF
                    php migrate.php
                    '
                """    
            }
        }
    }
}

