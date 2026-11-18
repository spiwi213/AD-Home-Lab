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
