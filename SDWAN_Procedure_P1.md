## Step-by-Step Procedure

This guide covers the initial setup and startup procedure for the RivanCorp SD-WAN Deployment Lab, including EVE-NG access, vManage startup, and SecureCRT console access.

---

## STEP 1 - Configure a Host Route for the vManage GUI

Open **Command Prompt (CMD)** and add a host route for the vManage GUI:

```cmd
route add 10.69.255.13 mask 255.255.255.255 10.69.255.6
```

> **Note:** Run Command Prompt as **Administrator** if the command requires elevated privileges.

---

## STEP 2 - Review the SD-WAN Appliance Credentials

### SD-WAN Appliance Login

> **Username:** `admin`  
> **Password:** `C1sc0123`

### Appliance Console Ports

| Device | Port |
| --- | ---: |
| CLOUD | `32902` |
| PKI SERVER | `32913` |
| vManage | `32897` |
| vSmart | `32898` |
| vBond | `32899` |
| vEdge-LUZON | `32900` |
| vEdge-VISAYAS | `32903` |
| vEdge-MINDANAO | `32904` |
| CSW-LUZON | `32905` |
| CSW-VISAYAS | `32906` |
| CSW-MINDANAO | `32907` |

---

## STEP 3 - Obtain the EVE Lab IP Address

Identify and take note of the IP address assigned to the **EVE Lab**.

<img width="564" height="136" alt="EVE Lab IP Address" src="https://github.com/user-attachments/assets/91c17d71-c891-4cb2-829c-6ec80199dfee" />

---

## STEP 4 - Access the EVE-NG Topology

Open the EVE-NG web interface using the IP address obtained in **Step 3**.

Use the following credentials:

> **Username:** `rivan`  
> **Password:** `C1sc0123`

Click **Sign In**.

<img width="1440" height="782" alt="EVE-NG Login Page" src="https://github.com/user-attachments/assets/cdc31fc2-7ce3-4173-951c-10cc5a19256e" />

---

## STEP 5 - Open the RivanCorp SD-WAN Deployment Lab

From the EVE-NG lab list:

1. Locate **RivanCorp SDWAN Deployment Lab**.
2. Select the lab.
3. Click **Open**.

<img width="1438" height="652" alt="RivanCorp SD-WAN Deployment Lab" src="https://github.com/user-attachments/assets/47bab84c-0ccf-468c-afff-c17c51668d41" />

---

## STEP 6 - Enable Dark Mode

Once inside the topology, navigate to the menu on the **left side** of the EVE-NG interface.

Enable **Dark Mode** for better visibility of the topology and device information.

<img width="145" height="373" alt="EVE-NG Dark Mode Option" src="https://github.com/user-attachments/assets/c6dd8cd7-7bb1-4fc0-a1c6-bffea598cd0a" />

---

## STEP 7 - Start the vManage Manager

Locate the **vManage-Manager** node in the topology.

1. Right-click **vManage-Manager**.
2. Click **Start**.

<img width="259" height="256" alt="Start vManage Manager" src="https://github.com/user-attachments/assets/22ccbee1-2d4c-4baa-83bc-ee911340865a" />

---

## STEP 8 - Access vManage Using SecureCRT

After starting **vManage-Manager**, access its console using **SecureCRT** via Telnet.

Configure the SecureCRT session with the following information:

| Setting | Value |
| --- | --- |
| Protocol | `Telnet` |
| IP Address | `208.8.8.187` |
| Port | `32897` |
| Device | `vManage` |

> [!IMPORTANT]
> vManage may take approximately **10 minutes** to fully initialize. Do not proceed with vManage configuration until the system reports that it is ready.

---

## STEP 9 - Start the vSmart, vBond, and CLOUD Nodes

While waiting for vManage to initialize, start the following nodes:

1. **vSmart-Controller**
2. **vBond-Validator**
3. **CLOUD**

Access each device through **SecureCRT** using Telnet.

Use the following IP address:

```text
208.8.8.187
```

Use the appropriate console port for each device:

| Device | Telnet Port |
| --- | ---: |
| CLOUD | `32902` |
| vSmart | `32898` |
| vBond | `32899` |

<img width="577" height="581" alt="SecureCRT SD-WAN Console Sessions" src="https://github.com/user-attachments/assets/ea86fd30-a108-4097-b11a-d89919d04947" />

---

## STEP 10 - Verify That vManage Is Ready

Wait approximately **10 minutes** for the vManage appliance to complete its startup process.

Monitor the **vManage-Manager** console in SecureCRT.

The expected output should indicate that the system is ready, similar to the following:

<img width="1065" height="707" alt="vManage System Ready Output" src="https://github.com/user-attachments/assets/b1f9e1c9-a7c8-485d-b6d2-583b12fe68c6" />

> [!NOTE]
> Once the **system ready** message appears, the vManage appliance has completed its initialization and you can proceed with the next configuration steps.

---

## STEP 11 - Verify the vManage NMS Services

Access the **vManage-Manager CLI** through SecureCRT.

Run the following command to check the status of all vManage NMS services:

```bash
request nms all status
```

<img width="525" height="100" alt="Check vManage NMS Status" src="https://github.com/user-attachments/assets/0cc76fdc-bd17-4628-85a2-7d8d3da067a7" />

Verify that the **NMS Application Server** is running.

The status should show:

```text
NMS application server
Enabled: true
Status: running
```

<img width="366" height="57" alt="NMS Application Server Running" src="https://github.com/user-attachments/assets/94085a7e-b0e5-43d4-baad-0714086720b1" />

> [!IMPORTANT]
> Do not proceed with the vManage configuration until the **NMS Application Server** shows a `running` status.

---

## STEP 12 - Configure vManage

Once the NMS Application Server is running, configure the vManage appliance.

Copy and paste the following configuration into the **vManage CLI**:

```text
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
```

<img width="605" height="687" alt="vManage CLI Configuration" src="https://github.com/user-attachments/assets/309e8dac-8625-4e2c-a000-af5c3fed010e" />

> [!NOTE]
> After entering the configuration, verify that the `commit` operation completes successfully before proceeding.

---

## STEP 13 - Access the vManage GUI

After configuring vManage, open its web interface using a browser.

> [!NOTE]
> **Microsoft Edge is recommended** for accessing the vManage GUI in this lab.
>
> It may take approximately **10 minutes** after configuration before the vManage GUI becomes accessible.

Open the following URL:

```text
https://10.69.255.13:8443
```

Use the following credentials:

> **Username:** `admin`  
> **Password:** `C1sc0123`

Click **Log In**.

<img width="1440" height="790" alt="vManage GUI Login Page" src="https://github.com/user-attachments/assets/5f81037b-17f7-4839-83b5-4c6c13103c80" />

