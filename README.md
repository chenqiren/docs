# Android Studio & Gradle configuration

To make the most of Android Studio with Gradle you must ensure it is properly configured otherwise you will experience freezes or slowness.
Start by reading [Configure IDE section on INSTALL.md](../INSTALL.md) and setting generous vmoptions.

### Avoid building on gradle sync

Android Studio syncs its project structure from gradle on a step called gradle sync.
However, this can slow you down since by default performs a partial build and indexing pass after a sync.
To address this you can disable build under Gradle Preferences (see below).

### Reusable Gradle Daemon

Android Studio and the terminal can reuse a single gradle daemon for better performance, however to do so the following must be warrantied:

- gradle version must match, you can warranty this by always using the gradle wrapper (`./gradlew`) on the IDE and CLI (see Gradle Preferences).
- JDK/JRE used to start the gradle daemon is the same, you can warranty this by using the same JDK on both ends (see SDK Location).
- Java opts are the same, this is usually the same unless you configured it so on the IDE or through `JAVA_TOOL_OPTIONS`.

Here is how you can debug whether or not you are a reusing a single gradle daemon correctly:
- From CLI, execute: `./gradlew -stop`
- From IDE, perform a gradle sync or gradle task through Gradle tool window (top right or double tap shift key and type Gradle).
- Wait for IDE to finish sync or gradle task.
- From CLI execute:
    - `./gradlew -status` observe single `IDLE` daemon.
    - `./gradlew tasks --info` you can interrupt as soon as configuration starts.
    - `./gradlew -status` continue observing single `IDLE` daemon.

If you see multiple daemons listed there it means you are not reusing a gradle daemon.

To debug this you can use "Show Gradle Daemons" on Android Studio or read the first lines of the `--info` step above it provides useful information that you can use to warranty reusability.

Here is an example of the log in which we fail to reuse gradle daemon because `-Dfile.encoding` value was different on CLI vs IDE:
```
Found daemon DaemonInfo{pid=2223, address=[00704d55-339d-4db3-a8da-85bfc50e0f6c port:64203, addresses:[/0:0:0:0:0:0:0:1, /127.0.0.1]], state=Idle, lastBusy=1521256767100, context=DefaultDaemonContext[uid=09ddaa13-9a74-40bb-9377-a2d9fbbc06
2f,javaHome=/Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home,daemonRegistryDir=/Users/evelio.tarazonacaceres/.gradle/daemon,pid=2223,idleTimeout=10800000,daemonOpts=-XX:MaxHeapSize=9g,-Xmx9g,-Dfile.encoding=UTF-8,-Duser.co
untry=US,-Duser.language=en,-Duser.variant]} however its context does not match the desired criteria.
At least one daemon option is different.
Wanted: DefaultDaemonContext[uid=null,javaHome=/Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home,daemonRegistryDir=/Users/evelio.tarazonacaceres/.gradle/daemon,pid=2411,idleTimeout=null,daemonOpts=-XX:MaxHeapSize=9g,-Xmx9g,
-Dfile.encoding=UTF8,-Duser.country=US,-Duser.language=en,-Duser.variant]
Actual: DefaultDaemonContext[uid=09ddaa13-9a74-40bb-9377-a2d9fbbc062f,javaHome=/Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home,daemonRegistryDir=/Users/evelio.tarazonacaceres/.gradle/daemon,pid=2223,idleTimeout=10800000,d
aemonOpts=-XX:MaxHeapSize=9g,-Xmx9g,-Dfile.encoding=UTF-8,-Duser.country=US,-Duser.language=en,-Duser.variant]

  Looking for a different daemon...
Removing 0 daemon stop events from registry
Previous Daemon (95099) stopped at Fri Mar 16 19:36:21 PDT 2018 stop command received
Previous Daemon (94543) stopped at Fri Mar 16 19:36:21 PDT 2018 stop command received
Previous Daemon (1592) stopped at Fri Mar 16 20:19:13 PDT 2018 stop command received
Previous Daemon (827) stopped at Fri Mar 16 20:19:13 PDT 2018 stop command received
Starting a Gradle Daemon, 1 incompatible and 4 stopped Daemons could not be reused, use --status for details
Starting process 'Gradle build daemon'. Working directory: /Users/evelio.tarazonacaceres/.gradle/daemon/4.5-rc-1 Command: /Library/Java/JavaVirtualMachines/jdk1.8.0_161.jdk/Contents/Home/bin/java -XX:MaxHeapSize=9g -Xmx9g -Dfile.encoding=
UTF8 -Duser.country=US -Duser.language=en -Duser.variant -cp /Users/evelio.tarazonacaceres/.gradle/wrapper/dists/gradle-4.5-rc-1-all/asl6g5tggd3fi1d9gru8ey0z6/gradle-4.5-rc-1/lib/gradle-launcher-4.5.jar org.gradle.launcher.daemon.bootstra
p.GradleDaemon 4.5-rc-1
Successfully started process 'Gradle build daemon'
```

### Gradle Preferences

1. Make sure you are using the gradle wrapper
![Use gradle wrapper.](configure-gradle-android-studio-images/gradle-preferences.png)
1. Make sure embedded Maven repo is disabled
![Disable embedded Maven repo.](configure-gradle-android-studio-images/gradle-preferences-as.png)
1. Make sure you disable build on gradle sync under "Experimental"
![Disable build on gradle sync.](configure-gradle-android-studio-images/gradle-preferences-experimental.png)

### SDK Location

After importing the project, please make sure that your paths match the ones configured in your environment variables:
1. Navigate to Project Structure by double tapping shift key or ⇧⌘A and typing "Project Structure" and then enter.
1. On the left option select SDK Location.
1. Make sure configuration looks like this:
![Project Structure must match CLI env var paths.](configure-gradle-android-studio-images/project-structure-sdk-location.png)
