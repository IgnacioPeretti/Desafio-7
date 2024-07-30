pipeline {
    agent { label 'ansible-controller'}
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
                    // Creamos los directorios necesarios en el agente
                    sh """
                    mkdir -p ~/inventories/dev ~/inventories/staging ~/inventories/main ~/playbook
                    """

                    // Copiamos los archivos necesarios al agente remoto
                    sh """
                    cp ${WORKSPACE}/inventories/dev/inventory.init ~/inventories/dev/
                    cp ${WORKSPACE}/inventories/staging/inventory.init ~/inventories/staging/
                    cp ${WORKSPACE}/inventories/main/inventory.init ~/inventories/main/
                    cp ${WORKSPACE}/playbook/main.yml ~/playbook/
                    """
                }
            }
        }
        stage('Run Ansible Playbook from Jenkins') {
            steps {
                sh """
                ansible-playbook -i ~/${env.INVENTORY} ~/playbook/main.yml --ssh-extra-args='-o StrictHostKeyChecking=no'
                """
            }
        }
    }
}