> [!TIP]
> If the GUI does not load immediately, wait a few minutes and refresh the browser. Make sure the host route configured in **Step 1** is still present.

---

## STEP 14 - Configure the vBond Controller

While waiting for the vManage GUI to initialize, access the **vBond-Validator** appliance through SecureCRT.

Use the following credentials:

> **Username:** `admin`  
> **Password:** `C1sc0123`

Copy and paste the following configuration into the **vBond CLI**:

```text
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
```

<img width="643" height="468" alt="vBond CLI Configuration" src="https://github.com/user-attachments/assets/3e272b23-ac80-410f-b963-d5702307126a" />

> [!NOTE]
> Verify that the configuration commits successfully before proceeding.

---

## STEP 15 - Configure the vSmart Controller

Access the **vSmart-Controller** appliance through SecureCRT.

Use the following credentials:

> **Username:** `admin`  
> **Password:** `C1sc0123`

Copy and paste the following configuration into the **vSmart CLI**:

```text
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
```

<img width="628" height="588" alt="vSmart CLI Configuration" src="https://github.com/user-attachments/assets/1e1af560-11e8-4e15-b818-10090df7f160" />

> [!IMPORTANT]
> Wait until the **SD-WAN controllers are stable** before proceeding with the vEdge devices.

---

## STEP 16 - Start the vEdge Nodes

While waiting for the vManage GUI and SD-WAN controllers to stabilize, start the following vEdge nodes:

1. **vEdge-LUZON**
2. **vEdge-VISAYAS**

Right-click each node in the EVE-NG topology and select **Start**.

<img width="767" height="167" alt="Start vEdge Luzon and Visayas Nodes" src="https://github.com/user-attachments/assets/50042b4b-1be6-44ab-bdba-180bd446a4dd" />

> [!NOTE]
> At this stage, the vEdge nodes may be powered on but are not yet expected to have full reachability to the SD-WAN controllers.

---

## STEP 17 - Verify the vManage GUI

Return to the **vManage GUI** in Microsoft Edge.

If necessary, refresh the browser:

```text
https://10.69.255.13:8443
```

Wait for vManage to finish initializing.

The vManage dashboard should load successfully, similar to the following expected output:

<img width="1440" height="727" alt="vManage Dashboard Expected Output" src="https://github.com/user-attachments/assets/1ca9ce51-a884-435c-8f87-6c29fad05182" />

> [!NOTE]
> The vEdge devices may still be unreachable at this stage. The **CLOUD node** must be configured to provide the required routing between the SD-WAN controllers and vEdge devices.

---

## STEP 18 - Access the CLOUD Node

Access the **CLOUD** node through SecureCRT using Telnet.

Use the following connection information:

| Setting | Value |
| --- | --- |
| Protocol | `Telnet` |
| IP Address | `208.8.8.187` |
| Port | `32902` |
| Device | `CLOUD` |

Enter privileged EXEC mode:

```text
enable
```

Enter the enable password when prompted:

```text
pass
```

---

## STEP 19 - Configure BGP on the CLOUD Node

Configure the CLOUD node to establish BGP connectivity with the vEdge devices.

Copy and paste the following configuration:

```text
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
```

<img width="632" height="624" alt="CLOUD Node BGP Configuration" src="https://github.com/user-attachments/assets/84f81dba-b770-49a9-93bd-28cb4de3bd02" />

> [!IMPORTANT]
> The CLOUD node provides routing between the SD-WAN infrastructure and the vEdge devices. Make sure the BGP configuration is applied successfully before proceeding with the vEdge configuration.

---

## STEP 20 - Verify vEdge Reachability in vManage

After completing the BGP configuration on the **CLOUD** node, return to the **vManage GUI** and verify the status of the vEdge devices.

Refresh the vManage GUI if necessary:

```text
https://10.69.255.13:8443
```

Check the dashboard and confirm that the vEdge devices are now **reachable**.

The expected output should be similar to the following:

<img width="1440" height="723" alt="vManage vEdge Reachability Verification" src="https://github.com/user-attachments/assets/a296cc71-4043-4d59-88d7-8816dc02f627" />

> [!IMPORTANT]
> The vEdge devices should now appear as **reachable** in the vManage GUI. This confirms that the CLOUD node's BGP configuration is providing the required network reachability between the vEdge devices and the SD-WAN controllers.

> [!NOTE]
> If the vEdge devices are still unreachable, verify the **CLOUD BGP configuration**, BGP neighbor status, IP addressing, and routing before proceeding to the next step.

---

## STEP 21 - Send the vEdge List to the Controllers

Before creating the vEdge feature templates, send the current vEdge device list to the SD-WAN controllers to reinitialize and synchronize the certificate information.

From the **vManage GUI**, navigate to:

```text
☰ Menu → Configuration → Certificates
```

<img width="199" height="98" alt="vManage Configuration Menu" src="https://github.com/user-attachments/assets/f0d24f62-59d0-4290-9175-a4083e0b33ba" />

Select **Certificates**.

<img width="492" height="66" alt="vManage Certificates Menu" src="https://github.com/user-attachments/assets/ed83972c-cb8f-47d7-8db2-4d348f290c69" />

Click **Send to Controllers**.

<img width="224" height="50" alt="Send to Controllers Button" src="https://github.com/user-attachments/assets/29e36971-f2f2-41ed-9116-8ad5cdc472b4" />

Wait for vManage to complete the operation.

The expected output should be similar to the following:

<img width="1437" height="332" alt="Send vEdge List to Controllers Expected Output" src="https://github.com/user-attachments/assets/29685aa5-22a5-4b38-ab9e-5f3e6c17bdb3" />

> [!IMPORTANT]
> Make sure the operation completes successfully before proceeding with the creation of the vEdge feature templates.

---

# vEdge Feature Template Configuration

The following steps create the feature templates that will later be used to build the vEdge device template.

---

## STEP 22 - Navigate to Feature Templates

From the **vManage GUI**, navigate to:

```text
☰ Menu → Configuration → Templates
```

<img width="500" height="204" alt="Navigate to Configuration Templates" src="https://github.com/user-attachments/assets/ed2b9619-0b85-4359-a395-70714086ff18" />

Select **Feature Templates**.

<img width="499" height="53" alt="Feature Templates Tab" src="https://github.com/user-attachments/assets/c2bf5f7b-aad3-4139-94ec-4d4f621121fe" />

This section is used to create individual configuration components that can later be combined into a device template.

---

## STEP 23 - Create the VE-SYSTEM Feature Template

Click **Add Template**.

<img width="227" height="102" alt="Add Feature Template Button" src="https://github.com/user-attachments/assets/63b78b73-4e2b-49db-ab26-54584db1a391" />

