pipeline{
    agent{
        label 'master'
    }
    environment{
        def scannerHome = tool 'sonar-scanner'
    }
    tools {
        maven 'maven-3.6.3'
        git 'git'
        jdk 'jdk'
    }
    stages{
        stage("git_checkout"){
            steps{
               git credentialsId: 'gtithub', url: 'https://github.com/AnushaM-git/Jenkinsproject.git' 
            }
        }
        stage("maven_build"){
            steps{
                bat "mvn clean packages"
            }
            post{
                always{
                    mail to: 'anusha4a4@gmail.com',
                        subject: "Status of pipeline: ${currentBuild.fullDisplayName}",
                        body: "${env.BUILD_URL} has result ${currentBuild.result}"
                      
                }
                failure{
                    emailext attachLog: true, attachmentsPattern: "$JOB_NAME : $BUILD_NUMBER", body: "${env.BUILD_URL} has result ${currentBuild.result}", subject: "Status of pipeline: ${currentBuild.fullDisplayName}", to: 'anusha4a4@gmail.com'
                }
            }
        }
        stage("SonarQube_analysis") {
            steps{
                withSonarQubeEnv(installationName: 'sonar-8.1', credentialsId: 'sonar_qube') { 
                    bat "${scannerHome}/bin/sonar-scanner -D sonar.host.url=http://localhost:9000 -D sonar.projectKey=pro-1 -D sonar.login=admin -D sonar.password=admin -D sonar.sources=C:/Users/ANUSHA/Desktop/mvnpractice/pro-1/src/main/java/mvn"
                }
             }
         }
        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
     }       
}
