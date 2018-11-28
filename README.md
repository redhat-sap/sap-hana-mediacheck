sap-hana-mediacheck
===================

SAP HANA is delivered and provided in various ways. The sap-hana-mediacheck role is used to verify that the installation media is valid. It checks the version that is to be installed and sets the following variables for further use:

 - `sap_hana_version` the major Release of HANA (e.g. 1 or 2)
 - `sap_hana_minor_version` typically 00
 - `sap_hana_sps` the Service Pack release (e.g. 03)

These variables can be used in the `sap-hana-preconfigure` role, so that they are not required to be set manually.

Role Variables
--------------

### Mandatory Parameters

```yaml
sap_hana_installdir: path/to/directory/that/contains/hdbclcm
```

This is the installation directory from where we want to install HANA. In the basic installation bundle the path looks like this:  `/hanainstalldir/DATA_UNITS/HDB_SERVER_LINUX_[X86_64|PPC64LE]`

For a Update Package this looks like: `TBD?`

With this role you have the option to provide the installation directory to the server when it is provided via NFS. If you provide it differently, e.g via automout setup setting this single parameter is sufficient.

### OPTIONAL Parameters

####  HANA Install Tree (unpacked) provided on NFS share
 If you have an NFS share that provides the SAP HANA installation binaries, use the following variables to mount it for the installation.

NOTE: You have to unmount it after installing HANA, or the mount stays until a reboot. It is not added to `/etc/fstab`.

```yaml
sap_hana_mediacheck_serverpath: <nfs-server>:<export-dir>
sap_hana_mediacheck_mountpoint: <local-mountpoint>
sap_hana_mediacheck_nfs_opts: <NFS Mount Options like -o version=3>
```

#### HANA Install Archive provided on an NFS share

Use the following set of variables if you provide a HANA install archive on an NFS-Server. It is then unpacked locally on the client.

SAP versions come in at least three diffrent package formats: RAR, SAPCAR and tar. If you provide the downloaded archive files on an NFS share and you want to unpack the archive files locally  define the following variables:

```yaml
# NFS Share that holds the archive files
sap_hana_mediacheck_serverpath: "<ip-address>:/export/directory"

# NFS Mount Options, eg. "-o version=3"
sap_hana_mediacheck_nfs_opts:

# Relative path to the archive that holds the SAP software
sap_hana_mediacheck_archive: path/to/archive

# Directory where to unpack the Hana install binaries
sap_hana_mediacheck_install: /installdir

# Command to unpack the hana binaries, e.g. "unrar x", "SAPCAR xvf" or "tar xvf"
sap_hana_mediacheck_unarchive_cmd: "unrar x"

#  Set this to an RPM if you need to install an unarchiver package, e.g. unrar (see below)
# you can leave this blank if you have the unarchive command already installed)
sap_hana_mediacheck_unarchive_pkg:
```

NOTE: Red Hat cannot provide an unrar command, due to license restrictions, so that you need to provide it externally.

Here are some examples where to get working unrar packages:

- `http://apt.sw.be/redhat/el6/en/x86_64/rpmforge/RPMS/unrar-4.2.3-1.el6.rf.x86_64.rpm` for RHEL 6
- `https://dl.fedoraproject.org/pub/epel/7Server/x86_64/Packages/u/unar-1.10.1-1.el7.x86_64.rpm` for RHEL 7
- `unar` if you have it in your package repository


Dependencies
------------

There are no know dependencies

Example Playbooks
-----------------

The following playbook uses an NFS mountpoint to provide the unpacked HANA installation directory

```yaml
---
- hosts: all

  vars:
          # SAP-Media Check (get unpacked HANA from Mountpoint)
          sap_hana_mediacheck_serverpath: "mynfssrv:/install"
          sap_hana_mediacheck_mountpoint: /install
          sap_hana_installdir: "{{ sap_hana_mediacheck_mountpoint + '/51052325_HANA2_20/DATA_UNITS/HDB_SERVER_LINUX_' + ansible_architecture|upper }}"

  roles:
    - sap-hana-mediacheck
```

The following playbook unpacks the HANA Express Package that resides on an NFS Share

```yaml
---
- hosts: all

  vars:
          # SAP-Media Check (unpack HXE from mountpoint)
          sap_hana_mediacheck_serverpath: "mynfssrv:/install"
          sap_hana_mediacheck_archive: "download/hxe.tgz"
          sap_hana_mediacheck_install: "/install"
          sap_hana_mediacheck_unarchive_cmd: "tar xzf"
          sap_hana_installdir: "{{ sap_hana_mediacheck_install + '/HANA_EXPRESS_20/DATA_UNITS/HDB_SERVER_LINUX_' + ansible_architecture|upper }}"

  roles:
    - sap-hana-mediacheck
```

The following playbook is used to just check the installation media which is provided by any other method, e.g. copied or via automount

```yaml
---
- hosts: all

  vars:
          # SAP-Media Check (already available)
          sap_hana_installdir: "{{ '/install/HANA_EXPRESS_20/DATA_UNITS/HDB_SERVER_LINUX_' + ansible_architecture|upper }}"

  roles:
    - sap-hana-mediacheck
```

Contribution
------------

Please read the [developer guidelines](./README.DEV.md)  if you want to contribute

License
-------

GNU GENERAL PUBLIC LICENSE
Version 3, 29 June 2007


Author Information
------------------

Markus Koch

Please leave comments in the github repo issue list
