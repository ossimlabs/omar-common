# omar-common
*This repository houses files that are used throughout many of the O2/OMAR apps and plugins.*

###omar-common-properties.gradle
The common properties file is a  [Gradle](https://gradle.org/) file that contains the tasks need to build an O2 application.

To use this file you will need to clone this repo.  Next you will need to modify you shell enviornment to point to the location of the .gradle file.

It utilizes two Gradle plugins to aid in some of the tasks.  These plugins simplify some of the items that are conducted during the build process.

####Plugins used:
[Gradle Docker plugin](https://github.com/bmuschko/gradle-docker-plugin)

[Gradle S3 Plugin](https://github.com/skhatri/gradle-s3-plugin)

####Gradle specific tasks:
* downloadBaseImage
* loadBaseDockerImage
* createDockerfile
* buildDockerImage
* tagDockerImage
* logIn
* pushDockerImage
* saveDockerImage
* dockerImageToS3
* buildAllArtifacts
