# LDAP (389/636)

- **Purpose:** read directory objects — users, groups, and domain structure — often without credentials.

| Task | Command |
| --- | --- |
| Find naming context | `ldapsearch -x -H ldap://$IP -s base namingcontexts` |
| Anonymous dump | `ldapsearch -x -H ldap://$IP -b "DC=domain,DC=local"` |
| Authenticated query | `ldapsearch -x -H ldap://$IP -D 'user@domain.local' -w pass -b "DC=domain,DC=local"` |
| Enumerate via netexec | `nxc ldap $IP -u user -p pass --users` |

- **Install:** `sudo apt install ldap-utils`
- In Active Directory, feed findings into [06-Active-Directory](../06-Active-Directory/) (BloodHound, kerbrute).
