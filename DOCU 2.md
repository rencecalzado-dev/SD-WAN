# Cisco SD-WAN Lab in EVE-NG

## Beginner-Friendly, Step-by-Step Deployment Guide

This lab explains how to start an existing Cisco SD-WAN topology in EVE-NG, configure the vManage, vBond, and vSmart controllers, configure BGP on the Cloud router, and begin a vEdge feature template in vManage.

> [!IMPORTANT]
> This guide assumes the EVE-NG topology, device images, cabling, and initial addressing already exist. It does not build a topology from an empty lab.

## Lab Overview

| Device | Purpose | Access |
| --- | --- | --- |
| EVE-NG | Hosts the virtual lab | `208.8.8.187` |
| vManage | Management controller | Telnet port `32897` |
| vSmart | Control-plane controller | Telnet port `32898` |
| vBond | Orchestration controller | Telnet port `32899` |
| Cloud | WAN/BGP router | EVE-NG console |
| vEdge-LUZON | SD-WAN edge router | Telnet port `32900` |
| vEdge-VISAYAS | SD-WAN edge router | Telnet port `32903` |

## Before You Begin

You need:

- Access to the existing EVE-NG lab
- SecureCRT or another Telnet client
- A browser that can reach vManage
- The credentials supplied by your lab administrator
- About 10–15 minutes for the controllers to start

> [!CAUTION]
> The original lab credentials are included because they are required for this isolated training environment. Never reuse them in production.

## Addressing Reference

| Device | System IP | VPN 0 address | VPN 512 address | Default gateway |
| --- | --- | --- | --- | --- |
| vSmart | `10.1.10.1` | `172.16.10.1/29` | — | `172.16.10.6` |
| vManage | `10.1.10.2` | `172.16.10.2/29` | `10.69.255.13/30` | VPN 0: `172.16.10.6`; VPN 512: `10.69.255.14` |
| vBond | `10.1.10.3` | `172.16.10.3/29` | — | `172.16.10.6` |

All controllers use organization name `RIVANCORP` and site ID `10`.

---

## Step 1 — Open the EVE-NG Lab

1. Browse to `208.8.8.187`.
2. Sign in:

   ```text
   Username: Rivan
   Password: C1sc0123
   ```

