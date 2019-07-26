node('haimaxy-jnlp') {
     //服务名称
     def service_name = "online"
     stage('Prepare') {
        echo "1.Prepare Stage"
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
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
      when {
                branch 'development' 
            }
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
	sh "sed -i 's/<SERVICE_NAME>/${service_name}/' k8s.yaml"
	sh "cat k8s.yaml"
        if (userInput == "Dev") {
            // deploy dev stuff
        } else if (userInput == "QA"){
            // deploy qa stuff
        } else {
            // deploy prod stuff
        }
        sh "kubectl apply -f k8s.yaml"
    }
   stage('Deploy') {
      echo "5. Deploy Stage"
      when {
                branch 'master'
            }   
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
        sh "sed -i 's/<SERVICE_NAME>/${service_name}/' k8s.yaml"
        sh "cat k8s.yaml"
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
