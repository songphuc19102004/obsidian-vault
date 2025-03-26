We start by creating a file called ==**.gitlab-ci.yml**== (this is a must, or we need to change it in settings)

This file will hold all the ci/cd configuration

  

A pipeline is what you’re defining in the `.gitlab-ci.yml` file,  
and is what happens when the contents of the file are run on a runner.  

Pipelines are made up of jobs and stages:

- Stages define the ==**order of execution**==. Typical stages might be `build`, `test`, and `deploy`.
- Jobs specify the ==**tasks to be performed in each stage**==. For example, a job can compile or test code.

Pipelines can be ==**triggered by various events**==, like commits or merges, or can be on schedule.  
In your pipeline, you can integrate with a wide range of tools and platforms.