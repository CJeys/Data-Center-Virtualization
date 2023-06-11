# Data Center Virtualization
# VIRTUALIZATION SOLUTION
**HOW THE SOLUTION MEETS BUSINESS NEEDS**

For the proposed hybrid-cloud solution, a virtualized environment will be deployed to an ESXi host, proving the value and merit of utilizing virtualization technology.  To fully productionalize this solution, vSphere Datacenter should be used to take advantage of active directory integration and more robust features that are not available in the free ESXi release. 

**HOW THE SOLUTION MEETS THE TECHNICAL REQUIREMENTS**

The proposed solution has been deployed with several virtualized components that satisfy the requirements laid out for this proof of concept.  Specific details for the virtualization technology presented in this solution are listed below. 

**VIRTUAL HOSTS**

The following hosts/virtual machines will be deployed as part of the hybrid-cloud solution:

**DC01 (Windows Server 2019)**

This virtual machine will be the Domain Controller for augustacrissy.lab and provide DNS services to the other virtual machines in this solution.  In addition, DC01 is configured with DCHP to issue IP addresses for the DEV network.  Windows Server 2019 is the underlying operating system used for this virtual machine.

**Router (pfSense)**

This virtual router is configured as the remote access endpoint to the network.  In addition, it also serves as the primary network and main gateway for this virtualized solution.

**IIS01 & IIS02 (Windows Server 2019 Datacenter)**

These virtual machines are configured with IIS Webserver and Remote Access Server.  Network Load Balancing is also configured in conjunction with NIC Teaming.  This will provide redundancy at the server level by balancing web requests between them and at the NIC level.

**JMP01 (Windows 10 Enterprise)**

This virtual machine serves as the gateway host for remote access, via RDP, to internal services and virtual machines.

**NETWORKING**

Virtual switches, one per subnet, and port groups are deployed to isolate traffic between the different networks.  A single virtual switch could also be utilized.  However, this solution takes advantage of the low costs and quick deployment of virtualization technology by implementing multiple virtual switches to easily segregate traffic for admin and troubleshooting purposes.  

| Host                | Interface | IP               | VLAN       | vSwitch  |
|---------------------|-----------|------------------|------------|----------|
| Router (pfSense)    | Em0       | 172.16.0.111/24  | Public     | vSwitch  |
|                     | Em1       | 192.168.1.1/24   | SysADMIN   | SysADMIN |
|                     | Em2       | 10.0.6.111/24    | DEV        | DEV      |
| ESXi Server Host    |           | 172.16.0.213     |            |          |
| DC01 (Svr-19-Stanard)| Eth0      | 192.168.1.102/24 | DEV        | DEV      |
|                     | Eth1      | 10.0.6.12/24     | SysADMIN   | SysADMIN |
| IIS01 (Srv-Datacenter1) | Eth0   | 172.16.0.108/24  | Public     | vSwitch  |
|                     | Eth1      | 172.16.0.107/24  | Public     | vSwitch  |
|                     | Eth2      | 10.0.6.11/24     | SysADMIN   | SysADMIN |
|                     | Eth3      | 192.168.1.101/24 | DEV        | DEV      |
| IIS02 (Srv-Datacenter2) | Eth0   | 172.16.0.110/24  | Public     | vSwitch  |
|                     | Eth1      | 172.16.0.109/24  | Public     | vSwitch  |
|                     | Eth2      | 10.0.6.8/24      | SysADMIN   | SysADMIN |
|                     | Eth3      | 192.168.1.100/24 | DEV        | DEV      |
| IIS01 & IIS02 Load Balancer|      | 172.16.0.225     |            |          |
| JMP01 (Win10-Ent)   | Eth0      | 172.16.0.106/24  | Public     | vSwitch  |
|                     | Eth1      | DHCP assigned thru DC01 | DEV | DEV |
|                     | Eth2      | 10.0.6.7/24      | SysADMIN   | SysADMIN |

![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/25c0b10d-320a-46eb-859b-d7076c654fa6)

**SECURITY:**
The following security measures will be taken to protect the network and data within this hybrid-cloud solution.

**VIRUS SCAN SYSTEM:**
Windows Defender will serve as the default antivirus solution.  It has the capability to push and deploy virus definitions to the virtual machines along with the configuration of alerts for when unusual activity and threats are detected within the environment.  If there are other requirements due to governmental regulations or other commercial purposes, a more robust antivirus solution can be implemented.

**FIREWALL RULES:**
For all Windows operating systems, firewall rules will be implemented and enforced with Active Directory.  All inbound entry points will follow the principles of ‘least privilege’ to further enhance the security implementation.  To reduce managerial overhead, a stateful firewall should be the primary choice to minimize the number of outbound rules.  In addition, configuration management should be automated to secure network-level controls in a standard and uniform manner.  This will also help simplify admin and auditing of all firewall-related activities.

| Virtual Machines | DC01                                                                         | IIS01 & IIS02                                                           |
|------------------|------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Inbound Rules    | Permit HTTP (80), HTTPS (443), DNS (53), RDP (3389), & DHCP (67)             | Permit HTTP (80), HTTPS (443), & RDP (3389)                              |
| Outbound Rules   | Permit All                                                                   | Permit All                                                               |

**ACCESS CONTROL LISTS:**
Access control lists, similar to the firewall rules, will follow ‘least privilege’ principles to ensure that access is allowed on an “as needed” basis.  Login credentials for admin, and other users, will be kept separate so that the supporting infrastructure can be managed while also being utilized by the subscriber base without them interfering with each other.  With the virtual machines being joined to the augustacrissy.lab domain, more granular control over the resources and supporting infrastructure can be achieved.

The Network Access Control List will be applied to the VLANs as follows:

