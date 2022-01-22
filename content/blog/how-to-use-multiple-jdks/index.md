---
Title: How to use multiple Java Development Kit
description: Using sdkmain.io to alternate between different jdks versions 
published: true
comments: true
date: 2022-01-21
asciinema: true
tags:
  - jdk
  - java
---

{{< table_of_contents >}}

## Intro

SDKMAN! is a tool for managing parallel versions of multiple Software Development Kits on most Unix based systems. It allows you to download and easy switch between various versions of JDK.

## Getting Started

Installing sdkman

```shell
curl -s "https://get.sdkman.io" | bash
```

Installing the latest stable java jdk

```shell
sdk install java
```

List all available java jdk

```shell
sdk list java
```

the output will be similar to this

```txt
================================================================================
Available Java Versions for Linux 64bit
================================================================================
 Vendor        | Use | Version      | Dist    | Status     | Identifier
--------------------------------------------------------------------------------
 Corretto      |     | 17.0.2.8.1   | amzn    |            | 17.0.2.8.1-amzn
               |     | 17.0.1.12.1  | amzn    |            | 17.0.1.12.1-amzn
               |     | 17.0.0.35.1  | amzn    |            | 17.0.0.35.1-amzn
               |     | 11.0.14.9.1  | amzn    |            | 11.0.14.9.1-amzn
               |     | 11.0.13.8.1  | amzn    |            | 11.0.13.8.1-amzn
               |     | 11.0.12.7.1  | amzn    |            | 11.0.12.7.1-amzn
               |     | 8.322.06.2   | amzn    |            | 8.322.06.2-amzn
               |     | 8.312.07.1   | amzn    |            | 8.312.07.1-amzn
 Dragonwell    |     | 11.0.12.8    | albba   |            | 11.0.12.8-albba
               |     | 8.8.9        | albba   |            | 8.8.9-albba
 GraalVM       |     | 21.3.0.r17   | grl     |            | 21.3.0.r17-grl
               |     | 21.3.0.r11   | grl     |            | 21.3.0.r11-grl
               |     | 21.2.0.r16   | grl     |            | 21.2.0.r16-grl
               |     | 21.2.0.r11   | grl     |            | 21.2.0.r11-grl
               |     | 21.2.0.r8    | grl     |            | 21.2.0.r8-grl
               |     | 21.1.0.r8    | grl     |            | 21.1.0.r8-grl
               |     | 20.3.4.r11   | grl     |            | 20.3.4.r11-grl
               |     | 20.3.3.r11   | grl     |            | 20.3.3.r11-grl
               |     | 20.3.3.r8    | grl     |            | 20.3.3.r8-grl
               |     | 20.3.2.r8    | grl     |            | 20.3.2.r8-grl
               |     | 19.3.6.r11   | grl     |            | 19.3.6.r11-grl
               |     | 19.3.6.r8    | grl     |            | 19.3.6.r8-grl
 Java.net      |     | 19.ea.5      | open    |            | 19.ea.5-open
               |     | 19.ea.1.lm   | open    |            | 19.ea.1.lm-open
```

Or, you can use TAB to get a list of versions:

```shell
$ sdk install java 
11.0.10-open       11.0.14-sapmchn    17.0.2.fx-zulu     21.1.0.r8-grl      8.0.292-open
11.0.11-open       11.0.14-zulu       17.0.2-librca      21.1-nik           8.0.302-open
11.0.12.7.1-amzn   11.0.2-open        17.0.2-open        21.2.0.2-mandrel   8.0.312.fx-librca
11.0.12.8-albba    11.0.9-trava       17.0.2-oracle      21.2.0.r11-grl     8.0.312.fx-zulu
11.0.12-open       17.0.0.35.1-amzn   17.0.2-sapmchn     21.2.0.r16-grl     8.0.312-librca
11.0.13.8.1-amzn   17.0.1.12.1-amzn   17.0.2-zulu        21.2.0.r8-grl      8.0.312-sem
11.0.13.fx-librca  17.0.1.fx-librca   17.ea.3.pma-open   21.2-nik           8.0.312-tem
11.0.13.fx-zulu    17.0.1.fx-zulu     18.ea.31-open      21.3.0.0-mandrel   8.0.312-zulu
11.0.13-librca     17.0.1-librca      19.3.6.r11-grl     21.3.0.r11-grl     8.0.322.fx-librca
11.0.13-ms         17.0.1-ms          19.3.6.r8-grl      21.3.0.r11-nik     8.0.322.fx-zulu
11.0.13-sapmchn    17.0.1-open        19.ea.1.lm-open    21.3.0.r17-grl     8.0.322-librca
11.0.13-sem        17.0.1-oracle      19.ea.5-open       21.3.0.r17-nik     8.0.322-zulu
11.0.13-tem        17.0.1-sapmchn     20.3.2.r8-grl      6.0.119-zulu       8.312.07.1-amzn
11.0.13-zulu       17.0.1-sem         20.3.3.0-mandrel   7.0.322-zulu       8.322.06.2-amzn
11.0.14.9.1-amzn   17.0.1-tem         20.3.3.r11-grl     7.0.332-zulu       8.8.9-albba
11.0.14.fx-librca  17.0.1-zulu        20.3.3.r8-grl      8.0.232-trava      
11.0.14.fx-zulu    17.0.2.8.1-amzn    20.3.4.r11-grl     8.0.265-open       
11.0.14-librca     17.0.2.fx-librca   21.0.0.2-nik       8.0.282-open       
``` 

Installing a specific java version

```shell
sdk install java 17.0.2-oracle
```

To alter among java versions, run the following command: 
> Using TAB at the end, you will have a list of available versions

```shell
$ sdk use java
11.0.12-open   17.0.1-tem     17.0.2-oracle  8.0.302-open
$ sdk use java 8.0.302-open

Using java version 8.0.302-open in this shell.
```

Changing the default

```shell
$ sdk default java 11.0.12-open
```

## References

* https://stackoverflow.com/questions/69602928/what-should-i-set-java-home-to-using-multiple-jdks
* https://sdkman.io/usage
