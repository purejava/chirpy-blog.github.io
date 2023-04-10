---
title: jextract on libappindicator
date: 2023-04-10 12:56:00 +/-0000
categories: [Java, FFI]
tags: [cli]     # TAG names should always be lowercase
toc: false
---
# Introduction
I was always fascinated by the possibility to use a library written in C from Java. The functionality of the library is available to your Java code then, without the need to re-implement the wheel.

With [Project Panama](https://openjdk.org/projects/panama/) and the `jextract` tool you can generate Java bindings directly from the header files of the C library of interest.

Let's see this in practice.

# Configuring jextract

`jextract` was developed with, but later excluded from Project Panama, so you need to acquire `jextract` and add it to your JDK.

`jextract` is available as a pre-build binary from the [official website](https://jdk.java.net/jextract/). Download the version that matches your JDK version.

The binaries need to be added to your path:
```shell
export PATH=$HOME/Downloads/openjdk-20-jextract+1-2_linux-x64_bin/jextract-20/bin:$PATH
```

And the executables need to be marked executable:
```shell
cd $HOME/Downloads/openjdk-20-jextract+1-2_linux-x64_bin/jextract-20/bin
chmod 0755 *
```

# Generate Java bindings for libappindicator

Generating the Java bindings for **libappindicator** is straight forward. As it depends on a couple of shared libraries, it's necessary to reference all of them. Otherwise, the command will fail:

```shell
jextract --source \
	-t org.purejava.linux \
	-I /usr/include/gtk-3.0/ \
	-I /usr/include/glib-2.0/ \
	-I /usr/include/cairo/ \
	-I /usr/include/gdk-pixbuf-2.0/ \
	-I /usr/include/pango-1.0/ \
	-I /usr/include/atk-1.0/ \
	-I /usr/include/harfbuzz/ \
	-I /usr/lib/glib-2.0/include/ \
	/usr/include/libappindicator3-0.1/libappindicator/app-indicator.h
```