pipeline {
    agent any
    tools {
        maven "mymaven1"
    }
    parameters{
        string(name:'Env',defaultValue:'Test',description:'Environment to deploy')
        booleanParam(name:'executeTests',defaultValue: true,description:'decide to run tc')
        choice(name:'APPVERSION',choices:['1.1','1.2','1.3'])

   }

    stages {
        stage('Compile') {
            steps {
                script {
                    echo 'Compile World'
                    echo "Deploying to ${params.Env} environment"
                    sh "mvn compile"
                }
            }
        }
        stage('CodeReview') {
            steps {
                script {
                    echo 'CodeReview World'
                    sh "mvn pmd:pmd"
                }
            }
        }
        stage('UnitTest') {
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
            steps {
                script {
                    echo 'CodeCoverageAnalysis World'
                    echo "Deploying to ${params.Env} environment"
                    sh "mvn verify"
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    echo 'Package World'
                    echo "Packaging version is ${params.APPVERSION}"
                    sh "mvn package"
                }
            }
        }
        stage('PublishToJFrog') {
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
