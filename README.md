# gradle-fetch-poc
Proof of concept for using Gradle to fetch files from a Maven repository.

# Overivew
I want to store a bunch of binary artifacts on a repository somewhere, maybe using [Archiva](https://archiva.apache.org/docs/1.3.6/quick-start.html).

I need a client to fetch and cache the artifacts locally.  I don't want to write the client from scratch because there are already nice client-side tools like Gradle which know how to talk to repositories like Archiva.

So here is a small Gradle script for fetching artifacts.

# Fetch

## Usage
You can run `fetch.gradle` from the command line.

The script comes with the [Gradle wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html) which is a script-plus-jar that prevents you from having to install Gradle yourself.  This really simplifies the command line usage!

Use environment variables to point the script where you want:
  * `REPOSITORY`, `USERNAME`, and `PASSWORD` could point at Maven central, your own repository, wherever.
  * `GROUP`, `ID`, `VERSION`, and `EXTENSION` select the single artifact that you want to fetch.

For example, get an arbitrary jar from Maven Central:
```
REPOSITORY="https://repo1.maven.org/maven2" USERNAME="" PASSWORD="" GROUP="org.apache.archiva" ID="archiva-cli" VERSION="2.2.0" EXTENSION="jar" ./gradlew -b fetch.gradle fetchIt
```

Gradle will resolve the artifact and write it to your local cache.  The `fetch.gradle` script will print out the path to the file in your local cache.  The `FETCHED` prefix should make the path easy to scrape out.

The output looks like this:
```
:fetchIt
FETCHED /Users/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-cli/2.2.0/1ac1906571d186c876077e75d394818a04f8e49f/archiva-cli-2.2.0.jar

BUILD SUCCESSFUL

Total time: 3.579 secs
```

The second time you run the command it will not have to download the artifact, but it will still report is as fetched.

## Matlab
It just so happens that I want to use this Gradle script from Matlab, so I wrote a Matlab wrapper around it called `FetchArtifact`.

The usage is similar to the command line usage:
```
filePath = FetchArtifact('https://repo1.maven.org/maven2', '', '', 'org.apache.archiva', 'archiva-cli', '2.2.0', 'jar')
```

And the Gradle output gets scraped into a Matlab variable:
```
filePath =

/Users/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-cli/2.2.0/1ac1906571d186c876077e75d394818a04f8e49f/archiva-cli-2.2.0.jar
```

# Publish
I added `publish.gradle` which you can use to push artifacts up to a repository.

Command line usage is similar to fetching.  For example, push a test artifact:
```
REPOSITORY="http://localhost:8080/repository/test-repository/" USERNAME="admin" PASSWORD="****" GROUP="test-group" ID="test-id" VERSION="42" EXTENSION="txt" FILE="test.txt" ./gradlew -b publish.gradle publish
```

Gradle will generate a default `.pom` (artifact metadata) and checksums and push them up to the repository along with the artifact itself.  The output looks like this:
```
:generatePomFileForMavenPublication
:publishMavenPublicationToMavenRepository
Upload http://localhost:8080/repository/test-repository/test-group/test-id/42/test-id-42.txt
Upload http://localhost:8080/repository/test-repository/test-group/test-id/42/test-id-42.txt.sha1
Upload http://localhost:8080/repository/test-repository/test-group/test-id/42/test-id-42.txt.md5
Upload http://localhost:8080/repository/test-repository/test-group/test-id/42/test-id-42.pom
Upload http://localhost:8080/repository/test-repository/test-group/test-id/42/test-id-42.pom.sha1
Upload http://localhost:8080/repository/test-repository/test-group/test-id/42/test-id-42.pom.md5
Could not find metadata test-group:test-id/maven-metadata.xml in remote (http://localhost:8080/repository/test-repository/)
Upload http://localhost:8080/repository/test-repository/test-group/test-id/maven-metadata.xml
Upload http://localhost:8080/repository/test-repository/test-group/test-id/maven-metadata.xml.sha1
Upload http://localhost:8080/repository/test-repository/test-group/test-id/maven-metadata.xml.md5
:publish

BUILD SUCCESSFUL

Total time: 4.058 secs
```

## Matlab

# Thanks
Thanks to [jiraaya's blog](https://jiraaya.wordpress.com/2014/06/05/download-non-jar-dependency-in-gradle/) for some tips:
  * Use the `resolve()` method of a Gradle configuration.
  * Use `@` syntax to fetch a single non-jar artifact (non-jartifact?).
