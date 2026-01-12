### Next UID Number:
```pwsh
(((Get-ADUser -Filter {uidNumber -gt "10000" -and uidNumber -lt "19999"} -Properties uidNumber | select name, uidNumber | sort uidNumber)[-1]).uidNumber) + 1
```

### Next GID Number
```pwsh
(((Get-ADGroup -Filter {gidNumber -gt "50000" -and gidNumber -lt "59999"} -Properties gidNumber | select name, gidNumber | sort gidNumber)[-1]).gidNumber) + 1
```

### Add Multiple Hosts to a NisNetGroup
```pwsh
# Create array:
$netGroupHost = @("tb1-vm-ev01", "tb1-vm-ev02", "tb1-vm-ev03","TestHost01")

#Add hosts:
Set-ADObject -Identity 'CN=testMember01,OU=Netgroups,DC=corp,DC=kodiak,DC=ai' -Add @{memberNisNetgroup=$netGroupHost}
```

### Check when users last updated password
```pwsh
while ($true) { clear; foreach ( $acct in $(Get-Content .\SSO_Group5.txt) ) { Get-ADUser -Filter 'mail -eq $acct' -Properties PasswordLastSet | Select-Object givenName, surName, PasswordLastSet }; Start-Sleep 10s }
```

### Update DNS Zone Transfer Policy
```pwsh
(Get-DnsServerZone | Where-Object {($_.ZoneName -notlike "_msdc*") -and ($_.ZoneName -nE "TrustAnchors") -and ($_.ZoneType -eq "Primary") -and ($_.IsAutoCreated -ne "False")}) | %{Set-DnsServerPrimaryZone -Name $_.ZoneName -SecureSecondaries TransferToSecureServers -SecondaryServers <allowedTransferServers> -Notify NotifyServers -NotifyServers <allowedTransferServers>}
```

### Create AutoFS Entry
```pwsh
Import-Module C:\scripts\modules\autoFS.psm1

New-AutoFSEntry -AdFqdn "corp.aeva.com" -AdCredentials $(Get-Credential) -ObjectPath "OU=AutoFS,DC=corp,DC=aeva,DC=com" -MapName "<mapName>" -EntryName "<mountName>" -AutoFSValue "-rw,relatime,rsize=16384,wsize=16384,hard,proto=tcp,timeo=600,retrans=2 <pathToExport>"
```

### Get Users SID
```pwsh
wmic useraccount get name,sid | Select-String <userName>
```

### List All AutoFS Mapps that Point to NFS Exports
```pwsh
Get-ADObject -SearchBase "OU=AutoFS,DC=corp,DC=aeva,DC=com" -LDAPFilter "(objectClass=nisObject)" -Properties name,nisMapEntry | where {$_.nisMapEntry -like "-rw,*"}
```

### Change nisMapEntry
```pwsh
Get-ADObject -SearchBase "OU=AutoFS,DC=corp,DC=aeva,DC=com" -LDAPFilter "(objectClass=nisObject)" -Properties name,nisMapEntry | where {$_.nisMapEntry -like "-rw,*"} | foreach { Set-ADObject -Identity $_.DistinguishedName -Replace @{nisMapEntry=$($($_.nisMapEntry).Replace('timeo=600','timeo=30'))} -WhatIf }
```
#### Add New AD User
```pwsh
New-ADUser -Name "<some_user_account>" `
   -GivenName "<Some>" `
   -Surname "<User_Account>" `
   -SamAccountName "<some_user_account>" `
   -UserPrincipalName "<some_user_account@corp.kodiak.ai>" `
   -Path "OU=Service,OU=Accounts,DC=corp,DC=kodiak,DC=ai" `
   -ChangePasswordAtLogon $false `
   -Enable $false
```