Use the search field to locate the following device type:

```text
vEdge Cloud
```

<img width="300" height="131" alt="Search for vEdge Cloud" src="https://github.com/user-attachments/assets/f7f92edc-c9d3-46a3-9281-a5957c379e45" />

Select **vEdge Cloud**.

Under **Basic Information**, select the **System** feature.

<img width="1045" height="382" alt="Select vEdge System Feature" src="https://github.com/user-attachments/assets/49a730e1-f836-4542-b415-18094e591ec2" />

Configure the template with the following values:

```text
Name: VE-SYSTEM
Description: VE-SYSTEM

BASIC INFORMATION:
Console Baud Rate (bps): 9600
```

Enter the template name and description:

<img width="402" height="95" alt="VE-SYSTEM Template Name and Description" src="https://github.com/user-attachments/assets/564735e5-483e-41a0-838e-9b25a4d5a02e" />

Under **Basic Information**, configure the **Console Baud Rate** as shown below:

<img width="1402" height="577" alt="VE-SYSTEM Basic Information Configuration" src="https://github.com/user-attachments/assets/89693100-c20a-477b-b0db-2899cc436296" />

Once the configuration is complete, click **Save**.

<img width="186" height="42" alt="Save Feature Template" src="https://github.com/user-attachments/assets/06500de0-2586-4f6b-91d4-c18a3fa360da" />

### Expected Output

The `VE-SYSTEM` feature template should now appear in the Feature Templates list.

<img width="1428" height="226" alt="VE-SYSTEM Feature Template Expected Output" src="https://github.com/user-attachments/assets/49b0cd84-a92a-4f45-b9cd-e3f6a230bae1" />

---

## STEP 24 - Create the VE-BANNER Feature Template

Create another feature template by clicking **Add Template**.

Follow this path:

```text
Add Template
    ↓
Search for "vEdge Cloud"
    ↓
Select "vEdge Cloud"
    ↓
Scroll down to "Other Templates"
    ↓
Select "Banner"
```

Under **Other Templates**, select the **Banner** feature.

<img width="1039" height="483" alt="Select vEdge Banner Feature" src="https://github.com/user-attachments/assets/36f7b5c7-c06e-4940-a83c-d0d5c17303c5" />

Configure the Banner feature template using the following values:

```text
Name: VE-BANNER
Description: VE-BANNER

Login Banner:
Global: Welcome to RIVANCORP

MOTD Banner:
Global: Property owned by RIVANCORP
```

Configure the **Login Banner** and **MOTD Banner** as shown below:

<img width="566" height="400" alt="VE-BANNER Configuration" src="https://github.com/user-attachments/assets/28ca6438-79a1-44ef-9062-7aece66eb5f9" />

Once the configuration is complete, click **Save**.

### Expected Output

The `VE-BANNER` feature template should now appear in the Feature Templates list.

<img width="1425" height="170" alt="VE-BANNER Feature Template Expected Output" src="https://github.com/user-attachments/assets/8a959ee9-342f-4f21-8eaa-6a30ca5af872" />

---

## STEP 25 - Create the VE-VPN0 Feature Template

Create another feature template by clicking **Add Template**.

Follow this path:

```text
Add Template
    ↓
Search for "vEdge Cloud"
    ↓
Select "vEdge Cloud"
    ↓
Scroll down to "VPN"
    ↓
Select "VPN"
```

Under the **VPN** section, select the **VPN** feature.

<img width="1029" height="373" alt="Select vEdge VPN Feature" src="https://github.com/user-attachments/assets/410b8c46-1654-4f4c-8971-4b3939f35cc8" />

Configure the template using the following values:

```text
Name: VE-VPN0
Description: VE-VPN0

VPN: 0
Name:
  Global: TRANSPORT VPN

IPv4 Route:
  Prefix: 0.0.0.0/0
  Gateway: Next Hop

Next Hop:
  Address: 192.168.20.6
```

Enter the template information and configure **VPN 0** as the transport VPN.

<img width="537" height="440" alt="VE-VPN0 Basic Configuration" src="https://github.com/user-attachments/assets/4c0bb180-a21c-4084-8995-7847f368bf11" />

---

## STEP 26 - Configure the Default Route for VE-VPN0

Scroll down to the **IPv4 Route** section.

Click **New IPv4 Route**.

<img width="1393" height="271" alt="New IPv4 Route Button" src="https://github.com/user-attachments/assets/7e6e1403-a5ce-4e5a-bf62-aa267a5cab9c" />

For the route prefix, enter:

```text
0.0.0.0/0
```

<img width="359" height="56" alt="Configure Default Route Prefix" src="https://github.com/user-attachments/assets/8781b18f-b7a7-48c8-9c70-b7cafce4f2f5" />

Set the gateway type to **Next Hop**.

Then click **Add Next Hop**.

<img width="573" height="225" alt="Add Next Hop for Default Route" src="https://github.com/user-attachments/assets/ed3d54a9-8269-40be-81f1-1c1a64d1c3d8" />

Enter the following next-hop address:

```text
192.168.20.6
```

<img width="622" height="277" alt="Configure Next Hop Address" src="https://github.com/user-attachments/assets/b22287fa-811e-4203-89df-c41bb892b2dd" />

> [!IMPORTANT]
> Enter only the next-hop IP address:
>
> ```text
> 192.168.20.6
> ```
>
> **Do not include a subnet mask or prefix length** in the Next Hop Address field.

Click **Add** to add the next-hop address.

Click **Add** again to add the completed IPv4 route to the `VE-VPN0` template.

### Expected Output - IPv4 Route Added

After clicking **Add**, verify that the default route has been successfully added.

The configured route should show:

```text
Prefix: 0.0.0.0/0
Gateway: Next Hop
Next Hop Address: 192.168.20.6
```

<img width="1390" height="192" alt="VE-VPN0 IPv4 Route Added" src="https://github.com/user-attachments/assets/d6a3e8b7-e743-4648-8245-f79473bb126f" />

Return to the `VE-VPN0` template configuration page and verify that the newly created IPv4 route appears under the **IPv4 Route** section.

> [!IMPORTANT]
> Before saving the template, verify that the default route is `0.0.0.0/0` and the next-hop address is `192.168.20.6`.

Once the IPv4 route has been verified, click **Save** to create the `VE-VPN0` feature template.

### Expected Output - VE-VPN0 Feature Template

After saving, the `VE-VPN0` feature template should appear in the **Feature Templates** list.

<img width="1426" height="214" alt="VE-VPN0 Feature Template Expected Output" src="https://github.com/user-attachments/assets/60e8cbe0-1014-4e4b-a44f-682e06f03ed8" />

---

## STEP 27 - Create the VE-VPN512 Feature Template

