# Active Directory Home Lab

## Overview
Active Directory Home Lab | Windows Server 2022, Windows 11, Oracle VirtualBox

Built a virtual domain environment with a domain controller (DNS and AD DS) and a domain-joined Windows 11 client on an isolated internal network.
Created an OU structure for HR, IT, and Sales, with user accounts and security groups for each department.
Configured SMB shares with NTFS permissions so each department can only access its own folder (least privilege).
Deployed Group Policy drive mapping per department and verified access and denial from the client.
Performed password resets and account unlocks, and troubleshot domain join, DNS, and GPO path issues.

## Lab Architecture
| VM | OS | Role | IP | Network |
|---|---|---|---|---|
| DC01 | Windows Server 2022 | Domain controller (AD DS, DNS), file server | 192.168.10.10 | NAT + Internal (labnet) |
| CLIENT01 | Windows 11 Enterprise | Domain-joined client | 192.168.10.20 | Internal (labnet) |

Domain: `corp.lab`. The NAT adapter gives DC01 internet access. The internal network keeps domain traffic isolated, with DNS on the client pointing to DC01.

## What I Built
- Domain controller with AD DS and DNS (`corp.lab`) on Windows Server 2022
- OU structure: Corp > IT, HR, Sales
- User accounts and a security group per department (`GG-IT`, `GG-HR`, `GG-Sales`)
- SMB shares with NTFS permissions, so each department can access only its own folder
- Group Policy drive mapping per department (H:, I:, S:)
- Windows 11 client joined to the domain on an isolated internal network

## Testing and Results
| User | Department | Drive seen | Access denied to |
|---|---|---|---|
| msantos | HR | H: | \\DC01\IT, \\DC01\Sales |
| jdelacruz | IT | I: | \\DC01\HR, \\DC01\Sales |
| preyes | Sales | S: | \\DC01\HR, \\DC01\IT |

## Problems I Hit and How I Fixed Them
|Problem | Cause | Fix | 
- |One network adapter at first | Not enable Adapter 2 on (internal network) in Virtual Box | Shutdown the VM then enable Adapter 2 as a Labnet the have a static IP 192.168.10.10 in LAB adapter | 
- |Cannot change password Maria's acc. ("unable to update password") | Did not meet the length password policy | Reset password in DC01 using a long password |
- | shared H:drive is not showing on Maria's account | typo error on DC01 \\DC01\HR1 instead of \\DC01\HR | rename it in GPO as \\DC01\HR then in client01 i use gpupdate /force, sign out and in |
- | Password is incorrect on juan's account | typo or wrong password | Reset his password on ADUC |

## What I Learned
I learned how Active directory works on a Home Lab project. his project taught me how a company onboards an employee in Active Directory: create the account, add it to a department group, and give access only to that department’s folder. I learned that share permissions and NTFS permissions work together, and that the stricter one wins. I also learned that most domain problems start with DNS, and that tools like gpresult /r and ping help find where a problem actually is. Fixing a typo in a GPO path taught me to verify each layer (policy applied, path correct, permissions) instead of guessing.

## Screenshots
![VirtualBox Manager showing the DC01 and CLIENT01 virtual machines](screenshots/01-virtualbox.png)
*Figure 1: The two virtual machines in the lab: DC01 (Windows Server 2022, domain controller) and CLIENT01 (Windows 11, domain-joined client).*
<img width="1915" height="962" alt="Screenshot 2026-11-16 162850" src="https://github.com/user-attachments/assets/21badb70-d581-4919-8dea-31cb37de796e" />

![DC01 network adapters in VirtualBox](screenshots/02-dc01-network-settings.png)
*Figure 2: DC01 has two adapters: NAT for internet access and an internal network (`labnet`) for domain traffic.*<img width="902" height="730" alt="Screenshot 2026-11-16 163226" src="https://github.com/user-attachments/assets/7fe02d76-fe6e-4e34-ba7f-556e9b8ba02b" />

![ipconfig /all output on DC01](screenshots/03-dc01-ipconfig.png)
*Figure 3: DC01 uses the static IP 192.168.10.10 on the lab network, with its own address as the DNS server.*
<img width="1527" height="887" alt="Screenshot 2026-11-16 163313" src="https://github.com/user-attachments/assets/3219e5cf-bc86-472b-bae2-a3e4bea9d991" />

