
# Department OUs
New-ADOrganizationalUnit -Name "Management" -Path "OU=Company,DC=yourdomain,DC=com"


# Management
New-ADUser -Name "Daniel" -GivenName "Daniel" -Surname "Manager" -SamAccountName "daniel" -UserPrincipalName "daniel@yourdomain.com" -AccountPassword (ConvertTo-SecureString "Passw0rd!" -AsPlainText -Force) -Enabled $true -Path "OU=Management,OU=Company,DC=yourdomain,DC=com" -Title "General Manager"


Final Check
Use the following to verify creation:
Get-ADUser -Filter * -SearchBase "OU=Company,DC=yourdomain,DC=com" | Format-Table Name, SamAccountName, Title, DistinguishedName
