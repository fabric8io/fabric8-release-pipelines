# fabric8 release pipelines

This is the fabric8 release pipeline project that contains a git branch for each pipeline that involves multiple projects.

Typically each project / git repo will contains it's own Jenkinsfile and is responsible for it's own release pipeline.  When multiple projects have dependencies on each other, are required to be staged, tested and released together then we will want another Jenkinsfile to orchestrate this too.  

This repo contains multiple branches, each have a Jenkinsfile which will be responsible for coordinating the higher level release of multiple projects.  When these Jenkinsfiles are run, they clone each project and use release scripts within those repos to perform its part of the release.  This allows us to chain the release of multiple projects, stage a potential release, run tests and approve the promotion so that all projects are released or dropped together.
