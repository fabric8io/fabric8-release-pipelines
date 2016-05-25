#!/usr/bin/groovy
try {
  node{

    def ipaasPipeline
    def ipaasUpdateDepsPrId
    def ipaasStagedProject

    def devopsPipeline
    def devopsUpdateDepsPrId
    def devopsStagedProject

    def quickstartsPipeline
    def quickstartsUpdateDepsPrId
    def quickstartsStagedProject

    def forgePipeline
    def forgeUpdateDepsPrId
    def forgeStagedProject

    def consolePipeline
    def consoleUpdateDepsPrId
    def consoleStagedProject

    stage 'Update fabric8-ipaas deps'
    ws ('ipaas'){
      git 'https://github.com/fabric8io/fabric8-ipaas.git'
      sh "git remote set-url origin git@github.com:fabric8io/fabric8-ipaas.git"
      ipaasPipeline = load 'release.groovy'
      ipaasUpdateDepsPrId = ipaasPipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

      stage 'Stage fabric8-ipaas'
      ipaasStagedProject = ipaasPipeline.stage()
    }

    stage 'Update fabric8-devops deps'
    ws ('devops'){
      git 'https://github.com/fabric8io/fabric8-devops.git'
      sh "git remote set-url origin git@github.com:fabric8io/fabric8-devops.git"
      devopsPipeline = load 'release.groovy'
      devopsUpdateDepsPrId = devopsPipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

      stage 'Stage fabric8-devops'
      devopsStagedProject = devopsPipeline.stage()
    }

    stage 'Update ipaas-quickstarts deps'
    ws ('quickstart'){
      git 'https://github.com/fabric8io/ipaas-quickstarts.git'
      sh "git remote set-url origin git@github.com:fabric8io/ipaas-quickstarts.git"
      quickstartsPipeline = load 'release.groovy'
      quickstartsUpdateDepsPrId = quickstartsPipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

      stage 'Stage ipaas-quickstarts'
      quickstartsStagedProject = quickstartsPipeline.stage()
    }

    stage 'Update fabric8-forge deps'
    ws ('forge'){
      git 'https://github.com/fabric8io/fabric8-forge.git'
      sh "git remote set-url origin git@github.com:fabric8io/fabric8-forge.git"
      forgePipeline = load 'release.groovy'
      forgeUpdateDepsPrId = forgePipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

      stage 'Stage fabric8-forge'
      forgeStagedProject = forgePipeline.stage()
    }

    stage 'Update fabric8-console deps'
    ws ('console'){
      git 'https://github.com/fabric8io/fabric8-console.git'
      sh "git remote set-url origin git@github.com:fabric8io/fabric8-console.git"
      consolePipeline = load 'release.groovy'
      consoleUpdateDepsPrId = consolePipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

      stage 'Stage fabric8-console'
      consoleStagedProject = consolePipeline.stage()
    }

    stage 'Deploy'
    consolePipeline.deploy(OPENSHIFT_URL, OPENSHIFT_DOMAIN, KUBERNETES_URL, KUBERNETES_DEFAULT_NAMESPACE, OPENSHIFT_STAGING_DOCKER_REGISTRY_URL, KUBERNETES_STAGING_DOCKER_REGISTRY_URL)

    stage 'Approve release'
    try {
      forgePipeline.approveRelease(forgeStagedProject)
    } catch (err) {

      ipaasPipeline.drop(ipaasStagedProject, ipaasUpdateDepsPrId)
      // devopsPipeline.drop(devopsStagedProject, devopsUpdateDepsPrId)
      // quickstartsPipeline.drop(quickstartsStagedProject, quickstartsUpdateDepsPrId)
      // forgePipeline.drop(forgeStagedProject, forgeUpdateDepsPrId)
      // consolePipeline.drop(consoleStagedProject, consoleUpdateDepsPrId)

      throw err
    }

    stage 'Promote fabric8-ipaas'
    ws ('ipaas'){
      ipaasPipeline.release(ipaasStagedProject)
      if (ipaasUpdateDepsPrId != null){
        ipaasPipeline.mergePullRequest(ipaasUpdateDepsPrId)
      }
    }

    stage 'Promote fabric8-devops'
    ws ('devops'){
      devopsPipeline.release(devopsStagedProject)
      if (devopsUpdateDepsPrId != null){
        devopsPipeline.mergePullRequest(devopsUpdateDepsPrId)
      }
    }

    stage 'Promote ipaas-quickstarts'
    ws ('quickstarts'){
      quickstartsPipeline.release(quickstartsStagedProject)
      if (quickstartsUpdateDepsPrId != null){
        quickstartsPipeline.mergePullRequest(quickstartsUpdateDepsPrId)
      }
    }

    stage 'Promote fabric8-forge'
    ws ('forge'){
      forgePipeline.release(forgeStagedProject)
      if (forgeUpdateDepsPrId != null){
        forgePipeline.mergePullRequest(forgeUpdateDepsPrId)
      }
    }

    stage 'Promote fabric8-console'
    ws ('console'){
      consolePipeline.release(consoleStagedProject)
      if (consoleUpdateDepsPrId != null){
        consolePipeline.mergePullRequest(consoleUpdateDepsPrId)
      }
    }
  }
} catch (err){
  hubot room: 'release', message: "Release failed ${err}"
  currentBuild.result = 'FAILURE'
}
