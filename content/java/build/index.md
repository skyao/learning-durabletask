---
title: "build"
linkTitle: "build"
weight: 10
date: 2024-01-19
description: >
  构建 durabletask-java 项目
---

## 构建

## 准备工作

### clone 仓库

```bash
mkdir durabletask
cd durabletask

git clone --recurse-submodules git@github.com:microsoft/durabletask-java.git
cd durabletask-java
```

如果没有使用 `--recurse-submodules` 参数就执行了 clone 操作，则需要：

```bash
git submodule init
git submodule update

# 或者直接
git submodule update --init --recursive
```

这个时候 `submodules/durabletask-protobuf` 目录下就会出现相关的文件。

- submodules/durabletask-protobuf/protos/orchestrator_service.proto

备注：本地开发时考虑使用 linux soft ln 来避免频繁提交 proto 修改。

### 安装 jdk 

使用 sdkman 安装 jdk11 或者 jdk17 都可以。

记录 JAVA_HOME，备用。

```bash
$ echo $JAVA_HOME
/home/sky/.sdkman/candidates/java/current
```

## 构建

### 构建java项目

最重要的第一件事情：

打开 `client/build.gradle` 文件，找到 

```bash
// When build on local, you need to set this value to your local jdk11 directory.
// Java11 is used to compile and run all the tests.
// Example for Windows:  C:/Program Files/Java/openjdk-11.0.12_7/
def PATH_TO_TEST_JAVA_RUNTIME = "$System.env.JDK_11"
```

将 PATH_TO_TEST_JAVA_RUNTIME 的值修改为上面真实的 JAVA_HOME 的值，如：

```bash
def PATH_TO_TEST_JAVA_RUNTIME = "/home/sky/.sdkman/candidates/java/current"
```

然后再执行构建命令：

```bash
./gradlew clean build
```

从 proto 文件生成的 java 代码将会存放在 `client/build/generated` 

### 发布到 maven 本地

修改 `client/build.gradle`文件：

```bash
# 修改版本为 snapshot
# version = '1.5.0'
version = '1.6.0-SNAPSHOT'

# 删除这一段
signing {
    sign publishing.publications.mavenJava
}
```

修改 `azurefunctions/build.gradle` 文件，删除这一段：

```
publications {
        mavenJava(MavenPublication) {
            from components.java
            artifactId = archivesBaseName
            pom {
                name = 'Azure Durable Functions SDK for Java'
                description = 'This package contains classes, interfaces, and annotations for developing with Azure Durable Functions in Java.'
                url = "https://github.com/microsoft/durabletask-java/tree/main/azurefunctions"
                licenses {
                    license {
                        name = "MIT License"
                        url = "https://opensource.org/licenses/MIT"
                        distribution = "repo"
                    }
                }
                developers {
                    developer {
                        id = "Microsoft"
                        name = "Microsoft Corporation"
                    }
                }
                scm {
                    connection = "scm:git:https://github.com/microsoft/durabletask-java"
                    developerConnection = "scm:git:git@github.com:microsoft/durabletask-java"
                    url = "https://github.com/microsoft/durabletask-java/tree/main/azurefunctions"
                }
                // use below script to include compile-only dependencies when generated pom file.
                // This is pain point when we onboard API docs as the missing compile-only dependencies crash the
                // API doc's team onboarding pipeline.
                withXml {
                    project.configurations.compileOnly.allDependencies.each { dependency ->
                        asNode().dependencies[0].appendNode("dependency").with {
                            it.appendNode("groupId", dependency.group)
                            it.appendNode("artifactId", dependency.name)
                            it.appendNode("version", dependency.version)
                            it.appendNode("scope", "provided")
                        }
                    }
                }
            }
        }
    }
```

还有:

```bash
signing {
    sign publishing.publications.mavenJava
}
```

然后就可以构建并发布了:

```bash
./gradlew clean build installDist publishToMavenLocal --stacktrace
```

> 参考: https://docs.gradle.org/current/userguide/publishing_maven.html

打包发布的文件在这里：

```bash
$ tree ~/.m2/repository/com/microsoft/
/home/sky/.m2/repository/com/microsoft/
└── durabletask-client
    ├── 1.6.0-SNAPSHOT
    │   ├── durabletask-client-1.6.0-SNAPSHOT.jar
    │   ├── durabletask-client-1.6.0-SNAPSHOT-javadoc.jar
    │   ├── durabletask-client-1.6.0-SNAPSHOT.module
    │   ├── durabletask-client-1.6.0-SNAPSHOT.pom
    │   ├── durabletask-client-1.6.0-SNAPSHOT-sources.jar
    │   └── maven-metadata-local.xml
    └── maven-metadata-local.xml

3 directories, 7 files
```

### 复制文件到另一台机器