Create the **VPN 512 Management VPN** feature template.

From the **Feature Templates** page, navigate to:

```text
Add Template
    ↓
vEdge Cloud
    ↓
VPN
    ↓
VPN
```

Select the **VPN** feature under the VPN section.

<img width="1033" height="377" alt="Select VPN Feature for VPN 512" src="https://github.com/user-attachments/assets/f326a82e-67e4-4b46-9062-6932ae5f6813" />

Configure the feature template using the following values:

```text
Name: VE-VPN512
Description: VE-VPN512

VPN: 512

Name:
  Global: MANAGEMENT VPN
```

<img width="555" height="439" alt="VE-VPN512 Configuration" src="https://github.com/user-attachments/assets/befb02c9-f1b5-439f-9e33-15a138a975c1" />

Once the configuration is complete, click **Save**.

### Expected Output

Verify that the `VE-VPN512` feature template appears in the **Feature Templates** list.

<img width="1427" height="227" alt="VE-VPN512 Expected Output" src="https://github.com/user-attachments/assets/15d58664-0310-46a4-9c39-8e744100d3b6" />

---

## STEP 28 - Create the VPN512-ETH0 Management Interface Template

Create an Ethernet interface feature template for the **Management VPN (VPN 512)**.

Navigate to:

```text
Add Template
    ↓
vEdge Cloud
    ↓
VPN
    ↓
VPN Interface Ethernet
```

<img width="1028" height="492" alt="Select VPN Interface Ethernet Feature" src="https://github.com/user-attachments/assets/0eaced19-8b6f-41e2-894f-4579ef470640" />

Configure the template using the following values:

```text
Name: VPN512-ETH0
Description: VPN512-ETH0

Shutdown:
  Global: No

Interface Name: eth0

Description:
  Global: MANAGEMENT INTERFACE

IPv4 Address:
  Default
```

<img width="772" height="561" alt="VPN512-ETH0 Management Interface Configuration" src="https://github.com/user-attachments/assets/5d52f4e2-7c8f-49c2-a09e-1863287215c9" />

> [!IMPORTANT]
> The interface name must be:
>
> ```text
> eth0
> ```
>
> Make sure the correct interface is specified before saving the template.

Once the configuration is complete, click **Save**.

### Expected Output

Verify that the `VPN512-ETH0` feature template appears in the **Feature Templates** list.

<img width="1428" height="267" alt="VPN512-ETH0 Expected Output" src="https://github.com/user-attachments/assets/a06bb884-b5c4-4a3a-8617-08c0c509c7c3" />

---

## STEP 29 - Create the INT-VPN0-GE01 Transport Interface Template

Create the `ge0/1` transport interface for **VPN 0**.

Navigate to:

```text
Add Template
    ↓
vEdge Cloud
    ↓
VPN
    ↓
VPN Interface Ethernet
```

<img width="1028" height="492" alt="Select VPN Interface Ethernet for GE01" src="https://github.com/user-attachments/assets/0eaced19-8b6f-41e2-894f-4579ef470640" />

Configure the feature template using the following values:

```text
Name: INT-VPN0-GE01
Description: INT-VPN0-GE01

Shutdown:
  Global: No

Interface Name: ge0/1

Description:
  Global: TRANSPORT INTERFACE

IPv4 Address:
  Device Specific
  Key: ge0/1

Tunnel:
  Tunnel Interface:
    Global: On

  Color:
    Global: BIZ-INTERNET

  Restrict:
    Global: On

Allow Service:
  - All
  - NETCONF
  - SSH
  - BGP

NAT:
  Global: On
```

Configure the **Basic Interface** settings:

<img width="775" height="567" alt="GE01 Transport Interface Basic Configuration" src="https://github.com/user-attachments/assets/2f557538-5b54-4c47-9671-2e1a586d05bb" />

Configure the **Tunnel Interface**:

<img width="474" height="546" alt="GE01 Tunnel Interface Configuration" src="https://github.com/user-attachments/assets/de02e3b5-2dba-499d-97bb-b8a1fe8e4cd9" />

Configure the required **Allow Service** options:

<img width="472" height="506" alt="GE01 Allow Service Configuration" src="https://github.com/user-attachments/assets/5ed21180-d1c3-473c-a158-a0ac613b36b5" />

Enable **NAT**:

<img width="549" height="472" alt="GE01 NAT Configuration" src="https://github.com/user-attachments/assets/a09bd9f9-a652-4d59-9f6b-0ebe32ffba79" />

> [!IMPORTANT]
> The IPv4 address is configured as a **Device Specific** variable using the key `ge0/1`. The actual IP address will be assigned later when the device template is attached to each vEdge.

Once all settings have been configured, click **Save**.

### Expected Output

Verify that the `INT-VPN0-GE01` feature template appears in the **Feature Templates** list.

<img width="1434" height="252" alt="INT-VPN0-GE01 Expected Output" src="https://github.com/user-attachments/assets/d3a505ce-929f-4b83-b385-0d106833af58" />

---

## STEP 30 - Create the INT-VPN0-GE00 LAN Interface Template

Create the `ge0/0` interface for **VPN 0**.

Navigate to:

```text
Add Template
    ↓
vEdge Cloud
    ↓
VPN
    ↓
VPN Interface Ethernet
```

<img width="1028" height="492" alt="Select VPN Interface Ethernet for GE00" src="https://github.com/user-attachments/assets/0eaced19-8b6f-41e2-894f-4579ef470640" />

Configure the feature template using the following values:

```text
Name: INT-VPN0-GE00
Description: INT-VPN0-GE00

Shutdown:
  Global: No

Interface Name: ge0/0

Description:
  Global: LAN INTERFACE

IPv4 Address:
  Device Specific
  Key: ge0/0

Tunnel:
  Tunnel Interface:
    Global: On

  Color:
    Global: Private1

  Restrict:
    Global: On

Allow Service:
  - All
  - NETCONF
  - SSH
  - OSPF
```

Configure the **Basic Interface** settings:

<img width="776" height="527" alt="GE00 LAN Interface Basic Configuration" src="https://github.com/user-attachments/assets/005c066b-c735-4eb2-a562-421b29e54b00" />

Configure the **Tunnel Interface**:

<img width="473" height="550" alt="GE00 Tunnel Interface Configuration" src="https://github.com/user-attachments/assets/68bfe0c6-7705-4adc-ba10-e95762c0a9bd" />

Configure the required **Allow Service** options:

<img width="471" height="507" alt="GE00 Allow Service Configuration" src="https://github.com/user-attachments/assets/95090382-eef3-4435-87bd-6d6031cbcefb" />

> [!IMPORTANT]
> The IPv4 address is configured as a **Device Specific** variable using the key `ge0/0`. The actual IP address will be assigned when the device template is attached to each vEdge.

