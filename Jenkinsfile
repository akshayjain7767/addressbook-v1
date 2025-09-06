pipeline {
    agent none
    tools {
        maven "mymaven1"
    }
    parameters{
        string(name:'Env',defaultValue:'Test',description:'Environment to deploy')
        booleanParam(name:'executeTests',defaultValue: true,description:'decide to run tc')
        choice(name:'APPVERSION',choices:['1.1','1.2','1.3'])

   }
   environment {
    BUILD_SERVER='ec2-user@172.31.46.169'
   }

    stages {
        stage('Compile') {
            agent any
            steps {
                script {
                    echo 'Compile World'
                    echo "Deploying to ${params.Env} environment"
                    sh "mvn compile"
                }
            }
        }
        stage('CodeReview') {
            agent {label 'linux_slave1'}
            steps {
                script {
                    echo 'CodeReview World'
                    sh "mvn pmd:pmd"
                }
            }
        }
        stage('UnitTest') {
            agent any
            when {
                expression {
                    params.executeTests == true
                }
            }
            steps {
                script {
                    echo 'UnitTest World'
                    echo "Deploying to ${params.Env} environment"
                    sh "mvn test"
                }
            }
            post {
                always {
                    junit "target/surefire-reports/*.xml"
                }
            }
        }
        stage('CodeCoverageAnalysis') {
            agent any
            steps {
                script {
                    echo 'CodeCoverageAnalysis World'
                    echo "Deploying to ${params.Env} environment"
                    sh "mvn verify"
                }
            }
        }
        stage('Package') {
            agent any
            steps {
                script {
                    sshagent(['slave3']) {
                        echo 'Package World'
                        echo "Packaging version is ${params.APPVERSION}"
                        sh "scp -o StrictHostKeyChecking=no server-script.sh ${BUILD_SERVER}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER} 'bash ~/server-script.sh'"
}
                }
            }
        }
        stage('PublishToJFrog') {
            agent any
            input {
                message "Do you want to publish the artifact to Jfrog Or Nexus?"
                ok "Yes,publish"
                parameters {
                    choice(name: "Platform", choices: ["Nexus", "Jfrog"])
                }
            }
            steps {
                script {
                    echo 'PublishToJFrog World'
                    echo "Deploying to ${params.Env} environment"
                    sh "mvn -U deploy -s settings.xml"
                }
            }
        }
    }
}
