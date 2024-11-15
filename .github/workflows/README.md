# Waterdeep Github Actions Workflows

Each workflow prefixed by an underscore is intended to be a component of a larger workflow. Example: [_build-and-test.yaml](./_build-and-test.yml)

Each workflow not prefixed by an underscore is intended to be ran directly (or executed automatically). Example: 


## Workflows

A summary of the main workflows is provided below. 

### [autobuild.yml](./autobuild.yml)

Runs: Every PR update and on merges to `master`.

This workflow actually builds the project. The built artifacts are shipped to S3 for use with deploys (see next workflows). 

### [deploy-to-stg.yml](./deploy-to-stg.yml)

Runs: On a release being created. 

When executing, it does the following: 
- takes the built artifacts in S3, promotes them to a "releases/" path in the bucket 
- then creates a new application version in Beanstalk for that release
- updates the ACTIVE staging environment to deploy the new version (the workflow discovers the inactive env by checking urls)

### [deploy-to-stg-inactive.yml](./deploy-to-stg-inactive.yml)

Runs: Manually 

When executing, it does the following: 
- takes the built artifacts in S3, promotes them to a "releases/" path in the bucket 
- then creates a new application version in Beanstalk for that release
- updates the INACTIVE staging environment to deploy the new version (the workflow discovers the inactive env by checking urls)

### [deploy-to-live.yml](./deploy-to-live.yml) 

Runs: This workflow is triggered manually. 

When executing it will: 
- takes the stg artifact and "promote" it to a live bucket, under a "releases/" path
- creates a new LIVE application version in Beanstalk (in us-east-1) for that release
- updates the appropriate LIVE environment to deploy the new version 


## Other workflows

### [purge-fastly-cache.yaml](./purge-fastly-cache.yaml) 


Runs: This workflow is intended to be run manually (for now). 

This will purge the cached content for an input site url. 
It supports purging all objects, or just a portion (by providing a path).