node('aws-test') {
    stage('Clone') {
        echo "1.Clone Stage"
        git url: "https://github.com/container-lab/jenkins-demo.git"
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        }
    }
    stage('Test') {
      echo "2.Test Stage"
    }
    stage('Build') {
        echo "3.Build Docker Image Stage"
        sh "docker build -t 182335798701.dkr.ecr.cn-northwest-1.amazonaws.com.cn/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4.Push Docker Image Stage"
        withDockerRegistry(credentialsId: 'ecr:cn-northwest-1:44d5ee4f-232d-408d-bbf0-8ebbc8156fd0', url: 'https://182335798701.dkr.ecr.cn-northwest-1.amazonaws.com.cn') {
           sh "docker push 182335798701.dkr.ecr.cn-northwest-1.amazonaws.com.cn/jenkins-demo:${build_tag}"
        }
    }
    stage('Deploy') {
        echo "5. Deploy Stage"
        def userInput = input(
            id: 'userInput',
            message: 'Choose a deploy environment',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition',
                    choices: "Dev\nQA\nProd",
                    name: 'Env'
                ]
            ]
        )
        echo "This is a deploy step to ${userInput}"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        if (userInput == "Dev") {
            // deploy dev stuff
        } else if (userInput == "QA"){
            // deploy qa stuff
        } else {
            // deploy prod stuff
        }
        sh "kubectl apply -f k8s.yaml"
    }
}