![Get-ADDomain output](screenshots/04-get-addomain.png)
*Figure 4: `Get-ADDomain` confirms the `corp.lab` domain is running on DC01.*
<img width="935" height="830" alt="Screenshot 2026-11-16 163420" src="https://github.com/user-attachments/assets/2404c2ec-ba79-4da0-8058-52608153db5a" />

![OU structure in Active Directory Users and Computers](screenshots/05-ou-structure.png)
*Figure 5: The OU structure: a parent OU (Corp) with IT, HR, and Sales departments.*
<img width="918" height="781" alt="Screenshot 2026-11-16 163515" src="https://github.com/user-attachments/assets/7782d46e-6429-4d83-8640-4cf30a4a88a8" />

![Users and security groups in ADUC](screenshots/06-users-and-groups.png)
*Figure 6: Each department has its own user account and security group (GG-IT, GG-HR, GG-Sales).*
<img width="870" height="661" alt="Screenshot 2026-11-16 163547" src="https://github.com/user-attachments/assets/d0a3a290-62cb-4c0e-89f6-2ed0bb280902" />

![Get-SmbShare output](screenshots/07-smb-shares.png)
*Figure 7: Three SMB shares (HR, IT, Sales) published from `C:\Shares`.*<img width="933" height="668" alt="Screenshot 2026-11-16 163622" src="https://github.com/user-attachments/assets/c332fd1b-349d-4478-a0b3-78c700c0a348" />

![icacls output for the HR folder](screenshots/08-ntfs-permissions.png)
*Figure 8: NTFS permissions on the HR folder allow only the HR group and administrators.*
<img width="963" height="693" alt="Screenshot 2026-11-16 163807" src="https://github.com/user-attachments/assets/6ae0d06f-ec86-418f-aabe-d70dd73242ee" />

![Group Policy Management showing the drive map GPO](screenshots/09-gpo-drive-map.png)
*Figure 9: A Group Policy linked to the HR OU maps `\\DC01\HR` as drive H:.*
<img width="897" height="663" alt="Screenshot 2026-11-16 163915" src="https://github.com/user-attachments/assets/70128c71-ccb6-4c9a-8a4e-9eb77c2e3be1" />

![System properties showing domain membership](screenshots/10-client-domain-join.png)
*Figure 10: CLIENT01 (Windows 11) joined to the `corp.lab` domain.*
<img width="878" height="695" alt="Screenshot 2026-11-16 164006" src="https://github.com/user-attachments/assets/8678ff2e-4469-46d0-a78a-9466629aaeeb" />

*Figure 11: Maria (HR) logs in on CLIENT01 and sees her department's H: drive.*
<img width="896" height="888" alt="Screenshot 2026-11-17 134948" src="https://github.com/user-attachments/assets/3838a690-362a-479b-85d3-df35ba502ae2" />

![IT user logged in with the I: drive](screenshots/12-it-user-i-drive.png)
*Figure 12: Juan (IT) sees only the I: drive for his department.*
<img width="952" height="917" alt="Screenshot 2026-11-17 135337" src="https://github.com/user-attachments/assets/542f9748-dfa3-4c6b-81a1-e772ea8a131e" />

![Sales user logged in with the S: drive](screenshots/13-sales-user-s-drive.png)
*Figure 13: Pedro (Sales) sees only the S: drive for his department.*
<img width="930" height="908" alt="Screenshot 2026-11-17 134113" src="https://github.com/user-attachments/assets/5e9558e5-5ef7-4671-8127-dc0ff28f7ca2" />

![Access denied error](screenshots/14-access-denied.png)
*Figure 14: An IT user is denied access to the HR share, confirming that permissions work.*
<img width="937" height="798" alt="Screenshot 2026-11-17 135819" src="https://github.com/user-attachments/assets/e02db50e-91a4-4ed6-8145-85d7e001390d" />

![Reset Password window in ADUC](screenshots/15-password-reset.png)
*Figure 15: Password reset and account unlock in Active Directory Users and Computers.*
<img width="920" height="833" alt="Screenshot 2026-11-17 135719" src="https://github.com/user-attachments/assets/c49074ef-09ee-44cf-8fae-d90139dea6fc" />
