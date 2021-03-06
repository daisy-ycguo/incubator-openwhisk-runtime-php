<!--
#
# Licensed to the Apache Software Foundation (ASF) under one or more contributor 
# license agreements.  See the NOTICE file distributed with this work for additional 
# information regarding copyright ownership.  The ASF licenses this file to you
# under the Apache License, Version 2.0 (the # "License"); you may not use this 
# file except in compliance with the License.  You may obtain a copy of the License 
# at:
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software distributed 
# under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR 
# CONDITIONS OF ANY KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations under the License.
#
-->

# Apache OpenWhisk runtimes for PHP
[![Build Status](https://travis-ci.org/apache/incubator-openwhisk-runtime-php.svg?branch=master)](https://travis-ci.org/apache/incubator-openwhisk-runtime-php)


### Give it a try today
To use as a docker action
```
wsk action update myAction myAction.php --docker openwhisk/action-php-v7.1:1.0.0
```
This works on any deployment of Apache OpenWhisk

### To use on deployment that contains the rutime as a kind
To use as a kind action
```
wsk action update myAction myAction.php --kind php:7.1
```

### Local development
```
./gradlew core:php7.1Action:distDocker
```
This will produce the image `whisk/action-php-v7.1`

Build and Push image
```
docker login
./gradlew core:php7.1Action:distDocker -PdockerImagePrefix=$prefix-user -PdockerRegistry=docker.io
```

Deploy OpenWhisk using ansible environment that contains the kind `php:7.1`
Assuming you have OpenWhisk already deploy localy and `OPENWHISK_HOME` pointing to root directory of OpenWhisk core repository.

Set `ROOTDIR` to the root directory of this repository.

Redeploy OpenWhisk
```
cd $OPENWHISK_HOME/ansible
ANSIBLE_CMD="ansible-playbook -i ${ROOTDIR}/ansible/environments/local"
$ANSIBLE_CMD setup.yml
$ANSIBLE_CMD couchdb.yml
$ANSIBLE_CMD initdb.yml
$ANSIBLE_CMD wipe.yml
$ANSIBLE_CMD openwhisk.yml
```

Or you can use `wskdev` and create a soft link to the target ansible environment, for example:
```
ln -s ${ROOTDIR}/ansible/environments/local ${OPENWHISK_HOME}/ansible/environments/local-php
wskdev fresh -t local-php
```

To use as docker action push to your own dockerhub account
```
docker tag whisk/php7.1Action $user_prefix/action-php-v7.1
docker push $user_prefix/action-php-v7.1
```
Then create the action using your the image from dockerhub
```
wsk action update myAction myAction.php --docker $user_prefix/action-php-v7.1
```
The `$user_prefix` is usually your dockerhub user id.

### Testing
Install dependencies from the root directory on $OPENWHISK_HOME repository
```
./gradlew install
```

Using gradle for the ActionContainer tests you need to use a proxy if running on Mac, if Linux then don't use proxy options
You can pass the flags `-Dhttp.proxyHost=localhost -Dhttp.proxyPort=3128` directly in gradle command.
Or save in your `$HOME/.gradle/gradle.properties`
```
systemProp.http.proxyHost=localhost
systemProp.http.proxyPort=3128
```
Using gradle to run all tests
```
./gradlew :tests:test
```
Using gradle to run some tests
```
./gradlew :tests:test --tests *ActionContainerTests*
```
Using IntelliJ:
- Import project as gradle project.
- Make sure working directory is root of the project/repo
- Add the following Java VM properties in ScalaTests Run Configuration, easiest is to change the Defaults for all ScalaTests to use this VM properties
```
-Dhttp.proxyHost=localhost
-Dhttp.proxyPort=3128
```

# License
[Apache 2.0](LICENSE.txt)


