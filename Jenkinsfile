pipeline {
    agent any
    parameters {
        choice( name: 'NameSpace', choices: ['dev','test','staging','pre-prod','prod','post-prod'], description: 'Select the namespace')
        string( name: "Slave_Number", defaultValue: "2", description: "Enter the number of slaves required")
        file(name: 'script', description: 'Upload the test script')
    }
    stages {
        stage('checkout SCM') {
            steps {
                git credentialsId: 'git_creds', url: 'https://github.com/Guruprasad4126/jmeter-kubernetes1.git'
            }
        }
        stage('execute script') {
            steps {
                withAWS(credentials: 'aws_creds', region: 'us-east-1') {
                withKubeConfig(caCertificate: '', clusterName: '', contextName: '', credentialsId: 'kubeconfig', namespace: '', serverUrl: '') {
                   sh 'aws eks --region us-east-1 update-kubeconfig --name eks-photoncluster'
                   sh 'chmod +x jmeter_cluster_create.sh dashboard.sh start_test.sh'
                   sh './jmeter_cluster_create.sh'
                   sh './dashboard.sh'
                   sh 'kubectl scale --replicas=${Slave_Number} deploy jmeter-slaves'
                   sh './start_test.sh'
                }
                }
            }
        }
    }
}
