# TagLib

[![Build Status](../../actions/workflows/build.yml/badge.svg)](../../actions)

### TagLib Audio Metadata Library

https://taglib.org/

TagLib is a library for reading and editing the metadata of several
popular audio formats. Currently, it supports both ID3v1 and ID3v2
for MP3 files, [Ogg Vorbis][] comments and ID3 tags
in [FLAC][], MPC, Speex, WavPack, TrueAudio, WAV, AIFF, MP4, APE, ASF,
DSF, DFF and AAC files.

TagLib is distributed under the [GNU Lesser General Public License][]
(LGPL) and [Mozilla Public License][] (MPL). Essentially that means that
it may be used in proprietary applications, but if changes are made to
TagLib they must be contributed back to the project. Please review the
licenses if you are considering using TagLib in your project.

  [Ogg Vorbis]: https://xiph.org/vorbis/
  [FLAC]: https://xiph.org/flac/
  [GNU Lesser General Public License]: https://www.gnu.org/licenses/lgpl.html
  [Mozilla Public License]: https://www.mozilla.org/MPL/MPL-1.1.html

## Jenkins Take-Home Task
The Jenkins take-home task is completed in my repo https://github.com/johncave/taglib 

### 1. (Optional) Install Jenkins

If you don’t want to use an exiting Jenkins instance, a docker-compose.yaml file is provided in the repo. Check out the repo and run `docker-compose up`, and connect to http://localhost:8080/jenkins/, copy the password in from the Docker Compose logs, and install the recommended plugins. Skip the remaining steps to continue using the default admin user.

### 2. Configure Jenkins

The “Docker” and “Docker Pipelines” plugins must be installed. In the Jenkins UI, navigate to `Manage Jenkins > Plugins > Available Plugins`, and search for “Docker”. Tick the two plugins and click install. 

### Run The Pipeline

Once there is a Jenkins instance with Docker support available, running the pipeline is pretty simple. 
1.	On the Jenkins Homepage / Dashboard, click “New Item”.
2.	Select item type “Pipeline” and give it a name like “Build Taglib”
3.	Press “OK” to go to the next screen.
4.	Scroll down to the “Pipeline” section, and change “Pipeline Script” to “Pipeline Script from SCM”.
5.	Set SCM to “Git”
6.	Set Repository URL to “https://github.com/johncave/taglib.git”
7.	Press “Save”
8.	Press “Build Now”
9.	The best way to watch the progress of the Pipeline is click the dropdown next to “Run 1” and press “Pipeline Console”
10.	Once the pipeline has completed, click to go back to the pipeline’s main screen. 
11.	The build artifacts will be listed on the main page. 

### Pipeline Overview

The pipeline instructs Jenkins to run the pipeline in a Docker container using the image “ubuntu:noble”, then consists of five main stages:
1.	**Install pre-requisite tools** - This stage installs all the tools needed to run the build, as the Ubuntu container image doesn’t come with any. 
2.	**Checkout** - The stage cleans up the workspace folder so a clean build can be performed, then checks out the Github repo.
3.	**Configure** - The stage configures some options for where to build the library and how it should be built. This is based on the default options used in the Github repo to build Taglib public releases.
4.	**Build** - This stage does the actual building of the library. It’s the primary stage and takes by far the longest.
5.	**Test** - This stage runs the included tests on the built files.
6.	**Post** - The artifacts are packaged up and fingerprinted, then they are stored in Jenkins for easy download by the developer.

## Assumptions and Limitations

### Assumptions

The pipeline makes a few assumptions.
1. The Jenkins instance is configured with Docker installed on the host, or the provided Docker Compose is used as an alternative.
1. The Docker and Docker Pipelines plugins are installed - this is the easiest way to get a clean Ubuntu environment.
1. Otherwise clean Jenkins environment. There could be some conflict with other plugins in a larger Jenkins environment.

### Limitations

The limitations of this approach are as follows:
1. There is an open Jenkins issue about Git checkout being broken in Docker containers. If I was building this pipeline for real I'd want to find a better way to do this.
1. Using Jenkins Artifact handling isn't the most accessible place for developers to find the code. In the real world I would want to upload the package to something like Gitlab releases. 
1. The library is not packaged ideally for including in a larger package. Most likely the .so files would be bundled into a larger build process.