Once all settings have been configured, click **Save**.

### Expected Output

Verify that the `INT-VPN0-GE00` feature template appears in the **Feature Templates** list.

<img width="1413" height="319" alt="INT-VPN0-GE00 Expected Output" src="https://github.com/user-attachments/assets/58c2f187-02d4-4d69-83e9-80b32ec54da1" />

---

## STEP 31 - Create the BGP-VPN0 Feature Template

Create the BGP feature template that will be associated with **VPN 0**.

Navigate to:

```text
Add Template
    ↓
vEdge Cloud
    ↓
Other Templates
    ↓
BGP
```

<img width="1036" height="484" alt="Select BGP Feature Template" src="https://github.com/user-attachments/assets/fd964bf1-19be-49c2-8da1-ac40a27d61e3" />

Configure the BGP template using the following values:

```text
Name: BGP-VPN0
Description: BGP-VPN0

Shutdown:
  Global: No

AS Number:
  Global: 100

Neighbor:
  Address: 192.168.20.6

  Remote AS:
    Global: 1

  Address Family:
    Global: On

    Address Family: IPv4 Unicast
    Shutdown: No
```

Configure the basic BGP settings:

<img width="509" height="553" alt="BGP VPN0 Basic Configuration" src="https://github.com/user-attachments/assets/9b9fde1d-3746-4993-ac12-62dca8ff416c" />

Configure the BGP neighbor and address family:

<img width="496" height="582" alt="BGP VPN0 Neighbor Configuration" src="https://github.com/user-attachments/assets/f5242e4c-b98c-42e3-8fd5-6dd6cc483657" />

Verify the following important BGP parameters before saving:

| Parameter | Value |
| --- | --- |
| Local AS | `100` |
| Neighbor | `192.168.20.6` |
| Remote AS | `1` |
| Address Family | `IPv4 Unicast` |

Once the configuration is complete, click **Save**.

---

# vEdge Device Template Configuration

## STEP 32 - Create the vEdge Device Template

After creating the required feature templates, combine them into a **Device Template**.

From the vManage GUI, navigate to:

```text
☰ Menu → Configuration → Templates → Device Templates
```

<img width="1440" height="464" alt="vManage Device Templates Page" src="https://github.com/user-attachments/assets/ca19631e-8fa3-4827-8816-7d52e18cac05" />

Click:

```text
Create Template → From Feature Template
```

<img width="208" height="109" alt="Create Device Template from Feature Template" src="https://github.com/user-attachments/assets/a8f92ac8-6f0e-42f8-8157-0a6e0e555420" />

Configure the device template using the following information:

```text
Device Model: vEdge Cloud
Device Role: SDWAN Edge
Template Name: VE-TEMP
Description: VE-TEMP

Basic Information:
  System: VE-SYSTEM

Transport & Management VPN:

  VPN 0:
    VPN Template: VE-VPN0

    Additional VPN 0 Templates:
      BGP: BGP-VPN0
      VPN Interface: INT-VPN0-GE00
      VPN Interface: INT-VPN0-GE01

  VPN 512:
    VPN Template: VE-VPN512

    VPN Interface:
      VPN512-ETH0

Additional Templates:
  Banner: VE-BANNER
```

Configure the **Basic Information** section:

<img width="1387" height="425" alt="vEdge Device Template Basic Information" src="https://github.com/user-attachments/assets/354b77a5-6cd4-4acd-a5f1-1a82d5806742" />

Configure the **Transport & Management VPN** section:

<img width="1420" height="361" alt="vEdge Device Template VPN Configuration" src="https://github.com/user-attachments/assets/94015c95-620c-4925-8de0-498b735c5df2" />

Under **Additional Templates**, assign:

```text
Banner: VE-BANNER
```

<img width="375" height="187" alt="vEdge Device Template Banner Configuration" src="https://github.com/user-attachments/assets/d047c7c5-4079-4089-826a-966c4c142f93" />

> [!IMPORTANT]
> Make sure each feature template is assigned to the correct section before creating the device template.

Once all feature templates have been assigned, click **Create**.

<img width="191" height="34" alt="Create vEdge Device Template Button" src="https://github.com/user-attachments/assets/dbd80c6e-7401-48e7-9b15-20358b4d94d4" />

### Expected Output

The `VE-TEMP` device template should now appear in the **Device Templates** list.

<img width="1426" height="128" alt="VE-TEMP Device Template Expected Output" src="https://github.com/user-attachments/assets/4308a4ee-dcf5-4156-ad14-a32db5a76922" />

---

## STEP 33 - Attach the Device Template to the vEdge Devices

Locate the `VE-TEMP` device template.

Click the **three-dot menu (`...`)** associated with the template and select:

```text
Attach Devices
```

<img width="1425" height="194" alt="Attach Devices to VE-TEMP" src="https://github.com/user-attachments/assets/7099758c-0c70-4e70-be54-9c1e0823912f" />

Select the following vEdge devices:

```text
vEdge-LUZON
vEdge-VISAYAS
```

Click the **right arrow (`>`)** to move the selected devices to the attached devices section.

<img width="1073" height="456" alt="Select Luzon and Visayas vEdge Devices" src="https://github.com/user-attachments/assets/32248bab-51f6-427a-808b-93f19d6a7f28" />

After selecting the devices, vManage will redirect you to the device-template configuration page.

<img width="1432" height="214" alt="vEdge Device Template Attachment Page" src="https://github.com/user-attachments/assets/8d81a013-084b-4f15-9c22-301d2914551a" />

---

## STEP 34 - Configure Device-Specific Variables

The feature templates contain **Device Specific** variables that require unique values for each vEdge.

Click the **three-dot menu (`...`)** for each device to edit its device-specific information.

<img width="1414" height="67" alt="Edit vEdge Device Specific Variables" src="https://github.com/user-attachments/assets/e7024f02-4f4a-4e52-a4c9-bf0d8e56491f" />

Configure the devices using the following values.

### vEdge-LUZON

```text
IPv4 Address (ge0/1): 192.168.20.1/24
IPv4 Address (ge0/0): 172.16.1.1/30

Hostname (system_host_name): vEdge-LUZON
System IP (system_system_ip): 10.1.21.1
Site ID (system_site_id): 21
```

### vEdge-VISAYAS

```text
IPv4 Address (ge0/1): 192.168.20.5/24
IPv4 Address (ge0/0): 172.16.5.1/30

Hostname (system_host_name): vEdge-VISAYAS
System IP (system_system_ip): 10.1.25.1
Site ID (system_site_id): 25
```

> [!NOTE]
> Refer to the **EVE-NG topology** and verify the interface addressing before applying the device-specific values.

