#!/usr/bin/groovy
node{

  def quickstartsPipeline
  def forgePipeline
  def stagedQuickstartsProject
  def stagedForgeProject
  def pipelineForgePrId

  stage 'Update ipaas-quickstarts deps'
  ws {
    git 'https://github.com/fabric8io/ipaas-quickstarts.git'
    sh "git remote set-url origin git@github.com:fabric8io/ipaas-quickstarts.git"
    quickstartsPipeline = load 'release.groovy'
    def quickstartsPipelinePrId = quickstartsPipeline.updateDependencies('http://central.maven.org/maven2/')

    stage 'Stage ipaas-quickstarts'
    stagedQuickstartsProject = quickstartsPipeline.stage()
  }

  stage 'Update fabric8-forge deps'
  ws {
    git 'https://github.com/fabric8io/fabric8-forge.git'
    sh "git remote set-url origin git@github.com:fabric8io/fabric8-forge.git"
    forgePipeline = load 'release.groovy'
    pipelineForgePrId = forgePipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')

    stage 'Stage fabric8-forge'
    stagedForgeProject = forgePipeline.stage()
  }

  //stage 'Deploy pipeline-test-project'
  //forgePipeline.deploy(stagedForgeProject)

  stage 'Approve release'
  forgePipeline.approveRelease(stagedForgeProject)

  stage 'Promote ipaas-quickstarts'
  ws {
    quickstartsPipeline.release(stagedQuickstartsProject)
    if (quickstartsPipelinePrId != null){
      quickstartsPipeline.mergePullRequest(quickstartsPipelinePrId)
    }
  }

  stage 'Promote fabric8-forge'
  ws{
    forgePipeline.release(stagedForgeProject)
    if (pipelineForgePrId != null){
      forgePipeline.mergePullRequest(pipelineForgePrId)
    }
  }
}
