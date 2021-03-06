# jchmlib

`jchmlib` is a Java library for reading CHM (Microsoft Compiled HTML Help) files.

Several CHM utilities are provided, 
including a web server (`ChmWeb`) for reading CHM files in web browsers.

Features:
* The `jchmlib` is written in Java only, rather than wrapping existing C/C++ library.
  It is easier to get it working on different platforms.
* Better search support:
  * full-text-search (FTS) is supported if there is built-in FTS index in CHM file.
  * otherwise, FTS index can be generated and searched on.
  * enumeration-based search by searching through the pages in CHM file can also be used without FTS index.
  * Phrase search is supported.
* Better support for non-English languages, like Chinese, Japanese, Korean.
  *  It will detect CHM file encoding, and can try to fix encoding when it is wrongly set in CHM file.
  *  It can show table of contents (topics tree) correctly for non-English files.
  *  It can also search using non-English search words in non-English files.
* The `ChmWeb` is a web server for serving CHM files, so that you can read CHM files in your favorite web browser.
  * The sidebar of the web UI shows table of contents, files tree (for listing files/directories in the CHM archive),
  and search box. 
  * It uses javascript so that switching between tabs of the sidebar would not incur reloading the whole sidebar.
  * When network is properly configured, you can run `ChmWeb` on a server, and access it from other devices.
* There are also some utility classes which you can use as command line tools for handling CHM files,
  like listing/searching/extracting files in CHM file (see `src/app`).  

# How to build

This project uses [Gradle](https://gradle.org/) as the build tool.

`javapackager` in [Oracle JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
 is used for building native bundles (like dmg, deb, rpm) of `ChmWeb`.
 
`jchmlib` itself is supposed to be compatible with JDK 1.6 or higher (including OpenJDK).

## Building `jchmlib`

To build jar for `jchmlib` itself, run
```
gradle jar
```

Use `gradle javadoc` to build javadoc for `jchmlib`,
the docs can be found under `build/docs/javadoc`.

## Building `ChmWeb`

To build jar for `ChmWeb`, run
```
gradle appJar
```

Native bundles have to be build on their target platforms.

On Mac OS X, build dmg using
```
gradle package_dmg
```

Please make sure `package/macosx/Info.plist` matches your settings (like version, JVMRuntime).
The dmg can be found under `build/deploy/bundles`.

On Linux, build rpm using
```
gradle package_rpm
```

On some Linux distributions, you may have to install the `rpmbuild` tool first.
 On Ubuntu, you can get it by `sudo apt-get install rpm`.

On Linux, build deb using
```
gradle package_deb
```

On Windows, build exe using
```
gradle package_exe
```

This will build a installer for installing `ChmWeb`.

You can also build exe for Windows on other platforms using
```
gradle createExe
```

the exe can be found under `build/launch4j`.
Note that JRE is not bundled into the exe for now.
You can change the launch4j task in build.gradle to bundle JRE as well.

# `jchmlib` usage

You can start by reading javadoc, and sample applications under `org.jchmlib.app`.

To use `jchmlib` in your project, you can add this to your `build.gradle` 
(when you are using Gradle):
```
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
   }
   dependencies {
         compile 'com.github.chimenchen:jchmlib:v0.5.4'
   }
```

# `ChmWeb` usage

You can run `ChmWeb` in different ways.

## Run `ChmWeb` using jar

You need to have JDK/JRE installed.

You can start it from command line like:
```
java -jar ChmWeb.jar
```
This will open a Swing window.
You can drag and drop CHM files into the window to open them,
or right click and choose "Open CHM file".

A web server will be started to serve each CHM file,
 and the default web browser will be opened to view the CHM file.

In the ChmWeb window, you can double click on a row to open it in browser
(or right click and choose "Open in browser").

To close CHM files, right click and choose "Close".

When you run
```
java -jar ChmWeb.jar test.chm
```

it will start and serve the given CHM file.
Note that, if there is already one instance running,
the file will be opened in that instance instead.

`ChmWeb` will try to find free ports starting from 50000 to serve CHM files,
to serve a file on a given port, say, 9000, run
```
java -jar ChmWeb.jar -p 9000 test.chm
```
But this port will only be used for this file.

To open the file without showing the window,
 run with the `--no-gui` or `-n` option, like:
```
java -jar ChmWeb.jar -n test.chm
```

You can also double click the jar to open `ChmWeb`,
if you have your system properly configured
 (for example, on Windows, you need to associate jar file type with javaw.exe).
 
## Run `ChmWeb` using native executable

The problem of using jar directly is that it may not work as expected
in file managers of some platforms.
For example, you may want to right click on a CHM file and choose to open with ChmWeb,
or to associate chm file type with ChmWeb so that you can double click to open CHM files with ChmWeb,
it is not allowed on some platforms when using jar.

To make it easier to open CHM files with ChmWeb in file manager,
you can wrap the jar using shell scripts or use native executables.

On Mac OS X, you can build DMG and install the app in DMG (drag to Applications).

On Linux, you can build rpm or deb, and install them using package manager.
 It will normally be installed to `/opt/ChmWeb/`.

On Windows, you can build exe.
The version built using javapackager (`gradle package_exe`) has to be installed first.
The version built using launch4j (`gradle createExe`) is smaller and doesn't have to be installed,
but it requires JDK/JRE to be installed.

The native executables (installers) can now be found in the [releases page](https://github.com/chimenchen/jchmlib/releases) of this project.

## Web UI of `ChmWeb`

On the search tab, search using
```
several query words
```
will return pages containing all the query words, ignoring cases.

Search using phrase like
```
"hello world"
```
will return pages containing the phrase.
Non-word characters (like punctuations) or some stopwords (like "a", "is") are ignored,
 so it is possible to match pages with phrase "Hello, World".
 
Search using regular expressions (when "Use regex" is checked) is enumeration-based and can be slow.
You don't have use regular expressions though.
 Here are some typical query strings:
```
abc
abc abc
"query string" (go)*
Http[a-zA-Z]*Request\(.*\);
```

The "Toggle Highlight" button can be used to highlight query words in the page
 and scroll to the first occurrence.
But it is not smart enough to handle complex query strings involving regular expressions
 or to handle the characters and stopwords ignored during search.
 Sometimes, the occurrences are not highlight as expected.
 
# Authors

* Chen Zhongguo (陈钟国) - Initial work - [chimenchen](https://github.com/chimenchen)

# License

This project is licensed under Apache License 2.0, 
see the [LICENSE](LICENSE) file for details.
