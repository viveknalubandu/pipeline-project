def artifactname = "sp-boot-app.jar"
def repoName = "sp-boot-app-repo"
def pipelineName = "devops_vk_pipeline"
def semanticVersion = "${env.BUILD_NUMBER}.0.0"
def packageName = "spboot-package_${env.BUILD_NUMBER}"
def version = "${env.BUILD_NUMBER}.0"
/////def orchToolId="1153dbd753f100107109ddeeff7b122e"  
//${env.BUILD_NUMBER}
pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage("checkout") {
            steps {
                
                echo "Building" 
                checkout scm
            }
        }

        stage("build") {
            steps {
                sh 'mvn clean install -DskipTests'
                echo 'Building..'
                echo "Pipeline name is ${env.JOB_NAME}"
 				echo "Pipleine run rumber is ${env.BUILD_NUMBER}"
 				echo "Stage name is ${env.STAGE_NAME}"
 				echo "GIT branch is ${env.GIT_BRANCH}"
                echo "globalprops -- ${env.snartifacttoolId} -- ${env.snhost} -- ${env.snuser} -- ${env.snpassword} ";
	        snDevOpsArtifact(artifactsPayload:"""{"artifacts": [{"name": "pipeline-demo-webapp.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "pipeline-demo"}],"stageName": "build"}""")
                
            }
        }

        stage('unit-tests') {
            steps {
                echo "Unit Test"
                sh "mvn test"
                sleep 5
                snDevOpsPackage(name: "devops_pipeline_demo_${version}", artifactsPayload: """{"artifacts": [{"name": "pipeline-demo-webapp.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "pipeline-demo"}]}""")
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml' 
                }
          }
        }

        stage("deploy") {
            stages{
                stage('deploy to dev') {
                    when{
                        branch 'dev'
                    }
                    steps{
                        echo "deploy in UAT"
                    }
                }
                stage('deploy to prod') {
                    when {
                        branch 'master'
                    }
                    steps{
                        echo "deploy in prod"
                        snDevOpsChange()
                    }
                }
            }
        }
	
    }

}
