### Ubuntu Check for Required Updates:
```bash
sudo cat /var/lib/update-notifier/updates-available
```

### Ldapsearch - User
```bash
ldapsearch -L -c -E pr=2000/noprompt -o ldif-wrap=no -x -s sub -h <domainName> -b <SearchBase DN> -D <ldapUser> -W "(&(objectCategory=Person) (objectClass=user)(mail=<searchUserEmail>))"
```

### Ubuntu 18.04 LTS - Upgrade to Linux 5.X Kernel
```bash
sudo apt-get install --install-recommends linux-generic-hwe-18.04
```

### Git Branch Count:
```bash
git branch -r | wc -l
```

### Top 10 Largest Git Files:
```bash
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | sed -n 's/^blob //p' | sort --numeric-sort --key=2 | $(command -v gnumfmt || echo numfmt) --field=2 --to=iec-i --suffix=B --padding=7 --round=nearest | tail -n 10 | tac
```

### Get the SHA for a specific branch:
```bash
git rev-parse DEVOPS-999-setup-prod-runs-for-dsp-alg-sim
```

### Check LM License Usage:
```bash
while ($true); do clear; echo -e -n "license01:\n"; lmstat -a -c 5280@license01 | grep -i "xcelium"; echo -e -n "\nfpgaserv:\n"; lmstat -a -c 5280@fpgaserv | grep -i "xcelium";  sleep 30; done
```

### Find all files belonging to a user and update ownership:
```bash
find ./ -user <oldUid> -exec chown <newUid> {} \;
```

### Find all files belonging to a group and update ownership:
```bash
find ./ -group <oldGid> -exec chgrp -h <newGid> {} \;
```

### Find all directories with multiple files larger than X MB:
```bash
find ./ -type f -size +100M | sed 's:[^/]*$::' | uniq -d
```