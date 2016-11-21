#!/usr/bin/groovy
try {
  node{
    hubot room: 'release', message: "${env.JOB_NAME} release started"

    if ("true".equals(FABRIC8)) {
      releaseit('fabric8')
    }

    if ("true".equals(FABRIC8_MAVEN_PLUGIN)) {
      releaseit('fabric8-maven-plugin')
    }

    if ("true".equals(FABRIC8_DEVOPS)) {
      releaseit('fabric8-devops')
    }

    if ("true".equals(FABRIC8_IPAAS)) {
      releaseit('fabric8-ipaas')
    }

    if ("true".equals(IPAAS_QUICKSTARTS)) {
      releaseit('ipaas-quickstarts')
    }

    if ("true".equals(FABRIC8_FORGE)) {
      releaseit('fabric8-forge')
    }

    releaseit('fabric8-platform')

  }
  hubot room: 'release', message: "${env.JOB_NAME} release successful"
} catch (err){
  hubot room: 'release', message: "${env.JOB_NAME} release failed ${err}"
  currentBuild.result = 'FAILURE'
}

def releaseit (name){
  ws (name){
    hubot room: 'release', message: "Starting ${name}"

    git "https://github.com/fabric8io/${name}.git"
    sh "git remote set-url origin git@github.com:fabric8io/${name}.git"
    def p = load 'release.groovy'

    stage "Stage ${name}"
    def prj = p.stage()

    if (name == 'fabric8-platform'){
      p.approve(prj)
    }

    stage 'Promote'
    p.release(prj)

    if (name != 'fabric8-platform'){
      stage 'Update downstream dependencies'
      p.updateDownstreamDependencies(prj)
    }
  }
}
