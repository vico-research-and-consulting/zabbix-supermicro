zabbix-supermicro
=================

This project enhances zabbix with a externalscript which provides the following functionality:

- monitor general operation status (power, disk, cooling outages)
- monitor power usage
- discover fans, dimms, cpus and other entities and alert if the are not in proper operation mode<BR>
  (constraints are delivered by ipmi)

A documentation of the monitoring capabilities can be reviewed by this [html-documentation](http://htmlpreview.github.io/?https://github.com/vico-research-and-consulting/zabbix-supermicro/blob/master/templates/documentation/custom_hw_supermicro_ipmi.html).

Basically this script monitors the results of the following comands:

```
ipmitool -H <ipmi_host> -U <ipmi_user> -P <ipmi_pass> dcmi power reading
ipmitool -H <ipmi_host> -U <ipmi_user> -P <ipmi_pass> chassis status
ipmitool -H <ipmi_host> -U <ipmi_user> -P <ipmi_pass> sensor list
```

Usage
=====

- Install ipmitool on your zabbix-server/proxy
  ```
  apt install ipmitool -yy
  ```
- Define the following setting in your zabbix-server/proxy
  ```
  ExternalScripts=/etc/zabbix/externalscripts
  ```
- Add the python scripts to that folder and make them readable for the zabbix server
  ```
  cp supermicro_* /etc/zabbix/externalscripts
  chmod 755 /etc/zabbix/externalscripts/supermicro_*
  ```
- import the template in the "templates" folder
- configure host
  - assign the template to your host
  - define the following macros
    - {$IPMI_HOST}
    - {$IPMI_PASS}

Technical details
=================

- Scripts cache values in ```/tmp/supermicro_ipmi_*_<ipmi_HOST>_<numeric uid>``` to prevent a high number of ipmitool executions for one minute
- Tool can be debugged using commands like this:
  ```
  ./supermicro_ipmi_sensors <hostname or ip> "IPMIUSER" "IPMIPASS" show
  ./supermicro_ipmi_sensors <hostname or ip> "IPMIUSER" "IPMIPASS" discover ".*DIMM.*"
  ./supermicro_ipmi_sensors <hostname or ip> "IPMIUSER" "IPMIPASS" get 5VSB

  ./supermicro_ipmi <hostname or ip> "IPMIUSER" "IPMIPASS" all
  ./supermicro_ipmi <hostname or ip> "IPMIUSER" "IPMIPASS" 'Power Overload'
  ```

Authors
=======

- Amin Dandache <amin.dandache@vico-research.com>
- Marc Schoechlin <marc.schoechlin@vico-research.com>


Licence
=======

see "[LICENSE](./LICENSE)" file
