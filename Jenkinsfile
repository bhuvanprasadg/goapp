node{
    def GITHUB_PROJECT_URL = "https://github.com/bhuvi-12/goapp.git"
    def root = tool type: "go", name: "go-tool"
    
    stage('Code Checkout'){
        git branch: "main", url: "${GITHUB_PROJECT_URL}"
    }
    withEnv(["GOROOT=${root}", "PATH+GO=${root}/bin"]){
        sh 'go version'
        sh 'go build main.go'
    }
    stage('Restart service'){
        sh 'service goweb restart'
    }
}