### Device-Specific Configuration Summary

| Parameter | vEdge-LUZON | vEdge-VISAYAS |
| --- | --- | --- |
| `ge0/1` | `192.168.20.1/24` | `192.168.20.5/24` |
| `ge0/0` | `172.16.1.1/30` | `172.16.5.1/30` |
| Hostname | `vEdge-LUZON` | `vEdge-VISAYAS` |
| System IP | `10.1.21.1` | `10.1.25.1` |
| Site ID | `21` | `25` |

### Expected Output

After entering the device-specific values, verify that all required fields have been populated.

<img width="1412" height="109" alt="vEdge Device Specific Variables Expected Output" src="https://github.com/user-attachments/assets/15089aaa-dc60-4dfb-94d3-ba65e538dafa" />

Once all values have been verified, click **Next**.

<img width="186" height="33" alt="Next Button for Device Template Configuration" src="https://github.com/user-attachments/assets/c3adbfea-6349-4fe9-85b3-8f4a4f93153d" />

---

## STEP 35 - Review the Generated vEdge Configuration

Before deploying the template, select one of the devices from the **left-side device list**.

Review the generated configuration and verify that the values match the intended configuration.

<img width="1433" height="656" alt="Review Generated vEdge Configuration" src="https://github.com/user-attachments/assets/0a4dcebd-8c5c-47d5-9e01-b78fd6128851" />

Verify the following before proceeding:

- The correct hostname is assigned.
- The correct System IP is assigned.
- The correct Site ID is assigned.
- `ge0/0` has the correct device-specific IP address.
- `ge0/1` has the correct device-specific IP address.
- VPN `0` contains the correct transport configuration.
- VPN `512` contains the management interface.
- BGP AS `100` is configured.
- BGP neighbor `192.168.20.6` uses remote AS `1`.
- The appropriate tunnel colors and services are configured.

> [!IMPORTANT]
> Review the generated configuration for **both vEdge-LUZON and vEdge-VISAYAS** before deploying the template.

---

## STEP 36 - Deploy the Device Template

Once the generated configurations have been verified, click **Configure Devices**.

<img width="320" height="41" alt="Configure Devices Button" src="https://github.com/user-attachments/assets/582a6079-b73f-48b5-8f44-637865a16b4f" />

A confirmation window will appear.

Select the confirmation checkbox, then click **OK**.

<img width="575" height="228" alt="Confirm Device Template Deployment" src="https://github.com/user-attachments/assets/4f9ef40a-49f3-40f3-bf87-3c699fbc17a2" />

### Expected Output

vManage will begin validating and deploying the device template to the selected vEdge devices.

<img width="1433" height="237" alt="vEdge Device Template Deployment Expected Output" src="https://github.com/user-attachments/assets/dfcc822d-91c1-4e21-8dff-63601e75f34b" />

> [!NOTE]
> Validation and deployment may take approximately **1 minute**.
>
> Wait until vManage reports **Validation Success** before proceeding.

---

## STEP 37 - Verify the vEdge Devices Through the CLI

After the device template has been successfully deployed, access **vEdge-LUZON** and **vEdge-VISAYAS** through SecureCRT using Telnet.

Use the following connection information:

```text
IP Address: 208.8.8.187
Protocol: Telnet
```

| Device | Telnet Port |
| --- | ---: |
| vEdge-LUZON | `32900` |
| vEdge-VISAYAS | `32903` |

### Expected Output

After connecting to the vEdge CLI, the device should display the configured hostname and login prompt.

<img width="505" height="126" alt="vEdge CLI Expected Output After Template Deployment" src="https://github.com/user-attachments/assets/19496848-c9eb-4989-b14a-2440b88a1ef5" />

> [!IMPORTANT]
> After the vEdge devices are configured through the **vManage device template**, the local login credentials used in this lab revert to the default credentials:
>
> ```text
> Username: admin
> Password: admin
> ```

---

# OSPF and Service VPN Configuration

## STEP 38 - Start the CSW-LUZON and CSW-VISAYAS Nodes

The next stage is to configure **OSPF** between the vEdge devices and their respective campus switches.

The final goal is to establish routing between the Luzon and Visayas sites so that their **Loopback 0 interfaces can communicate with each other**.

Start the following nodes in the EVE-NG topology:

- `CSW-LUZON`
- `CSW-VISAYAS`

<img width="674" height="183" alt="Start CSW-LUZON and CSW-VISAYAS" src="https://github.com/user-attachments/assets/6adc8e17-335d-4a4f-ad80-ae3a0e94abb2" />

---

## STEP 39 - Access the CSW Devices Through SecureCRT

Access both CSW devices through **SecureCRT using Telnet**.

Use the following IP address:

```text
208.8.8.187
```

Use the appropriate Telnet port for each device:

| Device | Telnet Port |
| --- | ---: |
| CSW-LUZON | `32905` |
| CSW-VISAYAS | `32906` |

After connecting to each device, enter privileged EXEC mode:

```text
enable
```

---

## STEP 40 - Configure CSW-LUZON

Access the **CSW-LUZON** CLI and paste the following preconfiguration:

```text
!@CSW-LUZON
conf t
 hostname CSW-LUZON
 enable secret pass
 service password-encryption
 no logging console
 no ip domain lookup
 username admin priv 15 secret pass
 line vty 0 14
  transport input all
  password pass
  login local
  exec-timeout 0 0
 int lo0
  ip add 1.1.1.1 255.255.255.255
  exit
 int g0/0
  no sw
  ip add 172.16.1.2 255.255.255.252
  no shut
 int g0/1
  no sw
  ip add 10.1.1.2 255.255.255.252
  no shut
 router ospf 1
  router-id 1.1.1.1
  network 172.16.1.0 0.0.0.3 area 0
  network 1.1.1.1 0.0.0.0 area 0
  network 10.1.1.0 0.0.0.3 area 0
  passive-interface lo0
  end
```

### CSW-LUZON Configuration Summary

| Component | Configuration |
| --- | --- |
| Hostname | `CSW-LUZON` |
| Loopback 0 | `1.1.1.1/32` |
| GigabitEthernet0/0 | `172.16.1.2/30` |
| GigabitEthernet0/1 | `10.1.1.2/30` |
| OSPF Process | `1` |
| OSPF Router ID | `1.1.1.1` |
| OSPF Area | `0` |

### Expected Output

<img width="1440" height="716" alt="CSW-LUZON Configuration Expected Output" src="https://github.com/user-attachments/assets/90277d7e-793c-4cc3-a436-d22e412003fc" />

---

## STEP 41 - Configure CSW-VISAYAS

Access the **CSW-VISAYAS** CLI and paste the following preconfiguration:

