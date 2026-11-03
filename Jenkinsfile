node {
    withEnv([
        'AZURE_SUBSCRIPTION_ID=02b11d7e-009c-482c-8e06-316f38aa1668',
        'AZURE_TENANT_ID=67c6f763-4d90-454a-85f1-38531f0d1307',
        'RESOURCE_GROUP=jenkins-get-started-rg',
        'WEB_APP_NAME=lan-jenkins-java-20261102'
    ]) {
        stage('init') {
            checkout scm
        }

        stage('build') {
            sh 'mvn clean package'
        }

        stage('deploy') {
            withCredentials([usernamePassword(
                credentialsId: 'AzureServicePrincipal',
                usernameVariable: 'AZURE_CLIENT_ID',
                passwordVariable: 'AZURE_CLIENT_SECRET'
            )]) {
                sh '''
                    set +x
                    az login --service-principal \
                        --username "$AZURE_CLIENT_ID" \
                        --password "$AZURE_CLIENT_SECRET" \
                        --tenant "$AZURE_TENANT_ID" \
                        --output none

                    az account set \
                        --subscription "$AZURE_SUBSCRIPTION_ID"

                    az webapp deploy \
                        --resource-group "$RESOURCE_GROUP" \
                        --name "$WEB_APP_NAME" \
                        --src-path target/calculator-1.0.war \
                        --type war

                    az logout
                '''
            }
        }
    }
}
