Ansible Atlassian Bambooagent Oracle Java role
==============================================

Installs an Oracle Java and certificates to its keystore on a remote machine and from a file installer.
This role can be used prior to deploying an agent running Atlassian Bamboo or any other Java service.
It is available on *Windows, OSX, Ubuntu*

On Linux, the
installed Java becomes the default Java and the Java *alternatives* are updated.

Requirements
------------

No system requirements.

Role Variables
--------------

| variable | default | meaning |
|----------|---------|---------|
|`java_version_major`| `1`| Java major version (the `1` in `1.8.64`)|
|`java_version_minor`| **required**| Java major version (the `8` in `1.8.64`)|
|`java_version_update`|**required**| The update version (the `64` in `1.8.64`)|
|`java_keystore_location`|`jre/lib/security/cacerts` | Indicates the path of the Java keystore relative to the `JAVA_HOME`|
|`certificate_files`|**required**| Certificate of the server running Bamboo server|
|`java_installation_path`|`/usr/local/oracle`| Java installation folder |
|`java_installation`|**required**| the definition of the Java installation (see below)|

**Note** on Linux the final `JAVA_HOME` is a folder inside `java_installation`.

### `java_installation` variable

The variable `java_installation` needs to be defined when running the role:

```
# Installs Java on the remotes, fixes the paths of the files on the fly
- role: atlassian-bambooagent-oracle-java-role
  java_installation: "{{ local_java_installation }}"
  certificate_files:
    - alias: "my-cert1"
      file: "ca-file.crt"
```

The definition is as follows:

On OSX:
```
local_java_installation:
  # the DMG containing the Java installer
  file: jdk-{{java_version_minor}}u{{java_version_update}}-macosx-x64.dmg

  # the install command of the DMG role
  install_cmd: 'installer -dumplog -pkg "${mount}/JDK {{java_version_minor}} Update {{java_version_update}}.pkg" -target /'

  remove_interactive: False
```

On Linux:
```
local_java_installation:
  # Java installer
  file: "/jdk-{{java_version_minor}}u{{java_version_update}}-windows-x64.exe"

  # Additional options (string) to pass to the installer
  install_options: ""
```

On Windows:

```
local_java_installation:
  # Archive containing the Java installer
  file: "jdk-{{java_version_minor}}u{{java_version_update}}-linux-x64.tar.gz"
```



### HTTPS certificate

The role also requires the variable `certificate_files` defined eg like this:

```
- certificate_files:
  - alias: "bamboo_server_certificate_alias"
    file: "{{some_root_location}}/bamboo_server_certificate.crt"
```

This installs a **list** of certificates into the remote Java. Each certificate is identified by its alias (as it appears
when certificates are listed from the keystore) and by their local file. This is intended to be used for installing the CA
from which the webserver in front of the Java service (eg. NGinx in front of Atlassian Bamboo) has a certificate.


Dependencies
------------

`atlassian-bambooagent-install-dmg`: for installing Java on OSX

License
-------

BSD

Author Information
------------------

Any comments on the Ansible, PR or bug reports are welcome from the corresponding Github project.
