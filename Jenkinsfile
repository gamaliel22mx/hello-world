#!groovy

def server
def rtMaven
def buildInfo

node ('master'){
    stage('Init') {
        server = Artifactory.server 'artifactory_server'
        rtMaven = Artifactory.newMavenBuild()
        buildInfo = Artifactory.newBuildInfo()
        rtMaven.tool = 'maven_3'
        rtMaven.deployer snapshotRepo:'snapshot', server: server
        dir('gitRepo'){
            git 'https://github.com/gamaliel22mx/hello-world.git'
        }
    }
    stage('Build') {
        dir('gitRepo'){
            withEnv(["PATH+MAVEN=${tool 'maven_3'}/bin"]) {
                sh 'mvn clean'
            }
            parallel(
                unitTest:{
                    withEnv(["PATH+MAVEN=${tool 'maven_3'}/bin"]) {
                        sh 'mvn test'
                    }
                },
                staticAnalisys:{
                    sleep 5
                },
                commpile:{
                    withEnv(["PATH+MAVEN=${tool 'maven_3'}/bin"]) {
                        sh 'mvn build'
                        //rtMaven.run pom: 'pom.xml', goals: 'package -DskipTests', buildInfo: buildInfo
                    }
                }
            )
        }
    }
    stage('Publish Artifact') {
        dir('gitRepo'){
            //server.publishBuildInfo buildInfo
            sleep 5
        }
    }
    stage('Functional Test') {
        sleep 5
    }
    stage('Manual Test') {
        sleep 5
    }
    stage('Promote') {
        timeout(time: 45, unit:'SECONDS') {
            input message:'Promote artifact to Releases?', ok: 'Promote'
        }
        sleep 5
    }
}