在另外一个机器上（开发dapr，因为 protoc 版本不同所以分开）

```bash
mkdir -p ~/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT
cd ~/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT
rm -f * -y

scp sky@192.168.0.102:/home/sky/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT/durabletask-client-1.6.0-SNAPSHOT.jar .
scp sky@192.168.0.102:/home/sky/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT/durabletask-client-1.6.0-SNAPSHOT-javadoc.jar .
scp sky@192.168.0.102:/home/sky/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT/durabletask-client-1.6.0-SNAPSHOT.module .
scp sky@192.168.0.102:/home/sky/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT/durabletask-client-1.6.0-SNAPSHOT.pom .
scp sky@192.168.0.102:/home/sky/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT/durabletask-client-1.6.0-SNAPSHOT-sources.jar .
scp sky@192.168.0.102:/home/sky/.m2/repository/com/microsoft/durabletask-client/1.6.0-SNAPSHOT/maven-metadata-local.xml .
```

## 问题备忘

遇到这样一个 gradle 报错：


```bash
> Task :client:compileTestJava FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':client:compileTestJava'.
> A problem occurred starting process 'command 'null/bin/javac''

* Try:
> Run with --info or --debug option to get more log output.
> Run with --scan to get full insights.

* Exception is:
org.gradle.api.tasks.TaskExecutionException: Execution failed for task ':client:compileTestJava'.
        at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.lambda$executeIfValid$1(ExecuteActionsTaskExecuter.java:147)
        at org.gradle.internal.Try$Failure.ifSuccessfulOrElse(Try.java:282)
        at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.executeIfValid(ExecuteActionsTaskExecuter.java:145)
        at org.gradle.api.internal.tasks.execution.ExecuteActionsTaskExecuter.execute(ExecuteActionsTaskExecuter.java:133)
        at org.gradle.api.internal.tasks.execution.CleanupStaleOutputsExecuter.execute(CleanupStaleOutputsExecuter.java:77)
        at org.gradle.api.internal.tasks.execution.FinalizePropertiesTaskExecuter.execute(FinalizePropertiesTaskExecuter.java:46)
        at org.gradle.api.internal.tasks.execution.ResolveTaskExecutionModeExecuter.execute(ResolveTaskExecutionModeExecuter.java:51)
        at org.gradle.api.internal.tasks.execution.SkipTaskWithNoActionsExecuter.execute(SkipTaskWithNoActionsExecuter.java:57)
        at org.gradle.api.internal.tasks.execution.SkipOnlyIfTaskExecuter.execute(SkipOnlyIfTaskExecuter.java:56)
        at org.gradle.api.internal.tasks.execution.CatchExceptionTaskExecuter.execute(CatchExceptionTaskExecuter.java:36)
        at org.gradle.api.internal.tasks.execution.EventFiringTaskExecuter$1.executeTask(EventFiringTaskExecuter.java:77)
        at org.gradle.api.internal.tasks.execution.EventFiringTaskExecuter$1.call(EventFiringTaskExecuter.java:55)
        at org.gradle.api.internal.tasks.execution.EventFiringTaskExecuter$1.call(EventFiringTaskExecuter.java:52)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$CallableBuildOperationWorker.execute(DefaultBuildOperationRunner.java:204)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$CallableBuildOperationWorker.execute(DefaultBuildOperationRunner.java:199)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$2.execute(DefaultBuildOperationRunner.java:66)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$2.execute(DefaultBuildOperationRunner.java:59)
        at org.gradle.internal.operations.DefaultBuildOperationRunner.execute(DefaultBuildOperationRunner.java:157)
        at org.gradle.internal.operations.DefaultBuildOperationRunner.execute(DefaultBuildOperationRunner.java:59)
        at org.gradle.internal.operations.DefaultBuildOperationRunner.call(DefaultBuildOperationRunner.java:53)
        at org.gradle.internal.operations.DefaultBuildOperationExecutor.call(DefaultBuildOperationExecutor.java:73)
        at org.gradle.api.internal.tasks.execution.EventFiringTaskExecuter.execute(EventFiringTaskExecuter.java:52)
        at org.gradle.execution.plan.LocalTaskNodeExecutor.execute(LocalTaskNodeExecutor.java:74)
        at org.gradle.execution.taskgraph.DefaultTaskExecutionGraph$InvokeNodeExecutorsAction.execute(DefaultTaskExecutionGraph.java:333)
        at org.gradle.execution.taskgraph.DefaultTaskExecutionGraph$InvokeNodeExecutorsAction.execute(DefaultTaskExecutionGraph.java:320)
        at org.gradle.execution.taskgraph.DefaultTaskExecutionGraph$BuildOperationAwareExecutionAction.execute(DefaultTaskExecutionGraph.java:313)
        at org.gradle.execution.taskgraph.DefaultTaskExecutionGraph$BuildOperationAwareExecutionAction.execute(DefaultTaskExecutionGraph.java:299)
        at org.gradle.execution.plan.DefaultPlanExecutor$ExecutorWorker.lambda$run$0(DefaultPlanExecutor.java:143)
        at org.gradle.execution.plan.DefaultPlanExecutor$ExecutorWorker.execute(DefaultPlanExecutor.java:227)
        at org.gradle.execution.plan.DefaultPlanExecutor$ExecutorWorker.executeNextNode(DefaultPlanExecutor.java:218)
        at org.gradle.execution.plan.DefaultPlanExecutor$ExecutorWorker.run(DefaultPlanExecutor.java:140)
        at org.gradle.internal.concurrent.ExecutorPolicy$CatchAndRecordFailures.onExecute(ExecutorPolicy.java:64)
        at org.gradle.internal.concurrent.ManagedExecutorImpl$1.run(ManagedExecutorImpl.java:48)
Caused by: org.gradle.process.internal.ExecException: A problem occurred starting process 'command 'null/bin/javac''
        at org.gradle.process.internal.DefaultExecHandle.execExceptionFor(DefaultExecHandle.java:241)
        at org.gradle.process.internal.DefaultExecHandle.setEndStateInfo(DefaultExecHandle.java:218)
        at org.gradle.process.internal.DefaultExecHandle.failed(DefaultExecHandle.java:369)
        at org.gradle.process.internal.ExecHandleRunner.run(ExecHandleRunner.java:87)
        at org.gradle.internal.operations.CurrentBuildOperationPreservingRunnable.run(CurrentBuildOperationPreservingRunnable.java:42)
        at org.gradle.internal.concurrent.ExecutorPolicy$CatchAndRecordFailures.onExecute(ExecutorPolicy.java:64)
        at org.gradle.internal.concurrent.ManagedExecutorImpl$1.run(ManagedExecutorImpl.java:48)
Caused by: net.rubygrapefruit.platform.NativeException: Could not start 'null/bin/javac'
        at net.rubygrapefruit.platform.internal.DefaultProcessLauncher.start(DefaultProcessLauncher.java:27)
        at net.rubygrapefruit.platform.internal.WrapperProcessLauncher.start(WrapperProcessLauncher.java:36)
        at org.gradle.process.internal.ExecHandleRunner.startProcess(ExecHandleRunner.java:98)
        at org.gradle.process.internal.ExecHandleRunner.run(ExecHandleRunner.java:71)
        ... 3 more
Caused by: java.io.IOException: Cannot run program "null/bin/javac" (in directory "/home/sky/work/code/durabletask/durabletask-java/client"): error=2, No such file or directory
        at net.rubygrapefruit.platform.internal.DefaultProcessLauncher.start(DefaultProcessLauncher.java:25)
        ... 6 more
Caused by: java.io.IOException: error=2, No such file or directory
        ... 7 more
```

