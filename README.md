# gradle-fetch-poc
Proof of concept for using Gradle to fetch files from a maven repository.

# Overivew
I want to store a bunch of binary artifacts on a Maven repository somewhere, probably using Archiva.

I need a client to fetch and cache the artifacts.  I don't want to write the client or worry about details like HTTP connections or checksums.  Gradle (and other tools) already solve this problem.

So here is a small Gradle script for fetching dependenices.

# Usage
Run this script from the command line.  It doesn't have to be part of a larger project or build system.

It comes with the [Gradle wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html) which is a script and jar that prevent you from having to install Gradle yourself.

Use environment variables to point the script where you want:
  * `REPOSITORY`, `USERNAME`, and `PASSWORD` can point at Maven central, or your own repository.
  * `GROUP`, `ID`, and `VERSION` select the artifact (and its dependencies) that you want. 

For example, get an arbitrary Archiva dependency from Maven Central:
```
REPOSITORY="https://repo1.maven.org/maven2" USERNAME="" PASSWORD="" GROUP="org.apache.archiva" ID="archiva-cli" VERSION="2.2.0" ./gradlew -b fetch.gradle fetchAll
```

# Credit
Thanks to [jiraaya](https://jiraaya.wordpress.com/2014/06/05/download-non-jar-dependency-in-gradle/) for the tip about using configuration resolve().
