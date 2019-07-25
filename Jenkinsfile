node('haimaxy-jnlp') {
    stage('Clone') {
      echo "1.clone"
      git url: "https://github.com/xiaoluoge11/jenkins_demo.git"
      script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        }
    }
    stage('Test') {
      echo "2.Test Stage"
    }
    stage('Build') {
      echo "3.Build Docker Image Stage"
      sh "docker build -t 192.168.1.27/devops/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
      echo "4.Push Docker Image Stage"
      withCredentials([usernamePassword(credentialsId: 'Harbor', passwordVariable: 'HarborPassword', usernameVariable: 'HarborUser')]) {
            sh "docker login -u ${HarborUser} -p ${HarborPassword} 192.168.1.27"
            sh "docker push 192.168.1.27/devops/jenkins-demo:${build_tag}"
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
