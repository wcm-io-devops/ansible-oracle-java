# wcm_io_devops.oracle-java

## Summary

This is a [wcm-io-devops](https://github.com/wcm-io-devops) fork of the (unmaintained) upstream `srsp.oracle-java` role, kept up because Oracle made it really hard to deterministically download its JDK.

Role name: **`wcm_io_devops.oracle-java`** (installed via this repository's git URL in `requirements.yml`; not published on Ansible Galaxy).

This Ansible role has the following features related to the Oracle JDK:

 - Install a locally-provided Oracle JDK archive/package, for any Java version from 11 onwards (earlier versions are EOL and rejected).
 - Install for CentOS, Debian/Ubuntu, SUSE, and macOS operating systems.

This role is based on [williamyeh.oracle-java](https://github.com/William-Yeh/ansible-oracle-java), but I wanted more recent Java versions and decided to drop support for older versions.

If you prefer OpenJDK, try [geerlingguy.java](https://galaxy.ansible.com/geerlingguy/java/).

## Local-only install

**Attention:** Oracle no longer allows deterministic on-the-fly downloads of the JDK, so this role only supports installing a JDK archive/package you already have. Put the downloaded JDK file for your intended system in the `files` directory of the role (or the playbook on the control machine) and provide all the details below — the role does not know about any specific Java version or download location on its own.

## Role Variables

### Required variables

Only `jdk_version` is required. `java_version`, `java_subversion` and
`jdk_file_name` are auto-derived from it (see defaults below) and only need to
be set explicitly to override the default — e.g. for a non-standard file name.
Only Java 11 and above are supported; earlier versions are EOL and rejected
with an error.

```yaml
- hosts: all

  roles:
    - wcm_io_devops.oracle-java

  vars:
    jdk_version: "21.0.6"
```

- `jdk_version`: the JDK version to install; used for install paths/symlinks (e.g.
  `/usr/java/jdk-21.0.6`) and to derive the defaults below.
- `java_version` (default: the major segment of `jdk_version`, e.g. `21`): used for
  version-specific logic (e.g. the macOS pkg naming and dmg volume differ across
  releases).
- `java_subversion` (default: everything after the first `.` in `jdk_version`, e.g.
  `0.6`): only used by the macOS installer naming logic. For Java 11/12 on
  macOS, set this explicitly to match the update segment used by your
  downloaded dmg/pkg; the generic default is not sufficient for those
  installers.
- `jdk_file_name` (default: `"jdk-{{ jdk_version }}_{{ jdk_os }}-{{ jdk_arch }}_bin"`,
  Oracle's standard JDK9+ naming): the file name (without extension) to copy from
  `files/`. On macOS, `jdk_os` resolves to `macosx` for Java 11/12 installers and
  `osx` for Java 13+ installers. The extension (`.tar.gz`, `.rpm`, or `.dmg`) is
  chosen automatically based on target OS and package manager (`jdk_os` and
  `jdk_arch` are role-internal facts). `jdk_arch` is only auto-mapped for
  `i386`/`x86_64`; on other architectures (e.g. ARM64) the role fails fast and
  requires `jdk_file_name` to be set explicitly.
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

### Install manually downloaded JDK (standard Oracle naming)

```yaml
- hosts: all

  roles:
    - wcm_io_devops.oracle-java

  vars:
    jdk_version: "21.0.6"
```

### Override jdk_file_name (e.g. custom/internal mirror naming)

```yaml
- hosts: all

  roles:
    - wcm_io_devops.oracle-java

  vars:
    jdk_version: "17.0.9"
    jdk_file_name: "openjdk-17.0.9_{{ jdk_os }}-{{ jdk_arch }}_bin"
```

### If running from the command line

```bash
ansible-playbook --ask-become-pass playbook.yml
```

## License

Licensed under the Apache License V2.0. See the [LICENSE file](LICENSE) for details.
