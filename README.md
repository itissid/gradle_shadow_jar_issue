# gradle_shadow_jar_issue
## Issue
Certain classes are not included in the shadow jar if mavenLocal is not in its current state. No warning is emmitted and gradle
continues on.
My understanding is that if gradle cannot find the artifact in a repo it should try finding it in other repos. In the project where I 
factored this out of there were class not found exceptions 

I have read material around a ticket that talks about a similar issue:
https://issues.gradle.org/browse/GRADLE-2709

Environment: 
```
Gradle 2.12, Ubuntu JDK 
sidharth_gupta:foo$ ./gradlew --version

------------------------------------------------------------
Gradle 2.12
------------------------------------------------------------

Build time:   2016-03-14 08:32:03 UTC
Build number: none
Revision:     b29fbb64ad6b068cb3f05f7e40dc670472129bc0

Groovy:       2.4.4
Ant:          Apache Ant(TM) version 1.9.3 compiled on December 23 2013
JVM:          1.7.0_95 (Oracle Corporation 24.95-b01)
OS:           Linux 4.2.0-34-generic i386
```

### Repro
1. Delete the zookeeper jar from .m2. Let the pom remiain.
2. Running shadowJar with mavenLocal does not package many classes for zookeeper.
The command to shadow with and without mavenLocal is:
`./gradlew --info --debug shadowJar > shadowJar_without_maven_local`
`./gradlew --info --debug shadowJar -PdontUseMavenLocal  > shadowJar_without_maven_local`
The commit in the repo contains the debug outout.

### Note that 
- Zookeeper is a valid dependency of this project. See:
`./gradlew  dependencyInsight --dependency zookeeper `
- I pulled zookeeper classes from the uber jar using following command to confirm: 
`jar tvf build/libs/foo-all.jar |grep zookeeper`
  - I logged the classes for before and after excluding mavenLocal in the enclosed files in the repo:
  `zookeeper_deps_with_maven_local`, `zookeeper_deps_without_maven_local`
