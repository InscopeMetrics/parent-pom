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

Set the parent to your _pom.xml_ file:

```xml
<parent>
    <groupId>com.inscopemetrics.build</groupId>
    <artifactId>parent-pom</artifactId>
    <version>VERSION</version>
</parent>
```

The Maven Central repository is included by default.

### Configure JDK Wrapper

The [JDK Wrapper](https://github.com/KoskiLabs/jdk-wrapper) ensures that the same version of the JDK is used in all 
builds. Although you are not required the JDK Wrapper, it is strongly recommended.

To specify the version of the JDK to use create a _.jdkw_ file with the following content:

```
JDKW_DIST=oracle
JDKW_VERSION=8u152
JDKW_BUILD=b16
JDKW_TOKEN=aa0333dd3019491ca4f6ddbe78cdb6d0
```

The parent pom configures the version of JDK to require, typically the latest available, but the version may
be overridden in the _properties_ section of your project's _pom.xml_ and __must__ match the version in _.jdkw_; for
example:

```xml
<jdk.version>1.8.0-152</jdk.version>
```

The enforcement is performed by Maven's [Enforcer Plugin](https://maven.apache.org/enforcer/enforcer-rules/requireJavaVersion.html). More 
details on how to install and configure JDK Wrapper can be found in that project's [README](https://github.com/KoskiLabs/jdk-wrapper/blob/master/README.md) file.

### Configure Maven Wrapper

The [Maven Wrapper](https://github.com/rimerosolutions/maven-wrapper) ensures that the same version of Maven is used in
all builds. To _configure_ Maven Wrapper in your project:

```
> mvn wrapper:wrapper
```

The parent pom configures the version of Maven to use, typically the latest available, but the version may
be overridden in the _properties_ section of your project's _pom.xml_; for example:

```xml
<maven.version>3.5.2</maven.version>
```

To _update_ Maven Wrapper in your project (e.g. after upgrading the parent pom or changing the desired version in your _pom.xml_):

```
> mvn wrapper:wrapper
```

However, as you can see bootstrapping Maven Wrapper in a new project requires a version of Maven to be installed. On a Mac, you can install Maven
with [Homebrew](http://brew.sh/):

```
> brew install maven
```

### Wrapper Script

To allow developers to build wrapped and unwrapped projects seamlessly you can use this shell script to invoke either 
__mvnw__ or __mvn__ and automatically wrap in __jdk-wrapper.sh__ as necessary. Simply copy the script, make it 
executable and ensure it is first on __PATH__. Now continue to invoke __mvn__ and it will actually run __mvnw__ and 
__jdk-wrapper.sh__ as necessary in the current project.

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
if [ -f "jdk-wrapper.sh" ]; then
  MVN_COMMAND="./jdk-wrapper.sh ${JDKW_OPTIONS} ${MVN_COMMAND}"
elif [ -f ".jdkw" ]; then
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

Usage
-----

Only execute integration tests goals during the verify lifecycle:
```
> mvn -DverifyIntegrationTestsOnly=true verify
```

Only execute Checkstyle goals during the verify lifecycle:
```
> mvn -DverifyCheckstyleOnly=true verify
```

Only execute Findbugs goals during the verify lifecycle:
```
> mvn -DverifyFindbugsOnly=true verify
```

Only execute code coverage goals during the verify lifecycle:
```
> mvn -DverifyCoverageOnly=true verify
```

Disable enforcing the Maven version:
```
> mvn -DskipEnforceMaven=true verify
```

Disable enforcing the JDK version:
```
> mvn -DskipEnforceJdk=true verify
```

Disable enforcing no snapshot versions on release (strongly __not__ recommended):
```
> mvn -DskipEnforceNoSnapshotsOnRelease=true verify
```

Building
--------

Prerequisites:
* [JDK8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) (Or Invoke with [JDK Wrapper](https://github.com/KoskiLabs/jdk-wrapper))

Building:

```
> mvn verify
```

To use the local version you must first install it locally:

```
> mvn install
```

You can determine the version of the local build from the pom file.  Using the local version is intended only for testing or development.

Releasing
---------

This project is versioned using [semantic versioning](http://semver.org/) please consult the commit history to determine
the next appropriate release version. Next, simply invoke the Maven Release plugin which will interactively query for the
version of the artifact to release:

```
> mvn release:prepare && mvn release:clean && git pull
```

The plugin will update the version and SCM tag in _pom.xml_ automatically based on your input and then commit the changes
to source control. When releasing the build system (e.g. [Travis](travis-ci.org)) must execute the _deploy_ lifecycle, 
enable the _release_ profile, and provide the _settings.xml_ file:

```
> mvn deploy -P release --settings settings.xml
```

The commands listed above assume you are using the wrapper described earlier in this file. If you are not, then substitute `mvn` with `./jdk-wrapper.sh ./mvnw`.

License
-------

Published under Apache Software License 2.0, see LICENSE

&copy; Inscope Metrics Incorporated, 2018