```text
!@CSW-VISAYAS
conf t
 hostname CSW-VISAYAS
 enable secret pass
 service password-encryption
 no logging console
 no ip domain lookup
 username admin priv 15 secret pass
 line vty 0 14
  transport input all
  password pass
  login local
  exec-timeout 0 0
 int lo0
  ip add 2.2.2.2 255.255.255.255
  exit
 int g0/0
  no sw
  ip add 172.16.5.2 255.255.255.252
  no shut
 int g0/1
  no sw
  ip add 10.1.2.2 255.255.255.252
  no shut
 router ospf 1
  router-id 2.2.2.2
  network 172.16.5.0 0.0.0.3 area 0
  network 2.2.2.2 0.0.0.0 area 0
  network 10.1.2.0 0.0.0.3 area 0
  passive-interface lo0
  end
```

### CSW-VISAYAS Configuration Summary

| Component | Configuration |
| --- | --- |
| Hostname | `CSW-VISAYAS` |
| Loopback 0 | `2.2.2.2/32` |
| GigabitEthernet0/0 | `172.16.5.2/30` |
| GigabitEthernet0/1 | `10.1.2.2/30` |
| OSPF Process | `1` |
| OSPF Router ID | `2.2.2.2` |
| OSPF Area | `0` |

### Expected Output

<img width="1440" height="707" alt="CSW-VISAYAS Configuration Expected Output" src="https://github.com/user-attachments/assets/3f876269-18c2-4c9b-ba34-d07fd2894efd" />

> [!NOTE]
> At this stage, both CSW devices have their local OSPF configurations. The vEdge configuration must now be updated to provide the required service VPN and OSPF connectivity between the sites.

---

# vEdge Service VPN Configuration

## STEP 42 - Create the VE-VPN1 Feature Template

Create a new feature template for **VPN 1**, which will be used as the **Data/Service VPN**.

From the vManage GUI, navigate to:

```text
☰ Menu
    ↓
Configuration
    ↓
Templates
    ↓
Feature Templates
    ↓
Add Template
    ↓
vEdge Cloud
    ↓
VPN
    ↓
VPN
```

Select the **VPN** feature.

<img width="1029" height="373" alt="Select vEdge VPN Feature" src="https://github.com/user-attachments/assets/410b8c46-1654-4f4c-8971-4b3939f35cc8" />

Configure the feature template using the following values:

```text
Name: VE-VPN1
Description: VE-VPN1

VPN: 1

Name:
  Global: DATA VPN

IPv4 Route:
  Prefix: 0.0.0.0/0
  Gateway: VPN

  Enable VPN:
    Global: On
```

Configure the basic VPN information:

<img width="549" height="474" alt="VE-VPN1 Basic Configuration" src="https://github.com/user-attachments/assets/48bea3d9-2296-4ca6-9df1-934a6279416c" />

Under the **IPv4 Route** section, configure the default route:

```text
Prefix: 0.0.0.0/0
Gateway: VPN
Enable VPN: On
```

<img width="1405" height="312" alt="VE-VPN1 IPv4 Route Configuration" src="https://github.com/user-attachments/assets/4c0b9eb8-ae87-416f-b307-542d9b619bb1" />

Click **Add** to add the IPv4 route.

### Expected Output - IPv4 Route Added

Verify that the route appears under the **IPv4 Route** section.

<img width="1405" height="191" alt="VE-VPN1 IPv4 Route Expected Output" src="https://github.com/user-attachments/assets/fe85a242-9d36-4683-a859-a8ee2df2fa5e" />

Once the route has been verified, click **Save**.

### Expected Output - VE-VPN1

The `VE-VPN1` feature template should now appear in the **Feature Templates** list.

<img width="1401" height="44" alt="VE-VPN1 Feature Template Expected Output" src="https://github.com/user-attachments/assets/b891f9d3-8022-4c56-9205-77c1a57df1b1" />

---

## STEP 43 - Create the OSPF-VPN1 Feature Template

Create an OSPF feature template for **VPN 1**.

Navigate to:

```text
Feature Templates
    ↓
Add Template
    ↓
vEdge Cloud
    ↓
Other Templates
    ↓
OSPF
```

Select the **OSPF** feature.

<img width="1032" height="488" alt="Select vEdge OSPF Feature Template" src="https://github.com/user-attachments/assets/aaeabca2-0b5b-44e1-a5e2-54771fdf4d27" />

Configure the template using the following values:

```text
Name: OSPF-VPN1
Description: OSPF-VPN1

Redistribute:
  Protocol: OMP

Area:
  Area Number: 0

  Interface:
    Interface Name: ge0/0

Advanced:
  Originate:
    Global: On

  Always:
    On
```

---

## STEP 44 - Configure OMP Redistribution into OSPF

Under the **Redistribute** section, select:

```text
Protocol: OMP
```

<img width="652" height="197" alt="Configure OMP Redistribution into OSPF" src="https://github.com/user-attachments/assets/1ebba6cd-c874-4df9-85f7-41ae1e93ebf7" />

Click **Add** to add the redistribution configuration.

The OMP redistribution entry should now appear in the template:

<img width="1404" height="263" alt="OMP Redistribution Expected Output" src="https://github.com/user-attachments/assets/f5f83f62-c3b9-4bee-9073-c764e5bf609c" />

> [!NOTE]
> Redistributing **OMP into OSPF** allows routes learned through the SD-WAN overlay to be advertised toward the local OSPF domain.

---

## STEP 45 - Configure OSPF Area 0 and Interface ge0/0

Under the **Area** section, click:

```text
New Area
```

Configure the OSPF area as:

```text
Area Number: 0
```

Under the interface section, click **Add Interface**.

<img width="494" height="279" alt="Create OSPF Area 0" src="https://github.com/user-attachments/assets/e32b84b8-3e2f-4b5d-a480-076657443ebf" />

Configure the interface as:

```text
Interface Name: ge0/0
```

<img width="588" height="420" alt="Configure ge0/0 OSPF Interface" src="https://github.com/user-attachments/assets/f2b00635-6c5b-4626-97eb-d8973f0f10f4" />

Verify the interface configuration:

<img width="579" height="433" alt="Verify OSPF ge0/0 Interface" src="https://github.com/user-attachments/assets/deea0375-e577-4375-8f6a-2c551b2cc528" />

Click **Add** to add the interface.

Then click **Add** again to add **Area 0** to the OSPF template.

### Expected Output

The OSPF Area 0 configuration should appear similar to the following:

<img width="1391" height="235" alt="OSPF Area 0 Expected Output" src="https://github.com/user-attachments/assets/ab10190e-5e4e-4178-ab61-408ae7e4f0d9" />

---

## STEP 46 - Enable OSPF Default Route Origination

Scroll down to the **Advanced** section.

