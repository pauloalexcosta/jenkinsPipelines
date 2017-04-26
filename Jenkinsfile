pipeline {
  agent any
  stages {
    stage('A Fresh Workspace') {
      steps {
        script {
          fileOperations([folderDeleteOperation('Reports'), fileDeleteOperation(excludes: '', includes: 'Report.zip')])
        }
        
      }
    }
    stage('Run JMeter Test') {
      steps {
        bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -n -t E:/JMeter/Resources/Webinar/Scripts/TaskManager.jmx -l TaskManager.jtl', encoding: 'UTF8')
        archiveArtifacts 'TaskManagerResults.csv'
      }
    }
    stage('Create Reporting') {
      steps {
        parallel(
          "Sleep": {
            sleep(time: 1, unit: 'MILLISECONDS')
            
          },
          "Create Jmeter HTML": {
            bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -g E:/JMeter/Resources/Webinar/Results/TaskManagerResults.csv -o Reports/', encoding: 'utf8', returnStdout: true)
            
          }
        )
      }
    }
    stage('Archive the HTML Report') {
      steps {
        parallel(
          "Archive the HTML Report": {
            zip(zipFile: 'Report.zip', archive: true, dir: 'Reports')
            
          },
          "Delete Leftovers": {
            bat(script: 'del /F /Q E:\\JMeter\\Resources\\Webinar\\Results\\TaskManagerResults.csv"', returnStatus: true, returnStdout: true)
            
          }
        )
      }
    }
  }
}