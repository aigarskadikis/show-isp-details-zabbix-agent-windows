# Show Internet Service Provider (ISP) details on Windows station. Template applicable for Zabbix 4.0 and later.



## Dependencies

### Timeout

Agent configuration must have timeout installed. Default Timeout=3 is not enought.
```
Timeout=10
```

### PowerShell 5

To populate PowerShell 5 on Windows 7 please see https://catonrug.blogspot.com/2017/04/powershell-5-silent-install.html

This is tested and works on Windows 7 with PowerShell 5.0. Before applying template to workstation/server kindly test if the following command reports a JSON kind of output. ~100 lines in output.
```
Invoke-WebRequest -UseBasicParsing -Uri "https://rest.db.ripe.net/search.json?query-string=$(Invoke-WebRequest -UseBasicParsing -Uri https://myexternalip.com/raw | foreach { $_.Content } )" | foreach { $_.Content }
```
The folowing command is tested and NOT works on PowerShell 2.0 becuase 'Invoke-WebRequest' does not exist.


You can also test directly from Windows Command prompt by initiating the following command:
```
powershell -nologo -command "(Invoke-WebRequest -UseBasicParsing -Uri \"https://rest.db.ripe.net/search.json?query-string=$(Invoke-WebRequest -UseBasicParsing -Uri https://myexternalip.com/raw ^| foreach { $_.Content } )\" | foreach { $_.Content } )"
```
appart from the original PowerShell command, now the double quotes inside the powershell command gets escapted (\") and the pipe gets different format (^|).


### Zabbix agent (active)

While it may work as a passive check 'Zabbix agent' it is highly recommended to use 'Zabbix agent (active)' because it takes at least 2 seconds to load powershell environment.


### Install UserParameter
```
UserParameter=show.isp.details.json,powershell -nologo -command "(Invoke-WebRequest -UseBasicParsing -Uri \"https://rest.db.ripe.net/search.json?query-string=$(Invoke-WebRequest -UseBasicParsing -Uri https://myexternalip.com/raw ^| foreach { $_.Content } )\" | foreach { $_.Content } )"
```

## Appendix 

### Recomendation

On Zabbix 4.2 it is recomended to set item throttling 'Discard unchanged' for the master item.

### Grow template

There is a lot of information available under the master item. By default the history is 0. Change it if you want to add a different information.

### Known issues

Sometimes 'Invoke-WebRequest' command uses a cached version. This may cause a wrong information picked up.


