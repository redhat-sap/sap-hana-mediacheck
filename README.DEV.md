# Developer Guide Lines

## Directory Structure

```
sap-hana-mediacheck/
├── defaults
│   └── main.yml
├── example1.yml
├── example2.yml
├── files
├── handlers
│   └── main.yml
├── LICENSE
├── meta
│   └── main.yml
├── README.DEV.md
├── README.md
├── tasks
│   ├── configuration.yml
│   ├── installation.yml
│   ├── main.yml
│   ├── sap-mount-media.yml
│   └── sap-unpack-media.yml
├── templates
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    ├── main.yml
    └── RedHat.yml
```

The deployment of this role follows the style guide from Adfinis Sygroup. [Click Here](https://docs.adfinis-sygroup.ch/public/ansible-guide/styling_guide.html) for more info

## Where/How to make change/additions

- Define additional required packages in `vars/RedHat.yml` and make appropriate changes in `tasks/installation.yml` if required
- configuration.yml reads the HANA version from `"{{ sap_hana_installdir }}"/server/manifest`
- `sap-mount-media.yml` and `sap-unpack-medai.yml` provide the HANA installation binaries to `sap_hana_installdir`
- if you want to implement another method to provide the installation media define approriate variables and add a separate tasks file for it, e.g. delivering via http or the ansible-server itself. Include the tasks file at the beginning of configuration.yml, if your variables are set
