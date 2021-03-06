<!---
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

* [Hadoop Commands Guide](#Hadoop_Commands_Guide)
    * [Overview](#Overview)
        * [Shell Options](#Shell_Options)
        * [Generic Options](#Generic_Options)
* [Hadoop Common Commands](#Hadoop_Common_Commands)
    * [User Commands](#User_Commands)
        * [archive](#archive)
        * [checknative](#checknative)
        * [classpath](#classpath)
        * [credential](#credential)
        * [distch](#distch)
        * [distcp](#distcp)
        * [fs](#fs)
        * [jar](#jar)
        * [jnipath](#jnipath)
        * [key](#key)
        * [trace](#trace)
        * [version](#version)
        * [CLASSNAME](#CLASSNAME)
    * [Administration Commands](#Administration_Commands)
        * [daemonlog](#daemonlog)
    * [Files](#Files)
        * [etc/hadoop/hadoop-env.sh](#etchadoophadoop-env.sh)
        * [etc/hadoop/hadoop-user-functions.sh](#etchadoophadoop-user-functions.sh)
        * [~/.hadooprc](#a.hadooprc)

Hadoop Commands Guide
=====================

Overview
--------

All of the Hadoop commands and subprojects follow the same basic structure:

Usage: `shellcommand [SHELL_OPTIONS] [COMMAND] [GENERIC_OPTIONS] [COMMAND_OPTIONS]`

| FIELD | Description |
|:---- |:---- |
| shellcommand | The command of the project being invoked. For example, Hadoop common uses `hadoop`, HDFS uses `hdfs`, and YARN uses `yarn`. |
| SHELL\_OPTIONS | Options that the shell processes prior to executing Java. |
| COMMAND | Action to perform. |
| GENERIC\_OPTIONS | The common set of options supported by multiple commands. |
| COMMAND\_OPTIONS | Various commands with their options are described in this documention for the Hadoop common sub-project. HDFS and YARN are covered in other documents. |

### Shell Options

All of the shell commands will accept a common set of options. For some commands, these options are ignored. For example, passing `---hostnames` on a command that only executes on a single host will be ignored.

| SHELL\_OPTION | Description |
|:---- |:---- |
| `--buildpaths` | Enables developer versions of jars. |
| `--config confdir` | Overwrites the default Configuration directory. Default is `$HADOOP_PREFIX/conf`. |
| `--daemon mode` | If the command supports daemonization (e.g., `hdfs namenode`), execute in the appropriate mode. Supported modes are `start` to start the process in daemon mode, `stop` to stop the process, and `status` to determine the active status of the process. `status` will return an [LSB-compliant](http://refspecs.linuxbase.org/LSB_3.0.0/LSB-generic/LSB-generic/iniscrptact.html) result code. If no option is provided, commands that support daemonization will run in the foreground. |
| `--debug` | Enables shell level configuration debugging information |
| `--help` | Shell script usage information. |
| `--hostnames` | A space delimited list of hostnames where to execute a multi-host subcommand. By default, the content of the `slaves` file is used. |
| `--hosts` | A file that contains a list of hostnames where to execute a multi-host subcommand. By default, the content of the `slaves` file is used. |
| `--loglevel loglevel` | Overrides the log level. Valid log levels are FATAL, ERROR, WARN, INFO, DEBUG, and TRACE. Default is INFO. |

### Generic Options

Many subcommands honor a common set of configuration options to alter their behavior:

| GENERIC\_OPTION | Description |
|:---- |:---- |
| `-archives <comma separated list of archives> ` | Specify comma separated archives to be unarchived on the compute machines. Applies only to job. |
| `-conf <configuration file> ` | Specify an application configuration file. |
| `-D <property>=<value> ` | Use value for given property. |
| `-files <comma separated list of files> ` | Specify comma separated files to be copied to the map reduce cluster. Applies only to job. |
| `-jt <local> or <resourcemanager:port>` | Specify a ResourceManager. Applies only to job. |
| `-libjars <comma seperated list of jars> ` | Specify comma separated jar files to include in the classpath. Applies only to job. |

Hadoop Common Commands
======================

All of these commands are executed from the `hadoop` shell command. They have been broken up into [User Commands](#User_Commands) and [Admininistration Commands](#Admininistration_Commands).

User Commands
-------------

Commands useful for users of a hadoop cluster.

### `archive`

Creates a hadoop archive. More information can be found at [Hadoop Archives Guide](../../hadoop-mapreduce-client/hadoop-mapreduce-client-core/HadoopArchives.html).

### `checknative`

Usage: `hadoop checknative [-a] [-h] `

| COMMAND\_OPTION | Description |
|:---- |:---- |
| `-a` | Check all libraries are available. |
| `-h` | print help |

This command checks the availability of the Hadoop native code. See [\#NativeLibraries.html](#NativeLibraries.html) for more information. By default, this command only checks the availability of libhadoop.

### `classpath`

Usage: `hadoop classpath [--glob |--jar <path> |-h |--help]`

| COMMAND\_OPTION | Description |
|:---- |:---- |
| `--glob` | expand wildcards |
| `--jar` *path* | write classpath as manifest in jar named *path* |
| `-h`, `--help` | print help |

Prints the class path needed to get the Hadoop jar and the required libraries. If called without arguments, then prints the classpath set up by the command scripts, which is likely to contain wildcards in the classpath entries. Additional options print the classpath after wildcard expansion or write the classpath into the manifest of a jar file. The latter is useful in environments where wildcards cannot be used and the expanded classpath exceeds the maximum supported command line length.

### `credential`

Usage: `hadoop credential <subcommand> [options]`

| COMMAND\_OPTION | Description |
|:---- |:---- |
| create *alias* [-v *value*][-provider *provider-path*] | Prompts the user for a credential to be stored as the given alias when a value is not provided via `-v`. The *hadoop.security.credential.provider.path* within the core-site.xml file will be used unless a `-provider` is indicated. |
| delete *alias* [-i][-provider *provider-path*] | Deletes the credential with the provided alias and optionally warns the user when `--interactive` is used. The *hadoop.security.credential.provider.path* within the core-site.xml file will be used unless a `-provider` is indicated. |
| list [-provider *provider-path*] | Lists all of the credential aliases The *hadoop.security.credential.provider.path* within the core-site.xml file will be used unless a `-provider` is indicated. |

Command to manage credentials, passwords and secrets within credential providers.

The CredentialProvider API in Hadoop allows for the separation of applications and how they store their required passwords/secrets. In order to indicate a particular provider type and location, the user must provide the *hadoop.security.credential.provider.path* configuration element in core-site.xml or use the command line option `-provider` on each of the following commands. This provider path is a comma-separated list of URLs that indicates the type and location of a list of providers that should be consulted. For example, the following path: `user:///,jceks://file/tmp/test.jceks,jceks://hdfs@nn1.example.com/my/path/test.jceks`

indicates that the current user's credentials file should be consulted through the User Provider, that the local file located at `/tmp/test.jceks` is a Java Keystore Provider and that the file located within HDFS at `nn1.example.com/my/path/test.jceks` is also a store for a Java Keystore Provider.

When utilizing the credential command it will often be for provisioning a password or secret to a particular credential store provider. In order to explicitly indicate which provider store to use the `-provider` option should be used. Otherwise, given a path of multiple providers, the first non-transient provider will be used. This may or may not be the one that you intended.

Example: `-provider jceks://file/tmp/test.jceks`

### `distch`

Usage: `hadoop distch [-f urilist_url] [-i] [-log logdir] path:owner:group:permissions`

| COMMAND\_OPTION | Description |
|:---- |:---- |
| `-f` | List of objects to change |
| `-i` | Ignore failures |
| `-log` | Directory to log output |

Change the ownership and permissions on many files at once.

### `distcp`

Copy file or directories recursively. More information can be found at [Hadoop DistCp Guide](../../hadoop-mapreduce-client/hadoop-mapreduce-client-core/DistCp.html).

### `fs`

This command is documented in the [File System Shell Guide](./FileSystemShell.html). It is a synonym for `hdfs dfs` when HDFS is in use.

### `jar`

Usage: `hadoop jar <jar> [mainClass] args...`

Runs a jar file.

Use [`yarn jar`](../../hadoop-yarn/hadoop-yarn-site/YarnCommands.html#jar) to launch YARN applications instead.

### `jnipath`

Usage: `hadoop jnipath`

Print the computed java.library.path.

### `key`

Manage keys via the KeyProvider.

### `trace`

View and modify Hadoop tracing settings. See the [Tracing Guide](./Tracing.html).

### `version`

Usage: `hadoop version`

Prints the version.

### `CLASSNAME`

Usage: `hadoop CLASSNAME`

Runs the class named `CLASSNAME`. The class must be part of a package.

Administration Commands
-----------------------

Commands useful for administrators of a hadoop cluster.

### `daemonlog`

Usage: `hadoop daemonlog -getlevel <host:port> <name> ` Usage: `hadoop daemonlog -setlevel <host:port> <name> <level> `

| COMMAND\_OPTION | Description |
|:---- |:---- |
| `-getlevel` *host:port* *name* | Prints the log level of the daemon running at *host:port*. This command internally connects to http://host:port/logLevel?log=name |
| `-setlevel` *host:port* *name* *level* | Sets the log level of the daemon running at *host:port*. This command internally connects to http://host:port/logLevel?log=name |

Get/Set the log level for each daemon.

Files
-----

### **etc/hadoop/hadoop-env.sh**

This file stores the global settings used by all Hadoop shell commands.

### **etc/hadoop/hadoop-user-functions.sh**

This file allows for advanced users to override some shell functionality.

### **~/.hadooprc**

This stores the personal environment for an individual user. It is processed after the hadoop-env.sh and hadoop-user-functions.sh files and can contain the same settings.
