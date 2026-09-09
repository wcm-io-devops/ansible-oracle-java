# srsp.oracle-java for Ansible Galaxy

[![Build Status](https://travis-ci.org/srsp/ansible-oracle-java.svg?branch=master)](https://travis-ci.org/srsp/ansible-oracle-java)

## Summary

⚠️ This is project is unmaintained, because now there are many great alternatives to Oracles JDK. Also Oracle made it really hard to download its JDK.

Role name in Ansible Galaxy: **[srsp.oracle-java](https://galaxy.ansible.com/srsp/oracle-java/)**

This Ansible role has the following features related to the Oracle JDK:

 - Install a locally-provided Oracle JDK archive/package, for any Java version.
 - Install for CentOS, Debian/Ubuntu, SUSE, and macOS operating systems.

This role is based on [williamyeh.oracle-java](https://github.com/William-Yeh/ansible-oracle-java), but I wanted more recent Java versions and decided to drop support for older versions.

If you prefer OpenJDK, try [geerlingguy.java](https://galaxy.ansible.com/geerlingguy/java/).

## Local-only install

**Attention:** Oracle no longer allows deterministic on-the-fly downloads of the JDK, so this role only supports installing a JDK archive/package you already have. Put the downloaded JDK file for your intended system in the `files` directory of the role (or the playbook on the control machine) and provide all the details below — the role does not know about any specific Java version or download location on its own.

## Role Variables

### Required variables

There are no defaults or known-version lookups. You must always provide:

```yaml
- hosts: all

  roles:
    - srsp.oracle-java

  vars:
    - java_version: 21
    - java_subversion: "0.6"
    - jdk_version: "21.0.6"
    - jdk_version_detail: "21.0.6"
    - jdk_file_name: "jdk-{{ jdk_version }}_{{ jdk_os }}-{{ jdk_arch }}_bin"
```

- `java_version` / `java_subversion`: the Java version being installed.
- `jdk_version`: used for install paths and symlinks (e.g. `/usr/java/jdk-21.0.6`).
  No longer derived by the role — set it explicitly.
- `jdk_version_detail`: the exact upstream version string, used only if you need it in
  your own `jdk_file_name` template.
- `jdk_file_name`: the file name (without extension) to copy from `files/`. The
  extension (`.tar.gz`, `.rpm`, or `.dmg`) is chosen automatically based on target OS
  and package manager (`jdk_os` and `jdk_arch` are role-internal facts you can
  reference, as shown above).

### Optional variables

```yaml
# Where to stage the copied/extracted JDK file on the target host
java_download_path: /tmp

# Remove temporary downloaded files?
java_remove_download: true

# Set $JAVA_HOME?
java_set_java_home: true
```

## Examples

### Install manually downloaded JDK

```yaml
- hosts: all

  roles:
    - srsp.oracle-java

  vars:
    - java_version: 8
    - java_subversion: 201
    - jdk_version: "1.8.0_201"
    - jdk_version_detail: "8u201-b09"
    - jdk_file_name: "jdk-8u201-{{ jdk_os }}-{{ jdk_arch }}"
```

### If running from the command line

```bash
ansible-playbook --ask-become-pass playbook.yml
```

## License

Licensed under the Apache License V2.0. See the [LICENSE file](LICENSE) for details.
