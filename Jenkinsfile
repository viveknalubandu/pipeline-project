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
            }

        }

        stage('unit-tests') {
            steps {
                echo "Unit Test"
                sh "mvn test"
	snDevOpsArtifact(artifactsPayload:"""{"artifacts": [{"name": "devops_pipeline_demo.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "devops_pipeline_demo"}],"stageName": "unit-tests"}""")            
	snDevOpsChange()
                sleep 5
		snDevOpsArtifact(artifactsPayload:"""{"artifacts": [{"name": "devops_pipeline_demo_dev.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "devops_pipeline_demo_dev"}],"stageName": "unit-tests"}""")            
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
