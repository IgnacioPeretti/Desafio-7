pipeline {
    agent {
        label 'ansible-controller'
    }
    stages {
        stage('Preparation') {
            steps {
                script {
                
                    if (env.BRANCH_NAME == 'dev') {
                        env.INVENTORY = 'inventories/dev/inventory.init'
                    } else if (env.BRANCH_NAME == 'staging') {
                        env.INVENTORY = 'inventories/staging/inventory.init'
                    } else if (env.BRANCH_NAME == 'main') {
                        env.INVENTORY = 'inventories/main/inventory.init'
                    }
                }
                echo "Using inventory: ${env.INVENTORY}"
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Copy Files') {
            steps {
                script {
                    // Crear directorios en el agente remoto
                    sh 'mkdir -p ~/inventories/dev'
                    sh 'mkdir -p ~/inventories/staging'
                    sh 'mkdir -p ~/inventories/main'
                    sh 'mkdir -p ~/playbook'
                    // Copiar los archivos necesarios al agente remoto
                    sh 'cp ${WORKSPACE}/inventories/dev/inventory.init ~/inventories/dev/'
                    sh 'cp ${WORKSPACE}/inventories/staging/inventory.init ~/inventories/staging/'
                    sh 'cp ${WORKSPACE}/inventories/main/inventory.init ~/inventories/main/'
                    sh 'cp ${WORKSPACE}/playbook/playbook.yml ~/playbook/'
                }
            }
        }
        stage('Run Playbook') {
            steps {
                sh """
                ansible-playbook -i ~/${env.INVENTORY} ~/playbook/playbook.yml --ssh-extra-args='-o StrictHostKeyChecking=no'
                """
            }
        }
    }
}