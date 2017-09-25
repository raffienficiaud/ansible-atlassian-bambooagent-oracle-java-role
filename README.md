Ansible Atlassian Bambooagent Oracle Java role
==============================================

Installs an Oracle Java and certificates to its keystore, into a remove machine and from a file installer. 
This role can be used prior to deploying a agent running Atlassian Bamboo *(Windows, OSX, Ubuntu)*

On Linux, the 
installed Java becomes the default Java and the Java *alternatives* are updated.

Requirements
------------

No system requirements.

Role Variables
--------------

| variable | default | meaning |
|----------|---------|---------|
|java_version_major| **required**| Java major version (the 1 in 1.8.64)|
|java_version_update|**required**| The update version (the 64 in 1.8.64)|
|java_keystore_location|`jre/lib/security/cacerts` | Indicates the path of the Java keystore relative to the `JAVA_HOME`|
|certificate_files|**required**| Certificate of the server running Bamboo server|
|java_installation_path|`/usr/local/oracle`| Java installation folder |
|java_installation|**required**| the definition of the Java installation (see below)|

**Note** on Linux the final `JAVA_HOME` is a folder inside `java_installation`.
**TODO** it is actually `java_version_minor` that should be set instead of `java_version_major`.


### `java_installation` variable

The variable `java_installation` needs to be defined when running the role:

```
# Installs Java on the remotes, fixes the paths of the files on the fly
- role: bamboo-agent-oracle-java
  java_installation: "{{ bamboo_java_installation }}"
```

The definition is as follows:

On OSX:
```
bamboo_java_installation:
  # the DMG containing the Java installer
  file: jdk-{{java_version_major}}u{{java_version_update}}-macosx-x64.dmg

  # the install command of the DMG role
  install_cmd: 'installer -dumplog -pkg "${mount}/JDK {{java_version_major}} Update {{java_version_update}}.pkg" -target /'

  remove_interactive: False
```

On Linux:
```
bamboo_java_installation:
  # Archive containing the Java installer
  file: "jdk-{{java_version_major}}u{{java_version_update}}-linux-x64.tar.gz"

  # Folder inside the installer that is deflated under {{java_installation_path}}
  folder: "jdk1.{{java_version_major}}.0_{{java_version_update}}"
```

### HTTPS certificate

The role also requires the variable `certificate_files` defined eg like this:

```
- certificate_files:
  - alias: "bamboo_server_certificate_alias"
    file: "{{some_root_location}}/bamboo_server_certificate.crt"
```


Dependencies
------------

`bamboo-agent-install-dmg`: for installing Java on OSX



License
-------

BSD

Author Information
------------------

Any comments on the Ansible, PR or bug reports are welcome from the corresponding Github project.
