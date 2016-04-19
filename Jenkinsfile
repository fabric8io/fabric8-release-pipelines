#!/usr/bin/groovy
node{

  def fabric8Pipeline
  def fabric8UpdateDepsPrId
  def fabric8StagedProject

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


  stage 'Update fabric8 deps'
  ws ('fabric8'){
    git 'https://github.com/fabric8io/fabric8.git'
    sh "git remote set-url origin git@github.com:fabric8io/fabric8.git"
    fabric8Pipeline = load 'release.groovy'
    fabric8UpdateDepsPrId = fabric8Pipeline.updateDependencies('http://central.maven.org/maven2/')

    stage 'Stage fabric8'
    fabric8StagedProject = fabric8Pipeline.stage()
  }

  stage 'Update fabric8-ipaas deps'
  ws ('ipaas'){
    git 'https://github.com/fabric8io/fabric8-ipaas.git'
    sh "git remote set-url origin git@github.com:fabric8io/fabric8-ipaas.git"
    ipaasPipeline = load 'release.groovy'
    ipaasUpdateDepsPrId = ipaasPipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

    stage 'Stage fabric8-ipaas'
    ipaasStagedProject = ipaasPipeline.stage()
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
  //stage 'Deploy pipeline-test-project'
  //forgePipeline.deploy(stagedForgeProject)

  stage 'Approve release'
  forgePipeline.approveRelease(forgeStagedProject)

  stage 'Promote fabric8'
  ws ('fabric8'){
    fabric8Pipeline.release(fabric8StagedProject)
    if (fabric8UpdateDepsPrId != null){
      fabric8Pipeline.mergePullRequest(fabric8UpdateDepsPrId)
    }
  }

  stage 'Promote fabric8-ipaas'
  ws ('ipaas'){
    ipaasPipeline.release(ipaasStagedProject)
    if (ipaasUpdateDepsPrId != null){
      ipaasPipeline.mergePullRequest(ipaasUpdateDepsPrId)
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
