pipeline {
  agent any
  stages {
    stage('A Fresh Workspace') {
      steps {
        script {
          fileOperations([folderDeleteOperation('Reports'), fileDeleteOperation(excludes: '', includes: 'Report.zip','TaskManagerResults.csv')])
        }
        
      }
    }
    stage('Run JMeter Test') {
      steps {
        bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -n -t E:/JMeter/Resources/Webinar/Scripts/TaskManager.jmx -l TaskManager.csv', encoding: 'UTF8')
        archiveArtifacts(artifacts: 'TaskManager.csv', allowEmptyArchive: true, onlyIfSuccessful: true)
      }
    }
    stage('Create Reporting') {
      steps {
        parallel(
          "Run Performance Plugin": {
            script {
              performanceReport compareBuildPrevious: true, configType: 'ART', errorFailedThreshold: 3, errorUnstableResponseTimeThreshold: '', errorUnstableThreshold: 1, failBuildIfNoResultFile: true, modeOfThreshold: true, modePerformancePerTestCase: true, modeThroughput: true, nthBuildNumber: 0, parsers: [[$class: 'JMeterCsvParser', delimiter: ',', glob: 'TaskManager.csv', pattern: 'timeStamp,elapsed,url,responseCode,responseMessage,threadName,dataType,success,failureMessage,bytes,sentBytes,grpThreads,allThreads,Latency,IdleTime,Connect', skipFirstLine: true]], relativeFailedThresholdNegative: 100, relativeFailedThresholdPositive: 100, relativeUnstableThresholdNegative: 20, relativeUnstableThresholdPositive: 20
            }
            
            
          },
          "Create Jmeter HTML": {
            bat(script: 'E:/JMeter/apache-jmeter-3.1/bin/jmeter.bat -g E:/JMeter/Resources/Webinar/Results/TaskManagerResults.csv -o Reports/', encoding: 'utf8', returnStdout: true)
            
          }
        )
      }
    }
    stage('Archive the HTML Report') {
      steps {
        zip(zipFile: 'Report.zip', archive: true, dir: 'Reports')
      }
    }
    stage('Email') {
      steps {
        emailext(subject: 'Jmeter Test has finished', body: 'Take it.', to: 'paulo.alexandre@gmail.com', attachmentsPattern: 'Report.zip')
      }
    }
  }
}