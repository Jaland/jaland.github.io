---
layout: post
title:  "Creating and Using Maven Archetype"
tags: mvn archetype spring springboot
categories: mvn
---

# Creating a Maven Archetype from an existing project

Navigate to the directory of your project and use the command:
```aidl
mvn archetype:create-from-project
```

Archetype should be created inside of your `target/generated-sources/archetype/` folder


# Using an existing maven archetype

Run the following command in order to create artifact interactively:
```
mvn archetype:generate
```

Or include all the values directly in the cmd

```
mvn archetype:generate \
    -DarchetypeGroupId=org.example.archetype \
    -DarchetypeArtifactId=example-archetype \
    -DarchetypeVersion=1.0.0-SNAPSHOT \
    -Dpackage=org.jland.example \
    -DgroupId=org.jland.example \
    -DartifactId=example-project \
    -Dversion=0.0.1-SNAPSHOT \
    -DsomeCustomValue=customKey \
    -Dinteractive=false
```