
### EVE-NGLABS


First Open EVEN-NGLABS and type 
ip add: 208.8.8.187
---
<img width="472" height="138" alt="{A58FB29A-E0A3-416A-919D-149E72438E74}" src="https://github.com/user-attachments/assets/0cc44e04-290e-4df5-88a5-d2172421f71c" />


---
User login: root
Password: C1sc0123
---
&nbsp;


!@cmd 
route add 10.69.255.13 mask 255.255.255.255 10.69.255.6

<br>
<img width="810" height="575" alt="{7358A47D-710A-48DD-9979-743BFF2DF639}" src="https://github.com/user-attachments/assets/6473ced0-4a90-4bc0-b0ea-b80976fd9bf7" />
<br>

---
### click on open

<br>
<img width="323" height="157" alt="{8C5D5FF1-E0A9-4D5B-94AF-D4F574D676D4}" src="https://github.com/user-attachments/assets/33ca3948-4cd8-408f-8f6a-cd9e4704dced" />
<br>
---
/* click on the dark mode for details */


&nbsp;


#### Click on Start these Devices


---
| vManage     | Start        |
| vSmart      | Start        |
| Cloud       | Start        |
| vBond       | Start        |
---

<br>
<img width="785" height="750" alt="{AC91BA4D-3FF4-49C4-B41C-65AF011FE07A}" src="https://github.com/user-attachments/assets/2b3d1313-5d8d-4e9e-b0f1-1b7442334027" />
<br>
/* Wait for 10 minutes to boot up */

&nbsp;


### Open Secure CRT and Telnet 

| Devices        | Ports |
| vManage        | 32897 |
| vSmart         | 32898 |
| vBond          | 32899 |



<br>
<img width="659" height="558" alt="{C2493DF2-1DB2-4A8B-BB5C-7EA30D44AE51}" src="https://github.com/user-attachments/assets/ba8d7253-62fe-4693-8c9b-6208e3a006d4" />
<br>

### Note: Before accessing the vManage, it must have a log "System Ready" 

<img width="554" height="437" alt="{84569F3C-24B7-45C0-89CC-647087B2CA94}" src="https://github.com/user-attachments/assets/934afd23-c379-4419-bae2-b21d86c87722" />

/* there is 5 attempts for login before lockout so be careful */


### Go to GITHUB and paste this at vManager-Manage

---
!@vManage
conf t
 system
  host-name Rivan-vManage
  site-id 10
  system-ip 10.1.10.2
  organization-name RIVANCORP
  vbond 172.16.10.3
  admin-tech-on-failure
 vpn 0
  int eth0
   ip add 172.16.10.2/29
   no shut
   tunnel-interface
    allow-service all
  ip route 0.0.0.0/0 172.16.10.6
 vpn 512
  int eth1
   ip add 10.69.255.13/30
   no shut
  ip route 0.0.0.0/0 10.69.255.14
  commit
  end
---

<br>
Verification

!@vManage
request nms all status
show system status
<br>

> [!NOTE]
> This must be enable and running 

<img width="377" height="61" alt="{3001B43B-B8EC-4524-B170-FEC6DB0A2665}" src="https://github.com/user-attachments/assets/d045e7db-bffa-42d4-a231-0dbc87c850ad" />

<br>
###Copy and paste it this on vbond line 122-140 and vSmart 146-163
~~~
!@vBond
conf t
 system
  host-name Rivan-vBond
  site-id 10
  system-ip 10.1.10.3
  organization-name RIVANCORP
  vbond 172.16.10.3 local vbond
  admin-tech-on-failure
 vpn 0
  int ge0/0
   ip add 172.16.10.3/29
   no shut
   tunnel-interface
    encapsulation ipsec
    allow-service all
  ip route 0.0.0.0/0 172.16.10.6
  commit
  end
~~~

<br>

~~~
!@vSmart
conf t
 system
  host-name Rivan-vSmart
  site-id 10
  system-ip 10.1.10.1
  organization-name RIVANCORP
  vbond 172.16.10.3
  admin-tech-on-failure
  vpn 0
   int eth0
    ip add 172.16.10.1/29
	no shut
	tunnel-interface
	 allow-service all
   ip route 0.0.0.0/0 172.16.10.6
   commit
   end
~~~

<br>

> [!IMPORTANT]
> Wait until the SDWAN Controllers are stable before turning on the vEdges




