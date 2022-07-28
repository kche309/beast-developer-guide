# BEAST v2.6 development setup
This guide will go through how to set up BEAST v2.6.x development using IntelliJ.

## Tools you will need:
* Git (command line or GUI)
* IntelliJ IDE
* Java install (Java 1.8 for BEAST v2.6) 
* Ant for building packages

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
...

## Using Ant to build your package
...

## Useful resources:
Model validation and general tips
https://github.com/rbouckaert/DeveloperManual

Beast package writing tutorial using Eclipse IDE
http://www.beast2.org/writing-a-beast-2-package/
