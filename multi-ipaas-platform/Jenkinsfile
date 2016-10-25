#!/usr/bin/groovy
try {
  node{
    hubot room: 'release', message: "${env.JOB_NAME} release started"

    ws ('ipaas'){
      git 'https://github.com/fabric8io/fabric8-ipaas.git'
      sh "git remote set-url origin git@github.com:fabric8io/fabric8-ipaas.git"
      def p = load 'release.groovy'

      stage 'Stage fabric8-ipaas'
      def prj = p.stage()

      stage 'Promote'
      p.release(prj)

      stage 'Update downstream dependencies'
      p.updateDownstreamDependencies(prj)
    }

    ws ('fabric8-platform'){
      git 'https://github.com/fabric8io/fabric8-platform.git'
      sh "git remote set-url origin git@github.com:fabric8io/fabric8-platform.git"
      def p = load 'release.groovy'

      stage 'Stage fabric8-platform'
      def prj = p.stage()

      stage 'Promote fabric8-platform'
      p.release(prj)
    }
  }
  hubot room: 'release', message: "${env.JOB_NAME} release successful"
} catch (err){
  hubot room: 'release', message: "${env.JOB_NAME} release failed ${err}"
  currentBuild.result = 'FAILURE'
}