| Virtual Machine | VLAN-DEV                             | VLAN-SysADMIN                            | VLAN-Public                             |
|-----------------|--------------------------------------|------------------------------------------|-----------------------------------------|
| DC01            | Access to IIS01, IIS02, & JMP01       | Access to IIS01, IIS02, & JMP01          | -                                       |
| JMP01           | Access to IIS01 & IIS02               | Access to DC01, IIS01, & IIS02           | -                                       |
| IIS01 & IIS02   | Access to DC01 & JMP01                | Access to DC01 & JMP01                   | Access to JMP01                         |

**SECURITY GROUPS:**
Windows Security Groups is another security feature that will be used to help follow the principle of ‘least privilege.’  This will allow for the separation of rights and permissions to perform tasks by providing manageable groups where these can be configured, tracked, and audited.  Entry into the various security groups will follow the appropriate approval process determined by Augusta Crissy Detective Games.

**INFORMATION SECURITY MANAGEMENT:**
All applicable standards and procedures of ACDG will be followed to mitigate risks and help with compliance management.  In addition, IOS-IEC 27001 standards for Information Security Management Systems will also be followed to capture, review, and build upon the current InfoSec policies and procedures.

**IMPLEMENTATION PROCESS:**
The following chart lists the phases, milestones, and dependencies for this hybrid-cloud solution.

| Phase             | Milestone                                                  | Dependencies                                                                                                       |
|-------------------|------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| Build             | Virtual machines deployed                                  | The virtual machines will be built, set up, & deployed for the separate operating systems required for this implementation. |
| Network           | Network infrastructure deployed, configured & secured     | The network will be mapped out, virtual switches & port groups set up, virtual machine IP addresses configured & ‘teamed’ where required, & firewall rules put in place. |
| Services & Roles  | Configure services & assign user roles                     | The virtual machines will have DNS, DHCP, remote desktop services, network load balancing, & IIS enabled based on the configuration requirements. |
| Test & Validate   | Verify functionality of the implementation                | Benchmark testing will be performed by the user base to ensure the new virtualized solution meets the performance and functionality requirements while also providing them the opportunity to give feedback. |

**PERFORMANCE TUNING:**
For this hybrid-cloud solution, NIC teaming will be implemented on the IIS01 & IIS02 servers.  Network Load Balancing will also be utilized on the network.  With the increased amount of network adapters attached to each server along with NLB, network throughput will be significantly increased while also providing redundancy at both the NIC and server levels.

Performance Monitor will also be used to capture and report on CPU utilization, network bandwidth, and memory consumption.  This will help provide reports for a review of the environment to determine if there are opportunities to fine-tune or improve on the solution ensuring all resources are correctly utilized without bottlenecks that will decrease the subscriber’s gaming experience.

**LOAD BALANCING:** 
Load balancing will be implemented to provide ACDG’s subscriber base with an improved gaming experience.  This will help balance gaming traffic across the IIS01 & IIS02 servers to improve performance.  This also provides the solution with redundancy in case of a server failure, as traffic can be redirected whenever a server goes offline unexpectedly.

# PROOF-OF-CONCEPT IMPLEMENTATION BUILD 

**PHASE – BUILD**

**VMs:** VMware ESXi with all VMs online
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/810872cc-03c6-43fa-8410-d7f7071b8c1b)

**PHASE – NETWORK**

**PORT GROUPS:** Public, DEV, & SysADMIN port groups created
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/0a3caaa6-c641-4ede-8039-5fd071551d4b)

**vSwitches:** vSwitch0(Public), DEV, & SysADMIN virtual switches
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/5c3c2e6c-72af-426b-bc8f-cae370288cf0)

**pfSense:** Router online and configured
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/965526a9-19e8-46b4-90a8-fd94be912969)

**DC01:** Firewall Inbound & Outbound rules enabled
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/9d68fa6d-b894-42c8-9795-6785f347489b)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/26326eae-4f00-4e4d-b2ab-9f9d065bb9f1)

**IIS01:** Firewall Inbound & Outbound rules enabled
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/4276aead-c3a4-418a-b308-708047557535)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/7327e8ed-870d-4d3d-af40-52a23684a19c)

**IIS02:** Firewall Inbound & Outbound rules enabled
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/55175eb9-8deb-43eb-97ce-cfd4d7576ecc)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/d416de19-1936-41fd-9afe-4c4ff56992a7)

**PHASE 3 – SERVICES & ROLES**

**DOMAIN:** Domain ‘augustacrissy.lab’ configured on DC01
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/f7c6b357-0a04-40ef-8e5a-cfb909694b7a)

**IIS01:** NIC Teaming
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/e90eebee-322c-433b-83d3-1628427f9b1d)

**IIS02:** NIC Teaming
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/3bae72c5-f5e8-4a21-a76e-42b66dbec9d8)

**IIS01 & IIS02:** Network Load Balancing & clustering configured
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/19081a04-94ac-463f-9f2d-a7a14812ea15)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/c0caca7e-6854-4b36-8fe9-8676ef6f11a1)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/cf561e73-aad6-465f-bef7-9f8668f56ee6)

**IIS01 & IIS02:** Anonymous authentication enabled
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/cc2da598-7d73-4d7f-bb48-b8435a76fbef)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/947a2e01-d087-4d65-9761-6fc24169002d)

**IIS:** Configuration complete & accessible via JMP01
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/6c5def1d-6a08-4da7-927b-d6e9e11c0ab4)
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/5052382a-bdce-42a4-a4fa-fb3c6d512c0a)

**PHASE – TEST & VALIDATE**

**Performance Monitor:** CPU utilization, network bandwidth, and memory consumption captured for reporting purposes
![image](https://github.com/CJeys/Data-Center-Virtualization/assets/126273127/ba7a0b08-b190-4e85-a429-33cae011aedd)
