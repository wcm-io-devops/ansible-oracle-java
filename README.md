# srsp.oracle-java for Ansible Galaxy

[![Build Status](https://travis-ci.org/srsp/ansible-oracle-java.svg?branch=master)](https://travis-ci.org/srsp/ansible-oracle-java)

## Summary

⚠️ This is project is unmaintained, because now there are many great alternatives to Oracles JDK. Also Oracle made it really hard to download its JDK. 

Role name in Ansible Galaxy: **[srsp.oracle-java](https://galaxy.ansible.com/srsp/oracle-java/)**

This Ansible role has the following features related to the Oracle JDK:

 - Install the Oracle JDK in versions 8 to 13.
 - Install the optional Java Cryptography Extensions (JCE). [Only needed for any JDK version <= 8u152.](https://bugs.java.com/view_bug.do?bug_id=JDK-8170157)
 - Install for CentOS, Debian/Ubuntu, SUSE, and macOS operating systems.

This role is based on [williamyeh.oracle-java](https://github.com/William-Yeh/ansible-oracle-java), but I wanted more recent Java versions and decided to drop support for older versions.

If you prefer OpenJDK, try [geerlingguy.java](https://galaxy.ansible.com/geerlingguy/java/).

## Automatic download from Oracle
**Attention:** Back in the days this role would download all the JDKs directly from the Oracle site. Oracle cut this possibility step by step. Now you cannot download any version of JDK 8 anymore without Oracle login. For JDK 11 you can only download one older version. JDK 13 can be downloaded at the moment.

Since this changes non-deterministically every time Oracle releases a new Java version, you should not rely on it. I recommend downloading the JDK manually and installing it by this role.

## Role Variables

### Basic usage

This will try to download the JDK from Oracle and install it.

```yaml
- hosts: all

  roles:
    - srsp.oracle-java

  vars:
    # You should set this:
    - java_version: 13
    # You can omit this (role will then use the latest version it knows):
    - java_subversion: 0.2
```


## Examples

### Install manually downloaded JDK

Put the downloaded JDK file for your intended system in the `files` directory and set `java_download_from` to `local`:

```yaml

- hosts: all

  roles:
    - srsp.oracle-java

  vars:
    - java_version: 8
    - java_subversion: 201
    - java_download_from: local
```

### Install from your mirror

```yaml

- hosts: all

  roles:
    - srsp.oracle-java

  vars:
    - java_version: 8
    - java_subversion: 172
    - java_download_from: mirror
    - java_mirror: "http://some.url/in/your/network/jdk-8u172-macosx-x64.dmg"
```

### Download JDK to local machine (prefetch)

If you just want the JDK on your local machine (e.g. in order to use the files for the
molecule test or to upload it to your corporate intranet mirror), you can use the
`prefetch.yml` playbook in this role:

```bash
ansible-playbook prefetch.yml
```

Change the playbook according to your needs.

### If running from the command line

```bash
ansible-playbook --ask-become-pass playbook.yml
```

### Optional variables

User-configurable defaults:

```yaml
# Java Version
java_version: 8

# Java Subversion
java_subversion: 201

# Whether to download Java from from Oracle directly
# - oracle: Download from Oracle website on-the-fly.
# - mirror: Download from the URL defined in 'java_mirror'.
# - local: Copies from `files` directory of the role or the playbook on the control machine.
java_download_from: oracle

# Depending on the value of 'java_download_from' different things happen here:
# - oracle: You don't need to set it. It is prefilled with the Oracle download mirror.
# - mirror: You need to set it the mirror you want to download from. You need to set the complete URL including the file, like in the example below. If you also want the JCE, you need to set 'jce_zip_url' as well.
# - local: 'java_mirror' is not used, therefore the value is ignored.
#java_mirror: "https://private-repo.com/java/jdk-8u172-macosx-x64.dmg"
java_mirror: "http://download.oracle.com/otn-pub/java"

# Remove temporary downloaded files?
java_remove_download: true

# Set $JAVA_HOME?
java_set_java_home: true

# Install JCE?
java_install_jce: false
```

For other configurable options, read `tasks/set-role-variables.yml` file; for example, to see supported `java_version`/`java_subversion` combinations.

### I want to install a JDK which you don't yet support!

No problem! You have to specify the corresponding Java build number in the variables `java_build` and `jdk_tarball_hash` in addition to `java_version` and `java_subversion`, for example:

```yaml
# file: playbook.yml
- hosts: all

  roles:
    - srsp.oracle-java

  vars:
    - java_version: 8
    - java_subversion: 141
    - java_build: 15
    - jdk_tarball_hash: 336fa29ff2bb4ef291e347e091f7f4a7
```

## License

Licensed under the Apache License V2.0. See the [LICENSE file](LICENSE) for details.

## Development

### Testing

The tests are using `molecule`. Since this role needs a JDK from Oracle, molecule test
containers will mount `/tmp/java`, so the JDK is persisted on the local machine and still
available, when the test container has been destroyed. You can also prefetch the installation file
and put it to `/tmp/java`.

 This will execute the whole test cycle against the default scenario:

```bash
molecule test
```

If you want to work on a certain distribution, use something like this:

```bash
molecule converge --scenario-name opensuse15
```

If you want to test everything, issue:

```bash
molecule test --all
```

This will take a while.
