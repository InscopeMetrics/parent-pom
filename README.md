Parent Pom
==========

<a href="https://raw.githubusercontent.com/InscopeMetrics/parent-pom/master/LICENSE">
    <img src="https://img.shields.io/hexpm/l/plug.svg"
         alt="License: Apache 2">
</a>
<a href="https://travis-ci.org/InscopeMetrics/parent-pom/">
    <img src="https://travis-ci.org/InscopeMetrics/parent-pom.png?branch=master"
         alt="Travis Build">
</a>
<a href="http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.inscopemetrics.build%22%20a%3A%22parent-pom%22">
    <img src="https://img.shields.io/maven-central/v/com.inscopemetrics.build/parent-pom.svg"
         alt="Maven Artifact">
</a>

A parent pom for Inscope Metrics projects.


Leveraging the Parent Pom
-------------------------

### Add Dependency

Determine the latest version of the parent pom in [Maven Central](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.inscopemetrics.build%22%20a%3A%22parent-pom%22).

#### Maven

Set the parent to your pom:

```xml
<parent>
    <groupId>com.inscopemetrics.build</groupId>
    <artifactId>parent-pom</artifactId>
    <version>VERSION</version>
</parent>
```

The Maven Central repository is included by default.

### Maven and JDK Wrappers

The parent pom enables the use of [Maven Wrapper](https://github.com/rimerosolutions/maven-wrapper) in child projects and it also uses Maven Wrapper itself. In order to ensure consistent builds you should invoke __mvnw__ instead of __mvn__ to build the project locally and from continuous integration.

Furthermore, this project uses [JDK Wrapper](https://github.com/vjkoskela/jdk-wrapper). Invocations of __mvnw__ should be wrapped by invoking __jdk-wrapper.sh__. The use of JDK Wrapper in child projects is strongly encouraged.

To allow developers to build wrapped and unwrapped projects seamlessly you can use this shell script to invoke either __mvnw__ or __mvn__ and automatically wrap in __jdk-wrapper.sh__ as necessary. Simply copy the script, make it executable and ensure it is first on __PATH__. Now continue to invoke __mvn__ and it will actually run __mvnw__ and __jdk-wrapper.sh__ as necessary in the current project.

```bash
#!/bin/bash

JDKW_OPTIONS=""

# Look for maven wrapper
MVN_COMMAND=""
if [ -f "./mvnw" ]; then
  echo "Maven wrapper found; invoking mvnw"
  MVN_COMMAND="./mvnw"
else
  echo "Maven wrapper not found; invoking mvn"
  MVN_COMMAND="/usr/local/bin/mvn"
fi

# Look for jdk wrapper
if [ -f ".jdkw" ]; then
  JDKW_REMOTE="https://raw.githubusercontent.com/vjkoskela/jdk-wrapper/master/jdk-wrapper.sh"
  JDKW_LOCAL="${HOME}/.jdk/jdk-wrapper.sh"
  mkdir -p "$(dirname "${JDKW_LOCAL}")"
  if [ -f "${JDKW_LOCAL}" ]; then
    curl "${JDKW_REMOTE}" -z "${JDKW_LOCAL}" -o "${JDKW_LOCAL}" --silent --location
  else
    curl "${JDKW_REMOTE}" -o "${JDKW_LOCAL}" --silent --location
  fi
  chmod +x "${JDKW_LOCAL}"
  MVN_COMMAND="${JDKW_LOCAL} ${JDKW_OPTIONS} ${MVN_COMMAND}"
fi

# Execute
eval "${MVN_COMMAND}" "$@"
exit $?
```

Please note that your environment should have JAVA_HOME set correctly.

Building
--------

Prerequisites:
* [JDK8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (Or Invoke with JDKW)

Building:

    parent-pom> ./mvnw verify

To use the local version you must first install it locally:

    parent-pom> ./mvnw install

You can determine the version of the local build from the pom file.  Using the local version is intended only for testing or development.

License
-------

Published under Apache Software License 2.0, see LICENSE

&copy; Inscope Metrics Incorporated, 2016
