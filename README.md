# gradle-fetch-poc
Proof of concept for using Gradle to fetch files from a Maven repository.

# Overivew
I want to store a bunch of binary artifacts on a Maven repository somewhere, probably using Archiva.

I need a client to fetch and cache the artifacts.  I don't want to write the client or worry about details like HTTP connections or checksums.  Gradle (and other tools) already solve these problems.

So here is a small Gradle script for fetching artifacts.

# Usage
Run this script from the command line.  It doesn't have to be part of a larger project or build system.

It comes with the [Gradle wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html) which is a script and jar that prevent you from having to install Gradle yourself.

Use environment variables to point the script where you want:
  * `REPOSITORY`, `USERNAME`, and `PASSWORD` can point at Maven central, your own repository, wherever.
  * `GROUP`, `ID`, `VERSION`, and `EXTENSION` select the single artifact that you want. 

For example, get an arbitrary Archiva jar from Maven Central:
```
REPOSITORY="https://repo1.maven.org/maven2" USERNAME="" PASSWORD="" GROUP="org.apache.archiva" ID="archiva-cli" VERSION="2.2.0" EXTENSION="jar" ./gradlew -b fetch.gradle fetchAll
```

Gradle will resolve the artifact and write it to your local cache.  The script will tell you the path to the file.  The `FETCHED` prefix should make the path easy to scrape out.

The output looks like this:
```
:fetchAll
FETCHED /home/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-cli/2.2.0/1ac1906571d186c876077e75d394818a04f8e49f/archiva-cli-2.2.0.jar
FETCHED /home/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-repository-scanner/2.2.0/1f5a5a4c24607a6dd3908210df02897e17f5b52f/archiva-repository-scanner-2.2.0.jar
FETCHED /home/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-repository-admin-api/2.2.0/1dba8c50cc745e93f3e43a22b31adb4021917337/archiva-repository-admin-api-2.2.0.jar

...

FETCHED /home/ben/.gradle/caches/modules-2/files-2.1/org.eclipse.sisu/org.eclipse.sisu.inject/0.1.0/26b8d748407f4eac4c5cee33836afd8d5c5286d5/org.eclipse.sisu.inject-0.1.0.jar
FETCHED /home/ben/.gradle/caches/modules-2/files-2.1/xml-apis/xml-apis/1.3.02/dc7315e359b5e43f20131414d60b5c307aace975/xml-apis-1.3.02.jar

BUILD SUCCESSFUL

Total time: 3.993 secs
```

# Thanks
Thanks to [jiraaya](https://jiraaya.wordpress.com/2014/06/05/download-non-jar-dependency-in-gradle/) for tips:
  * Use the `resolve()` method of a Gradle configuration.
  * Use `@` to fetch a single non-jar artifact (non-jartifact?).
