#!/usr/bin/groovy
node{

  def devopsPipeline
  def forgePipeline
  def stagedDevopsProject
  def stagedForgeProject
  def pipelineForgePrId


  git 'https://github.com/fabric8io/fabric8-devops.git'
  sh "git remote set-url origin git@github.com:fabric8io/fabric8-devops.git"
  devopsPipeline = load 'release.groovy'
  stage 'Updating dependencies'
  def devopsPipelinePrId = devopsPipeline.updateDependencies('http://central.maven.org/maven2/')

  stage 'Stage fabric8-devops'
  stagedDevopsProject = devopsPipeline.stage()

  stage 'Stage fabric8-forge'
  git 'https://github.com/fabric8io/fabric8-forge.git'
  sh "git remote set-url origin git@github.com:fabric8io/fabric8-forge.git"
  forgePipeline = load 'release.groovy'
  pipelineForgePrId = forgePipeline.updateDependencies('https://oss.sonatype.org/content/repositories/staging/')
  stagedForgeProject = forgePipeline.stage()

  //stage 'Deploy pipeline-test-project'
  //forgePipeline.deploy(stagedForgeProject)

  stage 'Approve release'
  forgePipeline.approveRelease(stagedForgeProject)

  stage 'Promote release'
  devopsPipeline.release(stagedDevopsProject)
  forgePipeline.release(stagedForgeProject)
  if (devopsPipelinePrId != null){
    devopsPipeline.mergePullRequest(devopsPipelinePrId)
  }
  if (pipelineForgePrId != null){
    forgePipeline.mergePullRequest(pipelineForgePrId)
  }
}
