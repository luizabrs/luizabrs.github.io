---
layout: post
title: Understanding the Debian packaging system (or at least trying to) [Part 2]
date: 2024-06-04 07:14:00
description: This post is about the debian package system and my experience trying to contribute to the project.
tags:  MAC0470 FLOSS
disqus_comments: true
related_posts: false
---
This is another post of the series of contributions and experiences through the discipline of MAC0470 (Free Software Development).

Now that we have chosen the package that we are contributing, lets move to:

## First base changes:

On the development of changing the `Hash::Wrap` package we had to check the files inside `debian/`:

- copyright
- control
- changelog

### `debian/copyright`:

To check for the copyrights of the packages, use the following commands:

```sh
$ grep -i -r --exclude-dir=.git --exclude-dir=debian copyright
$ licensecheck --shortname-scheme=debian,spdx --copyright --recursive .
$ scan-copyrights
```
These commands will help you locate the copyright information without manually checking all the files in the repository. After running these commands, we discovered that the copyright information was incomplete, and some reorganization of the files is necessary.

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/debian_copyright.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>

Also, can remove the DISCLAIMER message, then commit these changes and you are good to go.

### `debian/control`:

At the time this blog was produced, the policy for Standards-Version in the `control` file should be 4.7.0, and the version for `debhelper-compat` should be 13.
`
However, these changes were not necessary because the base configuration from `dh-make-perl` did not alter these settings. Keep in mind that you might need to update them for other packages.

You can check the versions of `Standards-Version` and `debhelper-compat` with the following commands:

```sh
Copy code
$ dpkg-query --show --showformat '${Version}\n' debian-policy
$ rmadison --suite=unstable debhelper | cut -d"|" -f 2 | sed 's/\s\+//'
```

Often, you will need to update the *Description* because `dh-make-perl` uses the upstream description, which is usually not adequate. It is likely you will need to change it to something more meaningful.

The final result of our changes was this:

<div class="row mt-3">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid path="assets/img/debian_control.png" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>


Verify if everything is correct with this file using:

```sh
$ cme check dpkg-control debian/control
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
```
If no further changes are needed, you can commit the file and proceed with the following steps.

### `debian/changelog`:

Before modifying this file, we need to ensure our package is correctly set up. Build it and run `lintian` to check for any issues.

We encountered an issue where some environment PERL variables were incorrectly configured, causing package installation paths to point to directories within the user's `/home`, which is not allowed for Debian packages.

To fix this, start a clean environment using `env -i` and run the following commands:

```sh
$ env -i sudo pbuilder create
$ env -i sudo pbuilder update
```

These commands will build and update the package. Next, ensure all dependencies are installed by running:

```sh
$ env -i BUILDER=pbuilder git-pbuilder
```

Then, check the package with `lintian`:

```sh
$ lintian -I --profile pkg-perl --info
```

For this package, we received a warning: `initial-upload-closes-no-bugs`. We'll fix this by opening an ITP report and updating our changelog file.

Now, run development tests to ensure our package passes:

```sh
$ sudo apt-get install ubuntu-dev-tools # Contains dev tests
$ sudo adduser <username> sbuild        # Allows you to run without root
$ mk-sbuild sid                         # Creates a chroot i.e. sid-amd64
$ autopkgtest . -- schroot sid-amd64    # Builds and tests with the chroot created
```

Check the last lines for confirmation:

```sh
All tests successful.
...
autodep8-perl-recommends PASS (superficial)
```

This indicates our package is ready for upload. However, we need to open an ITP report and update our changelog first.

To open an ITP report, send an email to `submit@bugs.debian.org`. Generate the subject and content by running:

```sh
$ dpt gen-itp > /tmp/mail
$ cat /tmp/mail
```
You can use any e-mail to send this. 

Once the buf report is created we are good to follow. Our bug report can be found [here](https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1071516).

With this task done, we can update our `debian/changelog` file:

```sh
$ dch --closes 1071516 # Updates changelog message to close this bug
$ dch --release        # Switch 'unreleased' to 'unstable' status
```

We can now commit and move on.


## Preparations to upload the package

To move on, first, create a Salsa account. A moderator will review your registration and accept it. This process can take a few hours or even days.

Than, it is necessary to associate an SSH Key with Your Salsa Profile. Once your account is accepted, you need to associate an SSH key with your profile on Salsa Gitlab. The SSH key must be configured on the machine you're working with, which is the Virtual Machine (VM) for this package.

### Steps to Set Up an SSH Key in the Debian VM

1. **Generate the SSH Key Using the ed25519 Algorithm**:
    ```sh
    $ ssh-keygen -t ed25519 -C "your_email@example.com"
    ```

2. **Start the ssh-agent in the Background**:
    ```sh
    $ eval "$(ssh-agent -s)"
    ```

3. **Add Your Private Key to the Agent**:
    ```sh
    $ ssh-add ~/.ssh/id_ed25519
    ```

4. **Copy the Public Key to Salsa Gitlab**:
    - Run the following command to get your public key:
      ```sh
      $ cat ~/.ssh/id_ed25519.pub
      ```
    - Copy and paste the content of your public key into Salsa Gitlab when associating a new SSH key to your profile. Ensure you use the public key (`id_ed25519.pub`), not the private one.

After completing these steps, you can upload packages to Salsa Gitlab without any issues or additional authentication.

## Uploading the package to Debian Perl’s team repository (incomplete)

First, we need to join the Perl Team. To do this, send them an email with a brief introduction and your Salsa username. You can refer to the email I sent [here](https://lists.debian.org/debian-perl/2024/06/msg00000.html).

However, we encountered difficulties in reaching the Perl Team, as they did not respond to any emails from our MAC0470 group.

We have uploaded our progress with this package on Salsa Gitlab: [Hash Wrap on Salsa](https://salsa.debian.org/lincolnyuji/hash-wrap-package).

We will contact Joenio, who authored the tutorials we used, to help finalize the job and upload the package to the Perl Team.
