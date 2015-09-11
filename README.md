# gradle-fetch-poc
Proof of concept for using Gradle to fetch files from a Maven repository.

# Overivew
I want to store a bunch of non-jar artifacts (non-jartifacts?) on a Maven repository somewhere, maybe using [Archiva](https://archiva.apache.org/docs/1.3.6/quick-start.html).

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
REPOSITORY="https://repo1.maven.org/maven2" USERNAME="" PASSWORD="" GROUP="org.apache.archiva" ID="archiva-cli" VERSION="2.2.0" EXTENSION="jar" ./gradlew -b fetch.gradle fetchIt
```

Gradle will resolve the artifact and write it to your local cache.  The script will tell you the path to the file.  The `FETCHED` prefix should make the path easy to scrape out.

The output looks like this:
```
:fetchIt
FETCHED /Users/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-cli/2.2.0/1ac1906571d186c876077e75d394818a04f8e49f/archiva-cli-2.2.0.jar

BUILD SUCCESSFUL

Total time: 3.579 secs
```

# Matlab
It just so happens that I want to use this utility from Matlab, so I wrote a Matlab wrapper around the Gradle command.

The usage is very similar:
```
filePath = FetchArtifact('https://repo1.maven.org/maven2', '', '', 'org.apache.archiva', 'archiva-cli', '2.2.0', 'jar')
```

And the output is scraped into a Matlab variable:
```
filePath =

/Users/ben/.gradle/caches/modules-2/files-2.1/org.apache.archiva/archiva-cli/2.2.0/1ac1906571d186c876077e75d394818a04f8e49f/archiva-cli-2.2.0.jar
```

# Thanks
Thanks to [jiraaya](https://jiraaya.wordpress.com/2014/06/05/download-non-jar-dependency-in-gradle/) for some tips:
  * Use the `resolve()` method of a Gradle configuration.
  * Use `@` to fetch a single non-jar artifact.
