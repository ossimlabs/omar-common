# omar-common
*This repository houses files that are used throughout many of the O2/OMAR apps and plugins.*

## Files
#### omar-common-properties.gradle
The common properties file is a  [Gradle](https://gradle.org/) file that contains the tasks needed to build an O2 application.

It is dependent upon two external Gradle plugins to aid in some of the tasks.  These plugins simplify some of the processes that are conducted during the build.

###### Plugins used:

[Gradle Docker plugin](https://github.com/bmuschko/gradle-docker-plugin)

[Gradle S3 Plugin](https://github.com/skhatri/gradle-s3-plugin)

##### Installation

You will need to modify you shell enviornment to point to the location of the .gradle file.
For example you will need to edit your .bashrc file, and add:

```
export OMAR_COMMON_PROPERTIES=$HOME/repos/rbt/omar-common/omar-common-properties.gradle
```
Make sure the above `export` location matches your local development enviornment.

##### Gradle specific tasks:
**downloadBaseImage**: Downloads the _o2-base_ or _o2-ossim_ Docker image that is used as the base image in the Docker build process.  The image is in .tgz format.

**loadBaseDockerImage**: Does a Docker load on the downloaded base image.
**createDockerfile**: Creates an app specific Dockerfile using the Docker Gradle Plugin task

**buildDockerImage**: Uses the Dockerfile from the create task above to build the app's image

**tagDockerImage**: Tags the image with proper Docker repository needed to push to our Digitalglobe Openshift

**logIn**: This is a Digitalglobe specific task needed to log in to our Openshift Docker repository

**pushDockerImage**: Pushes the newly built Docker image to the Openshift repository.

**saveDockerImage**: Saves the build Docker image to a .tgz file.

**dockerImageToS3**:  Takes the saved Docker image .tgz, and pushes it to the Digitalglobe S3 bucket

**buildAllArtifacts**: This task is used to kick off the full build process.  It also holds the clean up processes needed to remove the Docker images used for the build, created in the build, and zipped up during the build.

##### Notes:
* Document the full e2e build **buildAllArtifacts** will run then entire process of building the application .jar, adding the .jar to
* Document building only the local Docker image
