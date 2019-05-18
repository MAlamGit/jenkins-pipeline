pipeline {

  agent {
    node {
      label 'master'
    }
  }



  stages {
//    stage('PHPUnit Test') {
//      steps {
//       echo 'Running PHPUnit...'
//       sh '/bin/phpunit ${WORKSPACE}/src'
//     }
//   }
 stage("Create new tag") {
         when {
               expression {env.BRANCH_NAME == 'master'}
            }                     
            steps {
             sshagent (credentials: ['jenkins-ubuntu-cred'])                        
                {
                script {
                   	sh "git config --add remote.origin.fetch +refs/heads/master:refs/remotes/origin/master"
			sh "git fetch"
                        def tag = sh(returnStdout: true, script: "git tag | tail -1").trim()
                        println tag
//                        def semVerLib = load 'SemVer.groovy'
                        def version = getTagversion(tag)
                        println version
                        sh """
                            git tag -a "v${version}" \
                                -m "Generated by: ${env.JENKINS_URL}" \
                                -m "Job: ${env.JOB_NAME}" \
                                -m "Build: ${env.BUILD_NUMBER}"
                            git push --tags
                        """
                    
                }
              }
                
            }
        }
 
  }
}

def getTagversion (String oldtagVersion)
{
    oldtagVersion = oldtagVersion.substring(1)
    println oldtagVersion
    def versionParts = oldtagVersion.tokenize('.')
    println versionParts
    major = versionParts[0].toInteger()
    minor = versionParts[1].toInteger()
    timetag = versionParts[2].toString()
    this.month = timetag.substring(4,6).toInteger()


    Calendar now = Calendar.getInstance();
    currmonth = (now.get(Calendar.MONTH) +1)

    Date date = new Date()
    //println date.getTime()
    //println date.format( 'yyyy-MM-ddHH:mm:ss.S' )
    String newtimetag = date.format( 'yyyyMMdd-HHmm' ).toString()
    //println strtimetag
    //timetag= timetag.replaceAll(":","-")


    if (currmonth==month)
    {

        version =  major+ "." + minor + "." +newtimetag
    }
    else
    {
      if (month < 9)
      {

        minor = minor +1
        version = major+ "." + minor + "." +newtimetag

      }
      else
      {
        major = this.major +1
        minor = 0
        version = major+ "." + minor + "." +newtimetag
      }
    }
    return version
}

