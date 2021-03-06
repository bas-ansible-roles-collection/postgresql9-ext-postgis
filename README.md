# PostgreSQL 9 Extension PostGIS (postgresql9-ext-postgis)
 
Master: [![Build Status](https://semaphoreci.com/api/v1/bas-ansible-roles-collection/postgresql9-ext-postgis/branches/master/badge.svg)](https://semaphoreci.com/bas-ansible-roles-collection/postgresql9-ext-postgis)
Develop: [![Build Status](https://semaphoreci.com/api/v1/bas-ansible-roles-collection/postgresql9-ext-postgis/branches/develop/badge.svg)](https://semaphoreci.com/bas-ansible-roles-collection/postgresql9-ext-postgis)
 
PostGIS extension for PostgreSQL to enable geospatial functions
 
**Part of the BAS Ansible Role Collection (BARC)**
 
**This role uses version 0.1.0 of the https://github.com/bas-ansible-roles-collection/postgresql9-ext-postgis.git flavour of the BAS Base Project - Pristine**.
 
## Overview
 
* Installs PostGIS extension system package for use within PostgreSQL databases
 
## Quality Assurance
 
This role uses manual and automated testing to ensure its features work as advertised. See [here](tests/README.md) for more information.
 
## Dependencies
 
* [**bas-ansible-roles-collection.postgresql9-server**](https://galaxy.ansible.com/bas-ansible-roles-collection/postgresql9-server/)
  * Minimum version: *0.1.0*
 
More information on role dependencies is available in the 
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-dependencies)
 
## Requirements
 
* None
 
More information on role requirements is available in the 
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-requirements)
 
## Limitations
 
* The PostGIS version varies depending on which operating system is used and if non-system package sources are permitted

As the package policy varies between system and non-system package sources, and between operating systems, the version
of PostGIS installed is variable between supported operating systems.

For Ubuntu and CentOS machines, a non-system APT package is installed resulting in more recent versions of PostGIS to 
be installed.

It is a convention of BARC roles to use the latest version of packages. Where a suitable non-system package source is
available it will be used. Otherwise system packages will be used. Suitable non-system packages require a reputable,
maintainer, typically a company or well respected individual. Where non-system packages are used, the variable
*BARC_use_non_system_package_sources* can be set to `false` to always use system packages if this is needed.

*This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued.*
*Pull requests addressing this limitation will be considered.*

See [BARC-116](https://jira.ceh.ac.uk/browse/BARC-116) for further details.
 
More information on role limitations is available in the 
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-limitations)
 
## Usage
 
### BARC Manifest
 
By default, BARC roles will record that they have been applied to a system, this is termed the BAS Manifest.
The specific local facts set for this role are:
 
* `ansible_local.barc_postgresql9_ext_postgis.general.role_applied` - (boolean) records whether a role has been applied
* `ansible_local.barc_postgresql9_ext_postgis.general.role_version` - (string) records the version of the role that was applied
 
Note: You **SHOULD** use this feature to determine whether this role has been applied to a system.
If you do not want these facts to be set by this role, you **MUST** skip the **BARC_SET_MANIFEST** tag.
 
More information is available in the 
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-Manifest)
 
### PostGIS version

Depending on the operating system used, the version of PostGIS installed will differ, though it will be at least
PostGIS *2.0*, and not greater than the last PostGIS 2 series release. The table below hopes to clarify the
version you can expect:

| Operating System | Non-System Package Sources Permitted | PostGIS version | Notes |
| ---------------- | ------------------------------------ | --------------- | ----- |
| Ubuntu           | Yes                                  | *2.2*           | -     |
| Ubuntu           | No                                   | *2.1*           | -     |
| CentOS           | Yes                                  | *2.2*           | -     |
| CentOS           | No                                   | *2.0*           | -     |

Because the exact version installed cannot be guaranteed by this role, you should be careful if using depending on this
role in another role or a project that relies on PostGIS. If any version of the client greater than 2.0 and less
than 3.0, is acceptable this role is suitable, however where you depend on some feature added to minor releases (e.g.
*2.2*) this role is unsuitable.

This ambiguity is considered a limitation, see the *limitations* section for more information.

### Enabling PostgreSQL extensions

Extensions are enabled on a per-database basis in PostgreSQL. As the *postgresql9-server* role, on
which this role depends, does not support creating databases, tasks to enable this extension will need to be made
outside of this role, using a task like the following:

```yaml
---
- name: Enable postgis extension within app database 
  postgresql_ext:
    name=postgis
      db=app
  become_user: postgres
```

Note: This assumes a database 'app' has previously been created

Note: Extensions, **SHOULD NOT** be enabled within the default *postgres* database.

### Typical playbook
 
```yaml
---
 
- name: ...
  hosts: all
  become: yes
  vars: []
  roles:
    - bas-ansible-roles-collection.postgresql9-ext-postgis
  tasks:
    - name: create a non-privileged postgresql role for an application
      postgresql_user:
        name="app"
        password="password"
        state=present
      become_user: postgres
    - name: create database for an application
      postgresql_db:
        name="app"
        owner="app"
        state=present
      become_user: postgres
    - name: Enable postgis extension within app database 
      postgresql_ext:
        name="postgis"
        db="app"
      become_user: postgres
```
 
### Tags
 
BARC roles use standardised tags to control which aspects of an environment are changed by roles.
Tasks in this role will 'tag' themselves with these tags as appropriate, typically in `tasks/main.yml`.
 
More information is available in the
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Appendix-B---BARC-Standardised)
 
### Variables
 
#### *postgresql9_ext_postgis_barc_role_name*
 
* **MUST NOT** be specified
* Specifies the name of this role within the BAS Ansible Roles Collection (BARC) used for setting local facts
* See the *BARC roles manifest* section for more information
* Example: `postgresql9_ext_postgis` 
 
#### *postgresql9_ext_postgis_barc_role_version*
 
* **MUST NOT** be specified
* Specifies the name of this role within the BAS Ansible Roles Collection (BARC) used for setting local facts
* See the *BARC roles manifest* section for more information
* Example: `2.0.0` 
 
### *BARC_use_non_system_package_sources*
 
* **MAY** be specified
* Specifies whether non-system sources can be used to install packages
* Note: This variable is scoped to all other BARC roles which install packages from non-system sources
* Values MUST use one of these options, as determined by Ansible:
    * `true` 
    * `false` 
* Values **SHOULD NOT** be quoted to prevent Ansible coercing values to a string
* Where not specified, a value of true will be assumed
* Default: `true` 
 
## Developing
 
### Issue tracking
 
Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the 
[BAS Ansible Roles Collection](
https://jira.ceh.ac.uk/projects/BARC) (BARC) project on Jira.
 
This service is currently only available to BAS or NERC staff, although external collaborators can be added on request.
See our contributing policy for more information.
 
More information is also available in the
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Issue-Tracking)
 
### Source code
 
All changes should be committed, via pull request, to the canonical repository:
 
`
ssh://git@stash.ceh.ac.uk:7999/barc/ROLE-NAME.git` 
 
A read-only mirror of this repository is maintained on GitHub:
 
`git@github.com:bas-ansible-roles-collection/ROLE-NAME.git`
 
More information is available in the
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Source-Code)
 
### Contributing policy
 
This project welcomes contributions, see `CONTRIBUTING.md` for our general policy.
 
### Release procedure
 
The general release procedure for BARC roles is available in the
[BARC General Documentation](
https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Release-procedures)
 
## Licence
 
Copyright 2016 NERC BAS.
 
Unless stated otherwise, all documentation is licensed under the Open Government License - version 3.
All code is licensed under the MIT license.
 
Copies of these licenses are included within this project.
