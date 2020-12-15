pipeline {
  agent any
  stages {
    stage('compile') {
      steps {
        git 'https://github.com/pujithamothkuri/sampleJava.git'
        sh '/opt/apache-maven-3.6.3/bin/mvn compile'
        sleep 10
      }
    }

    stage('codereview-pmd') {
      post {
        success {
          recordIssues(tools: [acuCobol()])
        }

      }
      steps {
        sh '/opt/apache-maven-3.6.3/bin/mvn -P metrics pmd:pmd'
      }
    }

    stage('unit-test') {
      post {
        success {
          junit 'target/surefire-reports/*.xml'
        }

      }
      steps {
        sh '/opt/apache-maven-3.6.3/bin/mvn test'
      }
    }

    stage('codecoverage') {
      post {
        success {
          cobertura(autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false)
        }

      }
      steps {
        sh '/opt/apache-maven-3.6.3/bin/mvn cobertura:cobertura -Dcobertura.report.format=xml'
      }
    }

    stage('package') {
      steps {
        sh 'cd $WORKSPACE'
		    sh 'docker build -f Dockerfile -t thinkpad123/newcode:$BUILD_NUMBER '
		    sh 'docker login -u thinkpad123 -p $DOCKER_HUB_PWD '
		    sh 'docker push thinkpad123/newcode:$BUILD_NUMBER'

      }
    }
    stage('deploy') {
      steps {
        sh 'cd $WORKSPACE/deploy'
		    sh 'sudo su moth -c "ansible-playbook -i inv deploy.yml -e 'env=qa build=$Package_Build_Number'"'
      }
    }

  }
}
