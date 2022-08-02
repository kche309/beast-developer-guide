# BEAST v2.6 development setup
This guide will go through how to set up BEAST v2.6.x development using IntelliJ.

## Tools you will need:
* [Git](https://git-scm.com/downloads) (Git Bash recommended for Windows)
* [IntelliJ IDE](https://www.jetbrains.com/idea/)
* Java JDK 8 install (aka Java 1.8) 
* [Apache Ant](https://ant.apache.org/manual/install.html) for building packages

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

## Using Ant to build your package
To build the package, we will need to create a build.xml in your project directory e.g., `~/IdeaProjects/myBeastProject`. 

We can use the `build.xml` file from BEASTLabs as a template for making the build file for our project.

Copy the BEASTLabs build file, for example

```
cp ~/Repos/BEASTLabs/build.xml ~/IdeaProjects/myBeastProject/build.xml
```

...

Go to your project directory
```
cd ~/IdeaProjects/myBeastProject
```
Then compile the package using ant
```
ant addon
```

## Useful resources:
Model validation and general tips
https://github.com/rbouckaert/DeveloperManual

Beast package writing tutorial using Eclipse IDE
http://www.beast2.org/writing-a-beast-2-package/
