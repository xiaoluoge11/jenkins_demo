node('haimaxy-jnlp') {
     //服务名称
     def service_name = "online"
     stage('获取代码') {
        echo "1.Prepare Stage"
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }
    stage('测试') {
      echo "2.Test Stage"
    }
    stage('构建容器') {
      echo "3.Build Docker Image Stage"
      sh "docker build -t 192.168.1.27/devops/jenkins-demo:${build_tag} ."
    }
    stage('保存仓库') {
      echo "4.Push Docker Image Stage"
      withCredentials([usernamePassword(credentialsId: 'Harbor', passwordVariable: 'HarborPassword', usernameVariable: 'HarborUser')]) {
            sh "docker login -u ${HarborUser} -p ${HarborPassword} 192.168.1.27"
            sh "docker push 192.168.1.27/devops/jenkins-demo:${build_tag}"
        }
    }
    stage('部署') {
      echo "5. Deploy Stage"
      def userInput = input(
            id: 'userInput',
            message: 'Choose a deploy environment',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition',
                    choices: 生产环境\n测试环境\n回滚代码",
                    name: 'Env'
                ]
            ]
        )
        echo "This is a deploy step to ${userInput}"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
	sh "sed -i 's/<SERVICE_NAME>/${service_name}/' k8s.yaml"
	sh "cat k8s.yaml"
        if (userInput == "生产环境") {
            // deploy dev stuff
	    echo "prodoution"
        } else if (userInput == "测试环境"){
            // deploy qa stuff
	    echo "test"
        } else {
            // deploy prod stuff
	    echo "roback"
        }
        sh "kubectl apply -f k8s.yaml"
    }
}
