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
|One network adapter at first | Not enable Adapter 2 on (internal network) in Virtual Box | Shutdown the VM then enable Adapter 2 as a Labnet the have a static IP 192.168.10.10 in LAB adapter |Cannot change password Maria's acc. (

## What I Learned


## Screenshots
(01 to 15, with one-line captions.)