Configure the following options:

```text
Originate:
  Global: On

Always:
  On
```

<img width="475" height="465" alt="Enable OSPF Default Route Origination" src="https://github.com/user-attachments/assets/092fe814-cba5-4c00-b028-6beca334aaf5" />

Once all OSPF settings have been configured, click **Save**.

### Expected Output

Verify that the `OSPF-VPN1` feature template appears in the **Feature Templates** list.

<img width="1376" height="36" alt="OSPF-VPN1 Feature Template Expected Output" src="https://github.com/user-attachments/assets/4f7fd16e-1a5f-40f7-80c7-41b778b2040c" />

---

# Update the vEdge Device Template

## STEP 47 - Edit the VE-TEMP Device Template

The existing `VE-TEMP` device template must now be updated to include **VPN 1** and **OSPF**.

Navigate to:

```text
☰ Menu
    ↓
Configuration
    ↓
Templates
    ↓
Device Templates
    ↓
VE-TEMP
    ↓
...
    ↓
Edit
```

<img width="1428" height="420" alt="Edit VE-TEMP Device Template" src="https://github.com/user-attachments/assets/2dff34cd-f014-4ce0-a1e3-7db0948eb742" />

The target configuration should be:

```text
Service VPN:
  VPN: VE-VPN1

  Additional Templates:
    OSPF: OSPF-VPN1
    VPN Interface: INT-VPN0-GE00

Transport VPN:
  VPN: VE-VPN0

  VPN Interface:
    INT-VPN0-GE01

Management VPN:
  VPN: VE-VPN512

  VPN Interface:
    VPN512-ETH0
```

> [!IMPORTANT]
> The `ge0/0` interface template must be moved from **VPN 0** to **VPN 1**.
>
> `ge0/0` will be used for the LAN/service-side OSPF connection, while `ge0/1` remains associated with the transport VPN.

---

## STEP 48 - Add VE-VPN1 to the Service VPN

Under **Service VPN**, click **Add VPN**.

Select:

```text
VE-VPN1
```

Click the **right arrow (`>`)** to move the template to the selected section.

<img width="1072" height="238" alt="Add VE-VPN1 to Service VPN" src="https://github.com/user-attachments/assets/6f78e12e-d1e2-4983-b24d-c19f2f25f0c9" />

Confirm the VPN selection:

<img width="180" height="47" alt="Confirm VE-VPN1 Selection" src="https://github.com/user-attachments/assets/b4b6b24d-3a5c-46d6-8465-93e5863493ef" />

---

## STEP 49 - Add OSPF and ge0/0 to VPN 1

Under the newly added **VE-VPN1** service VPN, add the following templates:

```text
OSPF:
  OSPF-VPN1

VPN Interface:
  INT-VPN0-GE00
```

Select **OSPF** and **VPN Interface**.

<img width="1078" height="314" alt="Add OSPF and VPN Interface to VPN1" src="https://github.com/user-attachments/assets/f91dc900-a26a-4700-a1fb-fdff63dc602c" />

Click **Add**.

<img width="164" height="54" alt="Add VPN1 Feature Templates" src="https://github.com/user-attachments/assets/0873bd8e-41b3-461b-8a93-858fcb81067f" />

### Expected Output

Verify that VPN 1 contains the correct feature templates.

<img width="1431" height="274" alt="VE-TEMP VPN1 Expected Output" src="https://github.com/user-attachments/assets/e753e840-ba3b-4ecb-89ce-ea073e9527b1" />

> [!IMPORTANT]
> Verify that `INT-VPN0-GE00` is no longer assigned under **VPN 0** before updating the device template.
>
> The intended interface assignment is:
>
> | Interface | VPN | Purpose |
> | --- | ---: | --- |
> | `ge0/0` | VPN `1` | LAN / Service-side OSPF |
> | `ge0/1` | VPN `0` | Transport / WAN |
> | `eth0` | VPN `512` | Management |

Once the template assignments have been verified, click **Update**.

---

## STEP 50 - Re-enter the Device-Specific ge0/0 Addresses

After updating `VE-TEMP`, vManage will redirect you to the device-template attachment page.

<img width="1435" height="167" alt="vEdge Template Device Specific Configuration" src="https://github.com/user-attachments/assets/309bcc45-121f-4bd6-b617-afb6c57e8a46" />

Because `ge0/0` uses a **Device Specific** variable, re-enter the appropriate IPv4 address for each vEdge.

<img width="1435" height="159" alt="Re-enter vEdge ge0/0 IPv4 Addresses" src="https://github.com/user-attachments/assets/5b288803-2ccb-46e1-919c-3cfd444e1781" />

Use the following addresses:

| Device | `ge0/0` IPv4 Address |
| --- | --- |
| vEdge-LUZON | `172.16.1.1/30` |
| vEdge-VISAYAS | `172.16.5.1/30` |

> [!NOTE]
> Verify the addresses against the EVE-NG topology before deploying the updated template.

---

## STEP 51 - Deploy the Updated VE-TEMP Configuration

After entering the device-specific values, proceed with the deployment:

```text
Next
  ↓
Configure Devices
  ↓
Select the Confirmation Checkbox
  ↓
OK
```

Wait for vManage to validate and push the updated configuration to both vEdge devices.

### Expected Output

The deployment should complete successfully.

<img width="1428" height="239" alt="Updated VE-TEMP Validation Success" src="https://github.com/user-attachments/assets/6967307a-d3f5-4ace-86d8-dd1c8c9d26a9" />

> [!IMPORTANT]
> Wait until the deployment reports **Validation Success** before testing connectivity.

---

# OSPF Connectivity Verification

## STEP 52 - Verify Loopback Connectivity Between Luzon and Visayas

Return to **SecureCRT** and access both campus switches.

### From CSW-LUZON

Ping the **CSW-VISAYAS Loopback 0** address:

```text
ping 2.2.2.2
```

### Expected Output

The ping should succeed:

<img width="625" height="201" alt="CSW-LUZON Ping to CSW-VISAYAS Loopback" src="https://github.com/user-attachments/assets/baa74cb6-c2cc-48cd-a351-5307aecd894a" />

### From CSW-VISAYAS

Ping the **CSW-LUZON Loopback 0** address:

```text
ping 1.1.1.1
```

### Expected Output

The ping should succeed:

<img width="617" height="165" alt="CSW-VISAYAS Ping to CSW-LUZON Loopback" src="https://github.com/user-attachments/assets/d42f692f-6e60-42eb-803f-5161895c2da8" />

> [!IMPORTANT]
> Successful bidirectional ping between `1.1.1.1` and `2.2.2.2` confirms that the Luzon and Visayas sites have end-to-end Layer 3 reachability across the SD-WAN environment.

---


















