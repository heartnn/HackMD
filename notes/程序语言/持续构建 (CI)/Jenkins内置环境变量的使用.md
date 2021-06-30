# Jenkins内置环境变量的使用

## 一、查看Jenkins有哪些环境变量

1、新建任意一个job

2、增加构建步骤：Execute shell 或 Execute Windows batch command

3、点击输入框下方的“可用环境变量”

![](vx_images/20200204203824475_16509.jpg)

4、可以看到有如下变量供使用：

| 变量名 | 解释 |
| --- | --- |
| BUILD_NUMBER | The current build number, such as "153" |
| BUILD_ID | The current build ID, identical to BUILD_NUMBER for builds created in 1.597+, but a YYYY-MM-DD_hh-mm-ss timestamp for older builds |
| BUILD_DISPLAY_NAME | The display name of the current build, which is something like "#153" by default. |
| JOB_NAME | Name of the project of this build, such as "foo" or "foo/bar". (To strip off folder paths from a Bourne shell script, try: `${JOB_NAME##*/}`) |
| BUILD_TAG | String of "`jenkins-${JOB_NAME}-${BUILD_NUMBER}`". Convenient to put into a resource file, a jar file, etc for easier identification. |
| EXECUTOR_NUMBER | The unique number that identifies the current executor (among executors of the same machine) that’s carrying out this build. This is the number you see in the "build executor status", except that the number starts from 0, not 1. |
| NODE_NAME | Name of the slave if the build is on a slave, or "master" if run on master |
| NODE_LABELS | Whitespace-separated list of labels that the node is assigned. |
| WORKSPACE | The absolute path of the directory assigned to the build as a workspace. |
| JENKINS_HOME | The absolute path of the directory assigned on the master node for Jenkins to store data. |
| JENKINS_URL | Full URL of Jenkins, like http://server:port/jenkins/ (note: only available if Jenkins URL set in system configuration) |
| BUILD_URL | Full URL of this build, like http://server:port/jenkins/job/foo/15/ (Jenkins URL must be set) |
| SVN_REVISION | Subversion revision number that's currently checked out to the workspace, such as "12345" |
| SVN_URL | Subversion URL that's currently checked out to the workspace. |
| JOB_URL | Full URL of this job, like http://server:port/jenkins/job/foo/ (Jenkins URL must be set) |

## 二、使用Jenkins的内置变量

1、在Execute shell 或 Execute Windows batch command文本框中使用，使用方法：`%变量名%`，如下图

![](vx_images/20200204203824475_16509.jpg)

2、结合Ant，在build.xml文件中使用：

　　1) 添加如下第4行代码：`<property environment="env"/>`

　　2) 使用方法：`${env.WORKSPACE}`

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project name="ant-test" default="run" basedir=".">
    <property environment="env"/>

    <target name="clean">
        <mkdir dir="${env.WORKSPACE}/results/${env.BUILD_ID}" />
    </target>

</project>
```

---
via: http://www.cnblogs.com/puresoul/p/4828913.html
