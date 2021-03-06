# dpkg_dep

Abstract
========

This is a module for ansible that can be used to create meta
packages that only serve to maintain dependencies.

Rationale
=========

The idea behind the package is, that when you install some non-packaged
software on a machine, you want to ensure, that the packages it depends
on are kept there or, if they get removed or changed during an upgrade
you get a warning ("broken package") from the package manager.

The way to achieve this is by using empty packages, that only contain
a control file, that declares all the dependencies of the installed
software.

On plain Debian, you can achieve the same effect by using the
[equivs](http://packages.debian.org/equivs).

This module implements the same idea for ansible.

Installation
============

If you don't have one yet, create a directory named 'library' at the base
of your ansible tree. By default ansible will look there for custom or local
modules.

    cd $ANSIBLE && mkdir library

Now copy the dpkg_dep module into the library:

    cd library && wget https://github.com/tpo/dpkg_dep/raw/master/dpkg_dep    

Usage
=====

    - name: create foo dependencies
      dpkg_dep: name=foo-dependencies version=0.1 depends="dpkg (>=1.17.23)"
      register: foo

    - name: install foo dependencies
      apt: deb=/var/cache/apt/archives/foo-dependencies_0.1_all.deb
      when: not foo.check_mode and foo.changed

Help
====

<pre>
module: dpkg_dep
author: Tomas Pospisek
short_description: Create a meta package that has dependencies on given packages
description:
    - This module can be used to maintain dependencies for installed software. It creates
      a meta package with given parameters in /var/cache/apt/archives on the remote
      machine, that can be installed with the apt module.
      When run in 'check mode', the result's 'check_mode' property will be set
      to 'True'. This property can then be used to skip the following 'apt' command. See
      the examples section.
version_added: "1.8"
options:
    name:
        description:
            - Name of the package. It is suggested to call the package something like
              "mysoftware-dependencies"
        required: true
    version:
        description:
            - version of the dependencies package
        required: true
    depends:
        description:
            - what you would put into the 'Depends:' field in a Debian package 'control' file
        required: true
</pre>

Author
======
Tomáš Posíšek <tpo_deb@sourcepole.ch> for http://sourcepole.ch
