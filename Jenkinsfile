node{
    def GITHUB_PROJECT_URL = 'https://github.com/bhuvi-12/goapp.git'
    def JENKINS_WORKSPACE = '/var/lib/jenkins/workspace'
    def root = tool type: 'go', name: 'go-tool'
    
    stage('Code Checkout'){
        git branch: "main", url: "${GITHUB_PROJECT_URL}"
    }
    withEnv(["GOROOT=${root}", "PATH+GO=${root}/bin"]){
        stage('Dependencies version'){
            sh 'go version'
        }
        stage('test'){
            sh 'go test'
        }
        stage('build'){
            sh 'go build main.go'
        }
        stage('package'){
            sh 'zip main.zip main'
        }
    }
    stage('SonarQube code analysis'){
        def scannerHome = tool 'SonarScanner 4.0';
        withSonarQubeEnv('SonarQube-server') { 
          sh "${scannerHome}/bin/sonar-scanner"
        }
    }
    stage('Upload build artifacts'){
        def server = Artifactory.server 'jfrog-server'
        def buildNumber = currentBuild.number
        def uploadSpec = """{
            "files": [
                {
                    "pattern": "${JENKINS_WORKSPACE}/${env.JOB_NAME}/*.mod",
                    "pattern": "${JENKINS_WORKSPACE}/${env.JOB_NAME}/*.zip",
                    "target": "${env.JOB_NAME}/${buildNumber}/"
                }
              ]
            }"""
        def buildInfo = server.upload spec:uploadSpec
        server.publishBuildInfo buildInfo
    }
    stage('Download artifacts from JFrog'){
        def downloadSpec = """{
            "files": [
                {
                   "pattern": "${JENKINS_WORKSPACE}/${env.JOB_NAME}/*.zip",
                   "target": "${env.JOB_NAME}/${buildNumber}/"
                }
             ]
            }"""
        server.download spec: downloadSpec
    }
    //create a Virtual Machine & host this on a VM.
    //create a service file to start the application whenever the VM starts.
    //create nginx config file to port-forward
    stage('Deploy/Run'){
        sh 'unzip main.zip'
        sh 'service goweb restart'
    }
}
