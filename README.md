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

Only `jdk_version` is required. `java_version`, `java_subversion` and
`jdk_file_name` are auto-derived from it (see defaults below) and only need to be
set explicitly to override the default — e.g. for JDK 8's differently-formatted
version string, or a non-standard file name.

```yaml
- hosts: all

  roles:
    - wcm_io_devops.oracle-java

  vars:
    - jdk_version: "21.0.6"
```

- `jdk_version`: the JDK version to install; used for install paths/symlinks (e.g.
  `/usr/java/jdk-21.0.6`) and to derive the defaults below.
- `java_version` (default: the major segment of `jdk_version`, e.g. `21`): used for
  version-specific logic (JDK 8 installs differently than JDK 9+).
- `java_subversion` (default: everything after the first `.` in `jdk_version`, e.g.
  `0.6`): only used on macOS dmg installs.
- `jdk_file_name` (default: `"jdk-{{ jdk_version }}_{{ jdk_os }}-{{ jdk_arch }}_bin"`,
  Oracle's standard JDK9+ naming): the file name (without extension) to copy from
  `files/`. The extension (`.tar.gz`, `.rpm`, or `.dmg`) is chosen automatically based
  on target OS and package manager (`jdk_os` and `jdk_arch` are role-internal facts).
- `jdk_version_detail`: not used by the role itself — only needed if you reference it
  in your own `jdk_file_name` override.

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

### Install manually downloaded JDK (9+, standard Oracle naming)

```yaml
- hosts: all

  roles:
    - wcm_io_devops.oracle-java

  vars:
    - jdk_version: "21.0.6"
```

### Override for JDK 8 / non-standard file naming

```yaml
- hosts: all

  roles:
    - wcm_io_devops.oracle-java

  vars:
    - jdk_version: "1.8.0_201"
    - java_version: 8
    - java_subversion: 201
    - jdk_file_name: "jdk-8u201-{{ jdk_os }}-{{ jdk_arch }}"
```

### If running from the command line

```bash
ansible-playbook --ask-become-pass playbook.yml
```

## License

Licensed under the Apache License V2.0. See the [LICENSE file](LICENSE) for details.
