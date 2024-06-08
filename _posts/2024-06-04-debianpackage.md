---
layout: post
title: Understanding the Debian packaging system (or at least trying to) [Part 1]
date: 2024-06-04 07:14:00
description: This post is about the debian package system and my experience trying to contribute to the project.
tags:  MAC0470 FLOSS
disqus_comments: true
related_posts: false
---
This is another post of the series of contributions and experiences through the discipline of MAC0470 (Free Software Development).

First things first, let's talk about Debian:

## What is `Debian`?
Debian is a free and open-source Linux distribution renowned for its stability, security, and extensive software repository. It is actively developed through a community-driven workflow, following many open-source principles. 

Debian's package management system is defined by `apt`, and it simplifies software installation and updates. Debian is the foundation for many other distributions, including Ubuntu, and it supports a wide range of hardware architectures. 

### Ok, but what is a Linux distribution?

A Linux distribution is an operating system built on the Linux kernel, combined with system software and libraries. Each distribution typically includes a package management system to handle software installation and updates. 

Distributions vary in focus, such as user-friendliness, security, or performance, catering to different user needs. Examples include Ubuntu, Fedora, Debian, Manjaro, Kali and each of them has unique features and tools. Linux distributions are widely used in servers, desktops, and embedded systems due to their flexibility and open-source nature.

### And what's the meaning of `apt`?

Debian `apt`, short for Advanced Package Tool is a package management system used by Debian and its derivatives to handle the installation, update, and removal of software packages. 

`apt` simplifies all the instalation tree by searching for dependencies and retrieving packages from repositories. It uses various command-line tools, such as `apt-get` and `apt`. Ah, and just for record a doubt that I had for a long time: `apt` is intended to simplify package management for users, while `apt-get` is more suited for scripting and detailed package management tasks, in other words, `apt` is a cuter version of `apt-get`.

## Contributing with packages for Debian

Alike many other open-source projects, cntributing with packages to Debian helps support the open-source community and enhances the software ecosystem. 

It involves understanding Debian's policies, setting up a packaging environment, and learning the packaging process. Contributors maintain and update software, ensuring its reliability and availability. 

This makes it easy to understand why contributing to the project is something significant. Debian and its derivatives, such as Ubuntu and Linux Mint, are widely used in commercial fields and backend servers, resulting in a significant role in our lives today, serving as the foundation for many systems.

To contribute, if you have software that you'd like to make more accessible to others, that's an excellent starting point. The next step involves learning how to create and publish this package so it can be downloaded via `apt`, which we should explore further.

### How to contribute?

For this project we will be using **Joenio Marques da Costa** tutorials (in portuguese):
*[Parte 1: Tutorial de empacotamento Debian](https://joenio.me/tutorial-pacote-debian-parte1/)
*[Parte 2: Criando o seu próprio pacote Debian](https://joenio.me/tutorial-pacote-debian-parte2/)

## Installing Debian Testing via Docker

The recommended version of Debian to work with packages is the Testing version. It has a nice balance between Unstable and Stable. 

Considering I was having QEMU issues, I prefered using the Docker alternative to proceed with this. I followed the tutorial given by the TA.

First, follow these command: 

```sh
mkdir project_name
cd project_name
sudo docker run --privileged --name debian2 -v .:/app:Z -it debian
```

Inside the container, the `/app` directory will be a direct link to the directory your terminal was in when you ran docker run.

The creation command mounts the project_name directory to the `/app`directory inside the container. To maintain your files, use the `/app`directory within the container.

To reconnect to the container if it is still running:

```sh
sudo docker exec -it debian2 /bin/bash
```

If it is not running but has not been deleted, start it with the following command (then use the previous command to connect):

```sh
sudo docker start debian2
```

And to stop:

```sh
sudo docker stop debian2
```

## Configuring a development environment for Debian Packages inside the docker

This part works exactly like **Joenio** tutorial suggests.

With Debian Testing running in a Docker container, we start by preparing the environment. First, ensure `ssh-server` is enabled and running in the container:

```sh
sudo apt update && sudo apt upgrade
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
```

To find the container's IP address, use `ip a` in the command line. Now, access the container via SSH:

```sh
ssh <username>@<container-IP-address>
```

Type `yes` in the terminal and press `Enter`.

Next, install useful development tools for packaging:

```sh
sudo apt install devscripts debhelper debian-policy git-buildpackage pkg-perl-tools
```
Also, install `apt-file` and update the database for `dh-make-perl` to find Debian packages referred by Perl libraries:

```sh
sudo apt install apt-file
sudo apt-file update
```

Now, configure your Git for meaningful commits:

```sh
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

Note that `dh-make-perl` relies on variables for `debian/` directory data:

```sh
export EMAIL=you@example.com
export DEBFULLNAME="Your Name"
```

You can add these lines to `~/.bash_profile` for automatic setup on system restarts.

If it's your first time using `CPAN`, run this command before `dh-make-perl`:

```sh
cpan
```

### A random question: why Perl?

Debian packages are not exclusively created using Perl, but Perl plays a significant role in the Debian packaging ecosystem due to several historical and practical reasons. 

Perl's strong text-processing capabilities make it ideal for tasks such as parsing configuration files, generating package metadata, and handling various text-based formats involved in packaging. 

Many essential Debian packaging tools, like `debconf` for handling configuration questions, `lintian` for checking common packaging errors, and `debhelper` for assisting with package building, are written in Perl. Note that this historical context and the comprehensive text-processing strengths of Perl have cemented its place in the Debian infrastructure.

Moreover, the Comprehensive Perl Archive Network, long for `cpan` has contributed significantly to Perl's prominence in Debian packaging. 

`cpan` is a vast repository of Perl modules, and tools like `dh-make-perl` facilitate the creation of Debian packages from these modules, streamlining the packaging process for Perl software.

Over time, a strong tradition of using Perl for Debian packaging tools has developed, resulting in a wealth of Perl expertise within the Debian community. This tradition, combined with the practical advantages Perl offers, has led to its widespread use and continued importance in the Debian packaging ecosystem.

**P.S.**

Here is the exact reason my root was full on the previous post. It was while I was wrongly (focus on wrongly) installing perl on my Ubuntu. I found out by experience that Perl is extremely heavy.

Perl is considered heavy due to its extensive standard library, legacy code for backward compatibility, and dynamic features that increase memory and CPU usage. 

Also, the interpreter's overhead and complex syntax also contribute to its perceived bloat.

## Choosing a library and moving on...

Out of a list of available libraries, we chose `Hash::Wrap`.

```sh
dh-make-perl locate Hash::Wrap
== dh-make-perl 0.124 ==
Parsing Contents files:
	ll.lz4
	md64.lz4
Hash::Wrap is not found in any Debian package
```

Here's where we start to branch off from the tutorial. Instead of using the `Hello World` library, which doesn't offer much practical use beyond teaching the structure of Debian packages, and well... It's `Hello World` we'll pick something more useful.

Make sure to choose a library that isn't already a Debian package. By the time you're reading this, the library mentioned might already be packaged, so you'll need to find another one to work with.

But don't worry, you can use any library you’re interested in to create Debian packages. Don't feel limited by the few options shown in the tutorial.

Next, we'll create the first version of our package. Run the following commands to download and install the code from the upstream source and set up some template files inside the `debian/` directory:

```sh 
mkdir hash_wrap_pack && cd hash_wrap_pack
dh-make-perl --pkg-perl --cpan Hash::Wrap
```

This created a new directory, along with a `tar` file, named `libhash-wrap-perl`. You can just `cd` into this directory and start working with the package.

The next steps for this can be found in part 2...