![EVE-NG login](https://github.com/user-attachments/assets/0cc44e04-290e-4df5-88a5-d2172421f71c)

## Step 2 — Add a Route from the Windows PC

1. Open **Command Prompt as Administrator**.
2. Run:

   ```bat
   route add 10.69.255.13 mask 255.255.255.255 10.69.255.6
   ```

3. Verify it:

   ```bat
   route print 10.69.255.13
   ```

> [!NOTE]
> This route is temporary and disappears after a Windows restart. Add `-p` only if your lab administrator permits a persistent route.

![Add the Windows route](https://github.com/user-attachments/assets/6473ced0-4a90-4bc0-b0ea-b80976fd9bf7)

## Step 3 — Open the Existing Topology

1. Locate the assigned SD-WAN lab in EVE-NG.
2. Click **Open**.
3. Optionally enable dark mode.

![Open the lab](https://github.com/user-attachments/assets/33ca3948-4cd8-408f-8f6a-cd9e4704dced)

## Step 4 — Start the Controllers and Cloud Router

Start these nodes:

| Device | Action |
| --- | --- |
| vManage | Start |
| vSmart | Start |
| vBond | Start |
| Cloud | Start |

![Start the controllers](https://github.com/user-attachments/assets/2b3d1313-5d8d-4e9e-b0f1-1b7442334027)

Wait approximately **10 minutes** for them to boot.

> [!IMPORTANT]
> Do not start the vEdge routers yet. Configure and stabilize the controllers first.

## Step 5 — Connect to the Controller Consoles

Create SecureCRT Telnet sessions to the EVE-NG address with these ports:

| Device | Port |
| --- | ---: |
| vManage | `32897` |
| vSmart | `32898` |
| vBond | `32899` |

![SecureCRT sessions](https://github.com/user-attachments/assets/ba8d7253-62fe-4693-8c9b-6208e3a006d4)

Wait for the vManage console to display **System Ready** before signing in.

![vManage System Ready](https://github.com/user-attachments/assets/934afd23-c379-4419-bae2-b21d86c87722)

> [!CAUTION]
> The account may lock after five failed sign-in attempts. Check the credentials and keyboard layout before retrying.

## Step 6 — Configure vManage

Paste this into the **vManage console**:

```text
config
 system
  host-name Rivan-vManage
  site-id 10
  system-ip 10.1.10.2
  organization-name RIVANCORP
  vbond 172.16.10.3
  admin-tech-on-failure
 vpn 0
  interface eth0
   ip address 172.16.10.2/29
   no shutdown
   tunnel-interface
    allow-service all
  ip route 0.0.0.0/0 172.16.10.6
 vpn 512
  interface eth1
   ip address 10.69.255.13/30
   no shutdown
  ip route 0.0.0.0/0 10.69.255.14
 commit
end
```

### Verify vManage

```text
request nms all status
show system status
```

Confirm that the required NMS services are enabled and running.

![vManage service status](https://github.com/user-attachments/assets/d045e7db-bffa-42d4-a231-0dbc87c850ad)

If they are still starting, wait a few minutes and check again.

## Step 7 — Configure vBond

Paste this into the **vBond console**:

```text
config
 system
  host-name Rivan-vBond
  site-id 10
  system-ip 10.1.10.3
  organization-name RIVANCORP
  vbond 172.16.10.3 local vbond
  admin-tech-on-failure
 vpn 0
  interface ge0/0
   ip address 172.16.10.3/29
   no shutdown
   tunnel-interface
    encapsulation ipsec
    allow-service all
  ip route 0.0.0.0/0 172.16.10.6
 commit
end
```

## Step 8 — Configure vSmart

Paste this into the **vSmart console**:

```text
config
 system
  host-name Rivan-vSmart
  site-id 10
  system-ip 10.1.10.1
  organization-name RIVANCORP
  vbond 172.16.10.3
  admin-tech-on-failure
 vpn 0
  interface eth0
   ip address 172.16.10.1/29
   no shutdown
   tunnel-interface
    allow-service all
  ip route 0.0.0.0/0 172.16.10.6
 commit
end
```

> [!TIP]
> The organization name must match exactly on every controller and edge router. It is case-sensitive.

## Step 9 — Configure BGP on the Cloud Router

Open the **Cloud router console** and enter:

```text
enable
pass
configure terminal
 interface loopback8
  ip address 8.8.8.8 255.255.255.255
 exit
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
  exit-address-family
 end
```

Verify BGP:

```text
show ip bgp summary
show ip route bgp
```

The peers may remain down until their edge routers are running and configured.

## Step 10 — Check Controller Readiness

Before continuing, confirm that:

- vManage displays **System Ready**.
- vManage NMS services are running.
- vBond and vSmart committed without errors.
- Controller interfaces are up.

## Step 11 — Start the vEdge Routers

1. Start **vEdge-LUZON** and **vEdge-VISAYAS** in EVE-NG.
2. Open Telnet sessions in SecureCRT:

| Device | Port |
| --- | ---: |
| vEdge-LUZON | `32900` |
| vEdge-VISAYAS | `32903` |

![Start the vEdge routers](https://github.com/user-attachments/assets/a9eadbe9-cf21-487a-85dd-cb8117ad86b3)

> [!NOTE]
> The source notes do not include the CLI configuration for these vEdge routers. The edge configuration or complete device templates are required to finish the SD-WAN fabric.

## Step 12 — Sign In to vManage

1. Browse to `https://10.69.255.13:8443`.
2. Sign in:

   ```text
   Username: admin
   Password: C1sc0123
   ```

3. Continue past a certificate warning only after confirming this is the isolated lab server.

![vManage sign-in](https://github.com/user-attachments/assets/c957977a-b27f-405c-93f9-832cee6e7675)

## Step 13 — Send the Controller List

1. Open the upper-left menu.
2. Select **Configuration > Certificates**.

![Open Certificates](https://github.com/user-attachments/assets/9dee28da-d534-4887-a122-6d83eeb94189)

![Certificates page](https://github.com/user-attachments/assets/d49c4d4d-48ae-4a1f-8ad1-87390d09c306)

3. Click **Send to Controllers**.

![Send to Controllers](https://github.com/user-attachments/assets/b675f097-b41c-4c66-ac89-ff72b1234083)

4. Wait until the status becomes **Success**.

![Successful update](https://github.com/user-attachments/assets/cb8418a2-2663-4c7c-8036-f3df400cd7c0)

> [!IMPORTANT]
> If it fails, recheck reachability, organization names, system IPs, and the vBond configuration before continuing.

## Step 14 — Begin the vEdge Feature Template

1. Select **Configuration > Templates > Feature Templates**.

![Feature Templates](https://github.com/user-attachments/assets/0264b0c9-9b8f-4ab8-8f90-03fee2d0314b)

2. Click **Add Template**.

![Add Template](https://github.com/user-attachments/assets/2c455118-a8ca-4c8a-8542-7c223a19c72e)

3. Filter for **vEdge Cloud**.

![Filter for vEdge Cloud](https://github.com/user-attachments/assets/a95c0b11-7d2b-4893-b82e-6df959ea0820)

4. Select **Basic Information > System**.
5. Enter:

| Field | Value |
| --- | --- |
| Template name | `VE-SYSTEM` |
| Description | `VE-SYSTEM` |

6. Configure:

| Setting | Value type | Value |
| --- | --- | --- |
| Site ID | Device Specific | Entered when attached |
| System IP | Device Specific | Entered when attached |
| Hostname | Device Specific | Entered when attached |
| Console Baud Rate | Global | `9600` |

> [!NOTE]
> **Device Specific** values can differ per router. A **Global** value applies to every router using the template.

## Completion Check

You should now have:

- Configured vManage, vBond, and vSmart.
- Configured BGP on the Cloud router.
- Started both vEdge routers.
- Sent the controller list successfully.
- Begun the `VE-SYSTEM` feature template.



The source material ends here. Additional instructions are needed to configure the remaining vEdge features, build and attach a device template, and verify control connections and data-plane traffic.

## Quick Troubleshooting

| Problem | Check |
| --- | --- |
| vManage page does not open | Verify the Windows route, ping `10.69.255.13`, and check VPN 512 interface `eth1`. |
| vManage services are not running | Wait several minutes, then rerun `request nms all status`. |
| Controller-list update fails | Verify matching organization names, system IPs, vBond address, routes, and interfaces. |
| Configuration does not commit | Read the error and verify the interface name supported by the device image. |
| BGP neighbor is down | Check neighbor address, remote AS, reachability, and whether the peer router is running. |
