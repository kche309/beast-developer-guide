# BEAST v2.6 development setup
This guide will go through how to set up BEAST v2.6.x development using IntelliJ. 
This readme is based on the tutorial http://www.beast2.org/writing-a-beast-2-package/ 

## Tools you will need:
* [Git](https://git-scm.com/downloads) (Git Bash recommended for Windows)
* [IntelliJ IDE](https://www.jetbrains.com/idea/)
* Java JDK 8 install (aka Java 1.8) 
* [Apache Ant](https://ant.apache.org/manual/install.html) for building packages
* BEAST2 install (e.g. [BEAST v2.6.7](https://github.com/CompEvol/beast2/releases/tag/v2.6.7))

## Cloning the code repositories
Create a directory in your computer for your git repositories, e.g., from terminal execute
```
cd ~
mkdir Repos
```
From your terminal, clone the BEAST2 repo
```
git clone https://github.com/CompEvol/beast2.git
```
Switch BEAST source code to version 2.6
```
cd beast2
git checkout v2.6
```
Clone other BEAST2 repos that your package may use. 

For example, the BEASTLabs package contains many helpful methods. 
To clone this repo we use the following command
```
cd ~/Repos
git clone https://github.com/BEAST2-Dev/BEASTLabs.git
```
Switch BEASTLabs source code to a version compatible with BEAST 2.6
```
cd BEASTLabs
git checkout v2.6
```

## Setting up your BEAST package in IntelliJ
In the IntelliJ Menu go to **File -> New -> Project** to create your beast2 project in your workspace. 

Select the Java project from the left panel, and set your *Project SDK* to *Java 1.8*

You can name your project any name, in this guide we will refer to your project as `myBeastProject`

Your project directory would have a path like `~/IdeaProjects/myBeastProject`. This may be different depending on your Operating System. 

### Import beast2
From the Menu, go to **File -> Project Structure** to open the Project Structure Window. 

In the Project Structure Window, go to *Modules* from the left panel.

(i) Click "+" then **Add -> Import Module** and import the beast2 repository in `~/Repos/beast2` 

(ii) Click Next until you reach 'Libraries'

(iii) In Libraries, edit the beast2 library name from 'lib' to 'beast-lib'

(iv) In Libraries, remove the DensiTree.jar  

(v) Continue until you reach 'Finish'.

### Import BEASTLabs
In the Project Structure Window, go to *Modules* from the left panel.

(i) Click "+" then **Add -> Import Module** and import the BEASTLabs repository in `~/Repos/BEASTLabs` 

(ii) Click Next until you reach 'Libraries'

(iii) Continue until you reach 'Finish'.

### Add module dependancies
In the Project Structure Window, go to *Modules* (left panel)

Click on the **BEASTLabs** module (middle panel) and select the 'Dependencies' tab (right panel).

Under 'Dependencies' in the right panel, click "+" **Module Dependency** and select beast2. This will add the beast2 dependency to BEASTLabs. 
In general, you can check the `version.xml` in each package for a list of dependencies. 

Click on the **myBeastProject** module (middle panel) and select the 'Dependencies' tab (right panel).

Under 'Dependencies' in the right panel, click "+" **Module Dependency** and select beast2.

Under 'Dependencies' in the right panel, click "+" **Module Dependency** and select BEASTLabs.

This will make your project depend on both beast2 and BEASTLabs. 

### Versioning your package
Create a version.xml file in your project directory e.g.
```
nano ~/IdeaProjects/myBeastProject/version.xml
```

The version.xml should have the following contents:
```
<package name='myBeastProject' version='0.0.1'>
  <depends on='beast2' atleast='2.6.6' atmost="2.6.9"/>
  <depends on='BEASTLabs' atleast='1.9.6' atmost="1.9.9"/>
</package>
```

## Implementing the F84 substitution model 
Follow the steps for **Creating an F84 class** in http://www.beast2.org/writing-a-beast-2-package/

Sections:
* Creating an F84 class
* Adding the k parameter
* Adding the initAndValidate method
* Implementing the canHandleDataType method
* Implementing the getTransitionProbabilities method
* Adding a description and citation

## Adding a beauti template
Follow the steps for **Adding the F84 model to BEAUti** in http://www.beast2.org/writing-a-beast-2-package/ 

Sections:
* Create a template
* Run BEAUti
* Run the f84example.xml file

## Using Ant to build your package for release
To build the package, we will need to create a `build.xml` in your project directory e.g., `~/IdeaProjects/myBeastProject`. 

Paste the following contents into your `myBeastProject/build.xml`
```
<project basedir="." default="build_jar_all_F84" name="BUILD_F84">
    <description>
        Build F84.
        JUnit test is available for this build.
        $Id: build_F84.xml $
    </description>
    <!-- set global properties for this build -->
    <property name="srcF84" location="src" />
    <property name="buildF84" location="build" />
    <property name="libF84" location="lib" />
    <property name="release_dir" value="release" />
    <property name="distF84" location="${buildF84}/dist" />
    <property name="beast2path" location="../beast2" />
    <property name="libBeast2" location="${beast2path}/lib" />
    <property name="srcBeast2" location="${beast2path}/src" />
    <property name="beast2classpath" location="${beast2path}/build" />
    <property name="Add_on_dir" value="${release_dir}/package" />
    <import file="${beast2path}/build.xml" />
    <property name="main_class_BEAST" value="beast.app.BeastMCMC" />
    <property name="report" value="${buildF84}/junitreport"/>
    <path id="classpath">
        <pathelement path="${buildF84}"/>
        <fileset dir="${libBeast2}" includes="junit-4.8.2.jar"/>
        <fileset dir="${libBeast2}" includes="jam.jar"/>
        <fileset dir="${libBeast2}" includes="beagle.jar"/>
        <fileset dir="${libBeast2}" includes="fest.jar"/>
        <fileset dir="${libBeast2}" includes="colt.jar"/>
        <fileset dir="${libBeast2}" includes="antlr-runtime-4.7.jar"/>
        <fileset dir="${libBeast2}" includes="commons-math3-3.1.1.jar"/>
        <pathelement path="${beast2classpath}"/>
    </path>
    <!-- start -->
    <target name="initF84">
        <echo message="${ant.project.name}: ${ant.file}" />
    </target>
    <target name="cleanF84">
        <delete dir="${buildF84}" />
    </target>

    <!-- clean previous build, and then compile Java source code, and Juint test -->
    <target name="build_all_F84" depends="cleanF84,compile-allF84,junitF84" description="Clean and Build all run-time stuff">
    </target>

    <!-- clean previous build, compile Java source code, and Junit test, and make the beast.jar and beauti.jar -->
    <target name="build_jar_all_F84" depends="cleanF84,compile-allF84,junitF84,dist_all_F84" description="Clean and Build all run-time stuff">
    </target>

    <!-- No JUnit Test, clean previous build, compile Java source code, and make the F84.jar and beauti.jar -->
    <target name="build_jar_all_F84_NoJUnitTest" depends="cleanF84,compile-allF84,dist_all_F84" description="Clean and Build all run-time stuff">
    </target>

    <!-- compile Java source code -->
    <target name="compile-allF84" depends="initF84,compile-all">
        <!-- Capture the path as a delimited property using the refid attribute -->
        <property name="myclasspath" refid="classpath"/>
        <!-- Emit the property to the ant console -->
        <echo message="Classpath = ${myclasspath}"/>
        <mkdir dir="${buildF84}" />
        <!-- Compile the java code from ${srcF84} into ${buildF84} /bin -->
        <javac srcdir="${srcF84}" destdir="${buildF84}" classpathref="classpath" fork="true" memoryinitialsize="256m" memorymaximumsize="256m">
            <include name="beast/**/**" />
            <!-- compile JUnit test classes -->
            <include name="test/beast/**" />
        </javac>
        <echo message="Successfully compiled." />
    </target>

    <!-- make the beast.jar and beauti.jar -->
    <target name="dist_all_F84" depends="compile-allF84" description="create F84 jar">
        <!-- Create the distribution directory -->
        <mkdir dir="${distF84}" />
        <!-- Put everything in ${buildF84} into the beast.jar file -->
        <jar jarfile="${distF84}/F84.jar">
            <manifest>
                <attribute name="Built-By" value="${user.name}" />
                <attribute name="Main-Class" value="${main_class_BEAST}" />
            </manifest>
            <fileset dir="${buildF84}">
                <include name="beast/**/*.class" />
            </fileset>
            <fileset dir="${beast2classpath}">
                <include name="beast/**/*.class" />
                <include name="beast/**/*.properties" />
                <include name="beast/**/*.png" />
                <include name="beagle/**/*.class" />
                <include name="org/**/*.class" />
            </fileset>
        </jar>
        <jar jarfile="${distF84}/F84.src.jar">
            <fileset dir="${srcF84}">
                <include name="beast/**/*.java" />
                <include name="beast/**/*.png" />
                <include name="beast/**/*.xsl" />
            </fileset>
        </jar>
        <jar jarfile="${distF84}/F84.addon.jar">
            <manifest>
                <attribute name="Built-By" value="${user.name}" />
            </manifest>
            <fileset dir="${buildF84}">
                <include name="beast/**/*.class" />
                <include name="util/**/*.class" />
                <include name="**/*.properties" />
            </fileset>
        </jar>
    </target>

    <!-- run beast.jar -->
    <target name="run_F84">
        <java jar="${distF84}/F84.jar" fork="true" />
    </target>

    <!-- JUnit test -->
    <target name="junitF84">
        <mkdir dir="${report}" />
        <junit printsummary="yes"> <!--showoutput='yes'-->
            <classpath>
                <path refid="classpath" />
                <path location="${buildF84}" />
            </classpath>
            <formatter type="xml" />
            <batchtest fork="yes" todir="${report}">
                <fileset dir="${srcF84}">
                    <include name="test/**/*Test.java"/>
                </fileset>
                <fileset dir="${srcBeast2}">
                    <include name="test/beast/integration/**/*Test.java"/>
                    <exclude name="test/beast/integration/**/ResumeTest.java"/>
                </fileset>
            </batchtest>
        </junit>
        <echo message="JUnit test finished." />
    </target>
    <target name="junitreport">
        <junitreport todir="${report}">
            <fileset dir="${report}" includes="*.xml"/>
            <report format="frames" todir="${report}"/>
        </junitreport>
        <echo message="JUnit test report finished." />
    </target>
    <target name="addon"
            depends="build_jar_all_F84_NoJUnitTest"
            description="release BEAST 2 package version of F84">
        <delete dir="${Add_on_dir}" />
        <!-- Create the release directory -->
        <mkdir dir="${Add_on_dir}" />
        <mkdir dir="${Add_on_dir}/lib" />
        <mkdir dir="${Add_on_dir}/templates" />
        <mkdir dir="${Add_on_dir}/examples" />
        <copy todir="${Add_on_dir}">
            <fileset file="version.xml"/>
        </copy>
        <copy todir="${Add_on_dir}/examples">
            <fileset dir="examples" includes="*.xml">
            </fileset>
        </copy>
        <copy todir="${Add_on_dir}/lib">
            <fileset dir="${distF84}" includes="F84.addon.jar" />
        </copy>
        <copy todir="${Add_on_dir}">
            <fileset dir="${distF84}" includes="F84.src.jar" />
        </copy>
        <copy todir="${Add_on_dir}/templates">
            <fileset file="templates/F84.xml" />
        </copy>
        <jar jarfile="${distF84}/F84.addon.zip">
            <fileset dir="${Add_on_dir}">
                <include name="**/*" />
            </fileset>
        </jar>
        <echo message="Package version release is finished." />
    </target>
</project>
```

You may need to edit the beast2 path in your `build.xml` to the path to your beast2 directory. 

For example:
```
<property name="beast2path" location="/Users/username/Repos/beast2" />
```

Go to your beast2 directory and build beast2
```
cd Repos/beast2
ant build_all_BEAST
```

Then go to your project directory and build your package
```
cd ~/IdeaProjects/myBeastProject
ant addon
```

## Testing your package release
See instructions here https://www.beast2.org/managing-packages/

## Adding your package to BEAST package manager (CBAN)
Your package needs to be added to [CBAN](https://github.com/CompEvol/CBAN) to be released to the public. 

Packages that have not yet undergone peer-review should go into CBAN's `packages-extra.xml`.

Packages that have been peer-reviewed should go in the xml for the corresponding beast version, e.g., `packages2.6.xml`.

## Running your package on a server
There are two methods to run your package on the server: 
* Installing beast2 and adding your package (via command line or manually), see https://www.beast2.org/managing-packages/
* Creating an artifact jar (aka fat jar)
...

## Useful resources:
* Model validation and general tips
https://github.com/rbouckaert/DeveloperManual

* Beast package writing tutorial using Eclipse IDE
http://www.beast2.org/writing-a-beast-2-package/

* Installing and managing packages
https://www.beast2.org/managing-packages/

* Building a package from source
https://www.beast2.org/2021/06/21/building-from-source.html
