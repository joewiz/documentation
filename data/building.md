# Building eXist-db

## Building a WAR from the Standard Distribution

If you would like to deploy eXist-db into an existing servlet engine (e.g. tomcat), you will need a `.war` file. We no longer distribute a `.war` build, but it is easy to create one from the standard distribution by *repackaging* (not building) it:

Get a standard distribution from eXist-db [download page](http://exist-db.org/exist/apps/homepage/index.html#download). Install it into a directory of your choice. Make sure you include the *source package*.

Open a command line prompt and change to the directory where you just installed eXist-db. Call

build.sh dist-war
or

build.bat dist-war
once. This will generate a `.war` archive into directory `dist`.

Note that generating a WAR archive does only repackage the files in the distribution, so if the distribution worked properly for you, the WAR should as well. You don't need to recompile eXist-db or sign any jars.

## Building eXist-db from GitHub

Building eXist-db is quite easy and requires 3 simple steps:

The GitHub URL for the develop branche is:

git@github.com:eXist-db/exist.git
Please follow the instructions on [our GitHub page](https://github.com/eXist-db/exist) page ([README.md](https://github.com/eXist-db/exist/blob/develop/README.md) section)

Before starting the build, your JAVA\_HOME environment variable should be set to point to the root directory of the JAVA JDK (note: JDK, not JRE!). Open a console or a DOS box on windows and type:

set JAVA\_HOME=c:\\path\\to\\jdk
on windows or

export JAVA\_HOME=/path/to/jdk
on unix.

Change into the eXist-db directory you just checked out and call

build.bat
on windows or on unix:

build.sh
## Using the Build System

You can rebuild all eXist-db packages from the standard distribution or the GitHub repository. You can even create a new distribution from the one you installed. For example, assume you installed eXist using the installer GUI, but you need to have an exist.war package for deployment within a different servlet engine. Just call the main build script (`build.sh` or `build.bat`) and pass it the target `dist-war`:

build.sh dist-war
This will create a fresh `.war` archive in the `dist` directory.

> **Note**
>
> To be able to use the build system, you need to include the "source" module when selecting installable packages in the installer GUI.

eXist-db relies on Ant for all compile and build tasks. Ant is included in the distribution (directory `tools/ant`). As already mentioned above, there are two shell scripts to start Ant: `build.sh` or `build.bat`. The shell scripts will take care to create a proper command line to launch Java with the Ant version included in the distribution.

Calling Ant without further argument will compile the sources and create the main jar-files (`exist.jar`, `exist-optional.jar`, `start.jar`).

build.sh -p
lists the available build targets. Passing one of these as argument to ant will call execute the corresponding target. The main targets are:

|                          |                                                                                                                                                                |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| all                      | Compile the sources and create the main jar-files (`exist.jar`, `exist-optional.jar`, `start.jar`).                                                            |
| benchmark                | Performs the benchmark tests for eXist-db.                                                                                                                     |
| clean                    | Cleans the files and directories generated by a previous build. Data is preserved.                                                                             |
| clean-all                | Cleanup deeper.                                                                                                                                                |
| clean-conf               | Cleanup config file.                                                                                                                                           |
| clean-default-data-dir   | Remove contents in default data directory.                                                                                                                     |
| compile                  | Compiles the source code.                                                                                                                                      |
| compile-aspectj          | Compiles the aspects.                                                                                                                                          |
| dist-tgz                 | Packages eXist-db into a `.tgz` file.                                                                                                                          |
| dist-war                 | Packages eXist-db into a `.war` file for deployment within a servlet engine.                                                                                   |
| dist-zip                 | Packages eXist-db into a `.zip` file.                                                                                                                          |
| download-additional-jars | Download optional third-party jar files. These are currently: saxon. These are downloaded into `lib/user/`.                                                    |
| download-icu4j           | Download the optional icu4j library.                                                                                                                           |
| download-xars            | Download xars to include in installer.                                                                                                                         |
| download-xqts            | Download XQTS file.                                                                                                                                            |
| download-xslts           | Download XSLTS file.                                                                                                                                           |
| extension-modules        | Build the extensions directory. Edit the file `extensions/build.properties` enable extensions and modules.                                                     |
| installer                | Creates a new installer package based on [IzPack](http://izpack.org/).                                                                                         |
| installer-exe            | Create `.exe` from installer                                                                                                                                   |
| jar                      | The default: compiles the source and creates eXist's main `.jar` files.                                                                                        |
| javadocs                 | Creates the API docs into `webapp/api`.                                                                                                                        |
| jdepend                  | JDepend traverses Java class file directories and generates design quality metrics for each Java package.                                                      |
| jetty-keygen             | Generate keystore for jetty.                                                                                                                                   |
| jnlp-clean               |                                                                                                                                                                |
| jnlp-keygen              | Generate keystore for signing jars.                                                                                                                            |
| jnlp-pack200             | Pack all jar files.                                                                                                                                            |
| jnlp-sign-all            | This signs all of the jar files, so that the webstart of the [eXist-db client Java application](java-admin-client.md) will launch without any signing errors. |
| jnlp-sign-core           | Sign all CORE jar files.                                                                                                                                       |
| jnlp-sign-exist          | Sign all EXIST jar files.                                                                                                                                      |
| jnlp-unsign-all          | This unsigns all of the jar files.                                                                                                                             |
| rebuild                  | Performs a *clean* and the *all*.                                                                                                                              |
| samples                  | Builds the examples and packs them into `examples.jar`.                                                                                                        |
| snapshot-installer       | Creates a snapshot installer.                                                                                                                                  |
| test                     | Runs all tests in the test suite and outputs the HTML result into `test/junit/html`. This may take several minutes.                                            |
| xquery                   | Regenerate the XQuery parser code with ANTLR (only required if you change `src/org/exist/xquery/parser/XQuery.g`).                                             |
| xars                     | Scan apps directory and include all .xar files into installer.                                                                                                 |

> **Note**
>
> You might want to set some custom settings (in particular your HTTP proxy when external libraries are required) before running `build.sh` or `build.bat`. See `build.properties`.

## Signing jar files for webstart

The database can be easily accessed with the Java client, when the client is launched by clicking the [Java Webstart](http://en.wikipedia.org/wiki/Java_Web_Start) "Launch" button on the right side of this page. Java webstart explicilty requires that all application code, the 'jar' files, are [signed](http://java.sun.com/docs/books/tutorial/deployment/jar/signing.html) using a security certificate. Out of the box, when eXist-db is installed using the installer, all relevant jar files have already been signed, so webstart can be used directly. It is nessecary however to (re-)sign the jar files yourself if you want to (re-)build the eXist-db java code. This chapter explains how to do this.

### Quickstart

To get started quickly, just execute the following command:

./build.sh -f build/scripts/jarsigner.xml
This command sequentially creates the key.store file and signs a selection of jar files:

    ./build.sh -f build/scripts/jarsigner.xml 
    Starting Ant...

    Buildfile: ..../eXist-db/build/scripts/jarsigner.xml

    jnlp-prepare:

    jnlp-keygen:
       [genkey] Generating Key for exist

    jnlp-sign-exist:
      [signjar] Signing JAR: ..../eXist-db/exist-fluent.jar to ..../eXist-db/exist-fluent.jar as exist
      [signjar] Signing JAR: ..../eXist-db/exist-optional.jar to ..../eXist-db/exist-optional.jar as exist
      [signjar] Signing JAR: ..../eXist-db/exist.jar to ..../eXist-db/exist.jar as exist
      [signjar] Signing JAR: ..../eXist-db/start.jar to ..../eXist-db/start.jar as exist

    jnlp-sign-core:
      [signjar] Signing JAR: ..../eXist-db/lib/core/antlr-2.7.7.jar to ..../eXist-db/lib/core/antlr-2.7.7.jar as exist
      [signjar] Signing JAR: ..../eXist-db/lib/core/commons-collections-3.2.1.jar to ..../eXist-db/lib/core/commons-collections-3.2.1.jar as exist
      ....
      [signjar] Signing JAR: ..../eXist-db/lib/core/xmlrpc-server-3.1.3.jar to ..../eXist-db/lib/core/xmlrpc-server-3.1.3.jar as exist

Note that this command needs to be executed just once. In subsequent builds newly generated jars are signed automatically.

### Ant targets

An overview of all available ant targets can be retrieved by executing the following command:

./build.sh -projecthelp -f build/scripts/jarsigner.xml
The following ant targets are available for signing jar files:

jnlp-all  
Create keystore file and sign all EXIST and CORE jar files. **(Default task)**

jnlp-keygen  
Generate a new keystore file if not present.

jnlp-clean  
Delete the keystore file.

jnlp-sign-core  
Sign all CORE jar files in `lib/core`.

jnlp-sign-exist  
Sign all EXIST jar files, e.g. exist.jar, exist-XX.jar and start.jar

jnlp-unsign-all  
Unsign all jar files.

The target **jnlp-keygen** generates a new certificate file `key.store`. If this file is present the **EXIST jar files** are signed during each following build. The process of signing jar files can be stopped by removing this keystore file.

The ant script contains two additional targets: **jetty-keygen** which generates a keystore file for the jetty server and **jnlp-pack200** which processes the signed jar files with **pack200** technology. More about the latter in the next section.

### Reducing jar sizes with pack200 compression

Pack200 is a compression technology introduced in Java 1.5.0. It has been designed for compressing jar files and works most efficiently on Java class files. Pack200 can reduce the size of a jar file up to 60% resulting into a significant reduction of the amount of downloaded webstart data.

The webstart jar files can be compressed with pack200 with the following command: ./build.sh -f build/scripts/jarsigner.xml jnlp-pack200 This command consecutively performs the following steps for all jar files:

-   unsign

-   repack (normalization)

-   sign

-   compress with pack200

-   compress with gzip

<!-- -->

                        ./build.sh -f build/scripts/jarsigner.xml jnlp-pack200
                        Starting Ant...
                        
                        Buildfile: .../eXist-db/build/scripts/jarsigner.xml
                        
                        jnlp-pack200:
                        
                        jnlp-unsign-all:
                        [unsignjar] Unsigning exist-fluent.jar
                        [unsignjar] Unsigning exist-optional.jar
                        [unsignjar] Unsigning exist.jar
                        [unsignjar] Unsigning start.jar
                        [unsignjar] Unsigning antlr-2.7.7.jar
                        ...
                        [unsignjar] Unsigning xmlrpc-server-3.1.3.jar
                        [repack] Normalizing exist-fluent.jar
                        [repack] Normalizing exist-optional.jar
                        [repack] Normalizing exist.jar
                        [repack] Normalizing start.jar
                        [repack] Normalizing antlr-2.7.7.jar
                        ...
                        [repack] Normalizing xmlrpc-server-3.1.3.jar
                        
                        jnlp-prepare:
                        
                        jnlp-keygen:
                        
                        jnlp-sign-exist:
                        [signjar] Signing JAR: .../eXist-db/exist-fluent.jar to .../eXist-db/exist-fluent.jar as exist
                        [signjar] Signing JAR: .../eXist-db/exist-optional.jar to .../eXist-db/exist-optional.jar as exist
                        [signjar] Signing JAR: .../eXist-db/exist.jar to .../eXist-db/exist.jar as exist
                        [signjar] Signing JAR: .../eXist-db/start.jar to .../eXist-db/start.jar as exist
                        
                        jnlp-prepare:
                        
                        jnlp-keygen:
                        
                        jnlp-sign-core:
                        [signjar] Signing JAR: .../eXist-db/lib/core/antlr-2.7.7.jar to .../eXist-db/lib/core/antlr-2.7.7.jar as exist
                        ...
                        [signjar] Signing JAR: .../eXist-db/lib/core/xmlrpc-server-3.1.3.jar to .../eXist-db/lib/core/xmlrpc-server-3.1.3.jar as exist
                        [pack] Packing exist-fluent.jar
                        [pack] Packing exist-optional.jar
                        [pack] Packing exist.jar
                        [pack] Packing start.jar
                        [pack] Packing antlr-2.7.7.jar
                        ...
                        [pack] Packing xmlrpc-server-3.1.3.jar
                        
                        BUILD SUCCESSFUL
                        Total time: 1 minute 0 seconds
                            

The pack200 compression feature works with Java SE 6 update 10 ([Java6u10](http://java.sun.com/developer/technicalArticles/javase/java6u10/#usability)) or newer. Older Java6 versions will only receive the larger jar files.

### References

-   [Java Web Start](http://en.wikipedia.org/wiki/Java_Web_Start) (Wikipedia)

-   Java Web Start: [Pack200 compression](http://en.wikipedia.org/wiki/Java_Web_Start#Pack200_compression) (Wikipedia)

-   [Pack200](http://en.wikipedia.org/wiki/Pack200) (Wikipedia)

-   [Pack200](http://wiki.eclipse.org/Pack200) (Eclipse.org)

-   [Pack200 and Compression](http://java.sun.com/j2se/1.5.0/docs/guide/deployment/deployment-guide/pack200.html) for network deployment (Sun, bit out dated)

-   Introducing Java SE 6 update 10: [Deployment Usability](http://java.sun.com/developer/technicalArticles/javase/java6u10/#usability) (Sun).

-   Ant tasks: [ASOCAT](http://code.google.com/p/existdb-contrib/wiki/ASOCAT).

### Notes

-   Sometimes when the jar files are signed, eXist-db does not start anymore. In this situation unsign the jar files first. Calling target 'jnlp-pack200' does always unsign all jar files as first step.

-   The pack200 target is not executed automatically. If eXist-db code is changed and recompiled, you might want to execute the jnlp-pack200 target again.