经过检查，JAVA_HOME 环境变量正常设置，jdk 11 和 17 下都是同样报错。

解决方法，升级 gradle （从7.4版本） 到 7.6.3 版本即可：

```bash
sdk install gradle 7.6.3
```

尝试过 gradle 8.5 版本，报其他错误，也不能用：

```bash
* Exception is:
java.lang.NoSuchMethodError: 'void org.gradle.api.internal.artifacts.dsl.LazyPublishArtifact.<init>(org.gradle.api.provider.Provider)'
        at org.springframework.boot.gradle.plugin.JavaPluginAction.configureArtifactPublication(JavaPluginAction.java:135)
        at org.springframework.boot.gradle.plugin.JavaPluginAction.execute(JavaPluginAction.java:84)
        at org.springframework.boot.gradle.plugin.JavaPluginAction.execute(JavaPluginAction.java:62)
        at org.springframework.boot.gradle.plugin.SpringBootPlugin.lambda$null$0(SpringBootPlugin.java:126)
        at org.gradle.internal.code.DefaultUserCodeApplicationContext$CurrentApplication$1.execute(DefaultUserCodeApplicationContext.java:122)
        at org.gradle.api.internal.DefaultCollectionCallbackActionDecorator$BuildOperationEmittingAction$1.run(DefaultCollectionCallbackActionDecorator.java:110)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$1.execute(DefaultBuildOperationRunner.java:29)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$1.execute(DefaultBuildOperationRunner.java:26)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$2.execute(DefaultBuildOperationRunner.java:66)
        at org.gradle.internal.operations.DefaultBuildOperationRunner$2.execute(DefaultBuildOperationRunner.java:59)
        ......
```

### proto 生成的代码

生成的代码在：

`client/build/generated/source/proto/main/java/com/microsoft/durabletask/implementation/protobuf/OrchestratorService.java`