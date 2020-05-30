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
        stage("upload artifatc to s3"){
            steps{
                s3Upload consoleLogLevel: 'INFO', dontSetBuildResultOnFailure: false, dontWaitForConcurrentBuildCompletion: false, entries: [[bucket: 's3uploadtoart', excludedFile: '', flatten: true, gzipFiles: false, keepForever: false, managedArtifacts: false, noUploadOnFailure: false, selectedRegion: 'ap-south-1', showDirectlyInBrowser: false, sourceFile: '**/*.jar', storageClass: 'STANDARD', uploadFromSlave: false, useServerSideEncryption: false]], pluginFailureResultConstraint: 'SUCCESS', profileName: 's3uploadtoart', userMetadata: []
            }
        }
         stage("upload artifacts to nexus"){
            steps{
                nexusPublisher nexusInstanceId: 'Nexus-3', nexusRepositoryId: 'repo-1', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'C:\\Program Files (x86)\\Jenkins\\workspace\\nexus_test\\target\\project1-1.0-SNAPSHOT.jar']], mavenCoordinate: [artifactId: '$BUILD_TIMESTAMP', groupId: 'DEV', packaging: 'jar', version: '$BUILD_ID']]]
            }
        }
     }       
}