<br> 

### STEP 5 - Configure BGP on CLOUD to establish connection with controllers
~~~
!@Cloud (BGP Config)
conf t
 int lo8
  ip add 8.8.8.8 255.255.255.255
 router bgp 1
  bgp log-neighbor-changes
  neighbor 192.168.20.1 remote-as 100
  neighbor 192.168.20.5 remote-as 100
  neighbor 192.168.20.9 remote-as 100
  address-family ipv4
   neighbor 192.168.20.1 activate
   neighbor 192.168.20.5 activate
   neighbor 192.168.20.9 activate
   neighbor 192.168.20.1 as-override
   neighbor 192.168.20.5 as-override
   neighbor 192.168.20.9 as-override
   network 8.8.8.8 mask 255.255.255.255
   network 192.168.20.0 mask 255.255.255.0
   network 172.16.10.0 mask 255.255.255.248
   network 10.69.255.0 mask 255.255.255.248
   end
~~~


<br>

###Go to the Topology GUI and Open this "Start" vEdge-Luzon and vEdge-Visayas 
### then telnet them both to SecureCRT 

| Device         | Port  |
| ---            | ---   |
| vEdge-LUZON    | 32900 |
| vEdge-VISAYAS  | 32903 |


<img width="757" height="330" alt="{65CF34E5-BE44-4318-BAB6-1E477BD20449}" src="https://github.com/user-attachments/assets/a9eadbe9-cf21-487a-85dd-cb8117ad86b3" />


<br>


### Access this URL in a browser 


URL: https://10.69.255.13:8443  
- User: admin
- C1sc0123


<br>
<img width="1918" height="1016" alt="{88D7ACF7-AF0E-45F2-81B9-92EF9BFFE3BA}" src="https://github.com/user-attachments/assets/c957977a-b27f-405c-93f9-832cee6e7675" />
<br>
&nbsp;
### Click on the Burger Menu on the Top Left --> Configuration --> Certificates 

<br>
<img width="392" height="357" alt="{D70A94CE-A560-44FC-964F-BBB493B96BF4}" src="https://github.com/user-attachments/assets/9dee28da-d534-4887-a122-6d83eeb94189" />
<br>
&nbsp;
<img width="503" height="98" alt="{E6BB3CB1-38E1-411B-ABF5-50166350A49B}" src="https://github.com/user-attachments/assets/d49c4d4d-48ae-4a1f-8ad1-87390d09c306" />
<br>

&nbsp;

### Click on the "Send To Controllers"
<br>
<img width="1898" height="335" alt="{70D6B314-C01B-4677-89F6-A5781131AD23}" src="https://github.com/user-attachments/assets/b675f097-b41c-4c66-ac89-ff72b1234083" />
<br>

> [!IMPORTANT]
> Wait to Make Status to be "Success"
<br>
<img width="1881" height="310" alt="{2E63700C-A35C-44C5-89D3-3A8653B6BAFD}" src="https://github.com/user-attachments/assets/cb8418a2-2663-4c7c-8036-f3df400cd7c0" />
<br>



### Click on the Burger Menu on the Top Left --> Configuration --> Templates --> Feature Templates 



<br>
<img width="455" height="149" alt="{15CBA7FE-AF82-4ADE-BA3E-5BA624052408}" src="https://github.com/user-attachments/assets/0264b0c9-9b8f-4ab8-8f90-03fee2d0314b" />
<br>

### Click on "Add Template" 

<br>
<img width="371" height="215" alt="{C92307B5-44DD-4BED-A886-C8B880C8684F}" src="https://github.com/user-attachments/assets/2c455118-a8ca-4c8a-8542-7c223a19c72e" />
<br>

### Type on the Filter "vEdge Cloud"

<br>
<img width="453" height="287" alt="{9920410B-079A-4DC2-A23C-D7CCEAE5294B}" src="https://github.com/user-attachments/assets/a95c0b11-7d2b-4893-b82e-6df959ea0820" />
<br>

&nbsp;

#### Creating vEdge Templates 

### vEdge --> Basic Information --> System
###TemplateVE   - VE-SYSTEM
###Description  - VE-SYSTEM
---
| Basic Configuration                             |
| :---              | :---                        |
| Site ID           | Device Specific             |
| SYSTEM Ip         | Device Specific             |
| Hostname          | Device Specific             |
| Console Baud Rate | Global, 9600                |
---
