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
                    "cat > www/.env << 'EOF' ${credentials} EOF"
                    php migrate.php
                    '
                """    
            }
        }
    }
}

