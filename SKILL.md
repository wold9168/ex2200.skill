---
name: maintain-ex2200
description: >
  Use this skill when the user needs to install, configure, maintain, or troubleshoot a Juniper EX2200 or EX2200-C switch.
  Triggers on any mention of EX2200, EX2200-C, Juniper switch setup, configuration, maintenance, hardware issues, LED status interpretation, transceiver replacement, Virtual Chassis setup, PoE troubleshooting, or RMA procedures for EX2200 series.
  Also use when the user asks about mounting, cabling, grounding, power connections, factory reset, or initial switch configuration of EX2200 series.
  This skill provides hardware specifications, CLI commands, LED meanings, alarm tables, and step-by-step procedures extracted from the official EX2200 Hardware Guide.
---

# Juniper EX2200 / EX2200-C Switch — Complete Maintenance & Configuration Guide

This skill covers every aspect of maintaining and configuring Juniper EX2200 and EX2200-C Ethernet switches, based on the official Juniper Networks hardware guide.

## Switch Models

| Model | Ports | PoE | Max PoE Power | First Junos OS |
|---|---|---|---|---|
| EX2200-C-12T-2G | 12 GE | No | — | 11.3R1 |
| EX2200-C-12P-2G | 12 GE | All 12 ports | 100 W | 11.3R1 |
| EX2200-24T-4G | 24 GE | No | — | 10.1R1 |
| EX2200-24P-4G | 24 GE | All 24 ports | 405 W | 10.1R1 |
| EX2200-24T-4G-DC | 24 GE (DC) | No | — | 10.1R1 |
| EX2200-48T-4G | 48 GE | No | — | 10.1R1 |
| EX2200-48P-4G | 48 GE | All 48 ports | 405 W | 10.1R1 |

**Key facts:**
- Virtual Chassis: up to 4 switches (Junos 12.2+)
- DC models do NOT support PoE
- EX2200-C is fanless; all other models have fans

## Hardware Terminology CLI Mapping

| CLI Item | CLI Value | Physical Component |
|---|---|---|
| Chassis | Model name | The switch chassis |
| FPC 0 | Always 0 | The switch itself |
| PIC 0 | 12/24/48 ports | Front-panel network ports (ge-0/0/0 through ge-0/0/N) |
| PIC 1 | 2 or 4 ports | Uplink ports (ge-0/1/0 through ge-0/1/3) |
| Xcvr (n) | Port number | Optical transceiver |
| Power Supply 0 | Always 0 | Built-in AC/DC power supply |
| Fan | — | Fan system (EX2200-C: fanless) |

## Front Panel Components

### EX2200 (non-C, 24/48 port)
- 24 or 48 10/100/1000BASE-T ports
- 4 built-in SFP uplink ports (usable as VCPs for Virtual Chassis)
- 2 chassis status LEDs (SYS, ALM)
- 4 port status mode LEDs (SPD, DX, EN, POE)
- Factory reset / Mode button

### EX2200-C (12 port)
- 12 10/100/1000BASE-T ports
- 2 dual-purpose uplink ports (each: 1 RJ-45 + 1 SFP; only one active at a time)
- 1 USB port, 1 Mini-USB console port, 1 RJ-45 console port, 1 MGMT Ethernet port
- 2 chassis status LEDs
- 4 port status mode LEDs (PoE+) or 3 (non-PoE)
- Factory reset / Mode button

**Interface naming convention:**
- EX2200 non-C: `ge-0/0/0` to `ge-0/0/23` (24-port) or `ge-0/0/0` to `ge-0/0/47` (48-port), uplinks `ge-0/1/0` to `ge-0/1/3`
- EX2200-C: `ge-0/0/0` to `ge-0/0/11`, uplinks `ge-0/1/0` to `ge-0/1/1`

## Chassis Status LEDs

| LED | State | Meaning |
|---|---|---|
| SYS — Green steady | On | Switch functioning normally |
| SYS — Green blinking | Blinking | Switch booting |
| SYS — Off | Off | Powered off or halted |
| ALM — Unlit | Off | No alarm / halted |
| ALM — Amber | On solid | Minor alarm (needs monitoring) |
| ALM — Red | On solid | Major alarm (immediate action required) |

To view via CLI: `show chassis led`

## Port LEDs

### Link/Activity LED (green, per port)
- Blinking: link active with traffic
- Steady on: link active, no traffic
- Off: port not active

### Status LED — toggled by Mode button, indicated by SPD/DX/EN/POE mode LEDs

| Mode | Network ports | Uplink (non-C SFP) | Uplink (EX2200-C dual-purpose) |
|---|---|---|---|
| Speed (SPD) | 1 blink=10M, 2 blinks=100M, 3 blinks=1000M | On=1000M, Off=10/100M | Same as network ports |
| Duplex (DX) | On=full, Off=half | Same | Same |
| Admin (EN) | On=enabled, Off=disabled | Same | Same |
| PoE (POE) | On=device drawing power, Blinking=PoE available no device, Off=unavailable | Always unlit | Always unlit |

PoE LED only present on: EX2200-C-12P, EX2200-24P, EX2200-48P.

## Rear Panel Components

### EX2200 (non-C)
- Management Ethernet port (me0)
- USB port
- Console port (RJ-45)
- RPS port
- Protective earthing terminal
- ESD point
- Air exhaust (2 with fan + 1 open on PoE / sealed on non-PoE)
- Serial number ID label
- AC power cord inlet (or DC terminals)

### EX2200-C
- Protective earthing terminal
- ESD point
- Serial number ID label
- AC power cord inlet
- Heatsink (PoE+ models only) — fanless, vents on top and sides

## Chassis Physical Specifications

| Spec | EX2200 | EX2200-C |
|---|---|---|
| Height | 1.75 in (4.45 cm), 1U | 1.75 in (4.4 cm), 1U |
| Width | 17.5 in (44.5 cm); 19 in (48.2 cm) w/ brackets | 10.6 in (26.92 cm); 19 in (48.26 cm) w/ brackets |
| Depth | 10.5 in (26.7 cm) | 12T: 9.0 in (22.8 cm); 12P: 9.4 in (23.8 cm) |
| Weight (lightest) | 24T/24T-DC: 6 lb (2.7 kg) | C-12T: 4.6 lb (2.1 kg) |
| Weight (heaviest) | 48P: 10 lb (4.5 kg) | C-12P: 6.4 lb (2.9 kg) |

## Mounting

### Supported methods

| Method | EX2200 | EX2200-C |
|---|---|---|
| Desk (rubber feet) | Yes | Yes |
| Desk (screws) | No | Yes |
| Two-post rack | Yes | Yes (brackets separately orderable) |
| Four-post rack | Yes (kit separate) | No |
| Recessed 2-in | Yes | No |
| Wall mount (screws) | Yes | Yes |
| Wall mount (kit) | Yes (separately orderable) | No |
| Magnet mount | No | Yes (kit separate) |

### General mounting rules
- Mount heaviest unit at bottom of rack, others bottom-to-top
- Two-person job recommended for rack mounting
- When mounting vertically on wall: front panel MUST face downward
- PoE models wall-mounted: install baffle above units
- EX2200-C: do NOT block top vents (overheating risk)

### Clearance requirements
- Front: 24 in (61 cm) minimum for maintenance
- Rear: 6 in (15.2 cm) for airflow
- Side (between devices with fans): 6 in (15.2 cm)
- Side (to non-heat-producing surface): 2.8 in (7 cm)
- Power cord retainer clip extends 3 in (7.6 cm) from rear

## Power System

### AC Power Specifications

| Parameter | Value |
|---|---|
| Input voltage | 100–240 VAC |
| Frequency | 50/60 Hz |
| Current (PoE models) | 7 A at 100 VAC, 2.9 A at 230 VAC |
| Current (non-PoE) | 1.8 A at 100 VAC, 0.5 A at 230 VAC |

### DC Power Specifications (EX2200-24T-4G-DC)

| Parameter | Value |
|---|---|
| Input voltage | 36–75 VDC |
| Input current | 3.5 A max |
| Power supply output | 100 W |
| Recommended breaker | 10 A minimum |
| PoE support | NOT available |

### Power Consumption & Thermal Output

| Model | Max Power | Max Thermal | Max PoE Available |
|---|---|---|---|
| EX2200-C-12T | 30 W | 102 BTU/hr | — |
| EX2200-C-12P | 30 W | 102 BTU/hr | 100 W |
| EX2200-24T | 50 W | 171 BTU/hr | — |
| EX2200-24P | 60 W | 205 BTU/hr | 405 W |
| EX2200-24T-DC | 50 W | 171 BTU/hr | — |
| EX2200-48T | 76 W | 259 BTU/hr | — |
| EX2200-48P | 91 W | 310 BTU/hr | 405 W |

Max PoE per port: 30 W (Junos 10.3+, IEEE 802.3at), 15.4 W (Junos ≤10.2, IEEE 802.3af).

### Connecting AC Power
1. Squeeze retainer clip sides, insert L-shaped ends into holes on each side of AC inlet
2. Insert coupler end into AC power cord inlet on rear panel
3. Push cord into slot in adjustment nut, turn nut until tight against coupler base (slot 90° from top)
4. If source outlet has switch: set OFF, insert plug, set ON

### Connecting DC Power
1. Ensure input circuit breaker is OPEN
2. Remove clear plastic cover over terminals
3. Terminals: A+, B+, A-, B- (A+/B+ = +RTN, A-/B- = -48V)
4. DC source must supply minimum 7.5 A at -48 VDC
5. Torque terminal screws to **8–9 in-lb (0.9–1.02 Nm)**
6. Single source: positive to A+ or B+, negative to A- or B-
7. Dual source: first source to A+/A-, second to B+/B-
8. Replace cover, close breaker

### Connecting Earth Ground (MUST be done before power)
- Earthing terminal: rear panel
- Grounding cable: 14 AWG (2 mm²), 90°C min
- Grounding lug: Panduit LCC10-14BWL or equivalent
- Screws: Two 10-32 × .25 in with split-lock washer
- Steps: connect cable to earth ground → attach ESD strap → place lug over terminal → secure with washers and screws → dress cable

## Console Connection

### RJ-45 Console Port
- Default baud: **9600**
- Cable: RJ-45 Ethernet (supplied) + RJ-45 to DB-9 adapter (supplied)
- Port labeled CON, CONSOLE, or CON1

### Mini-USB Type-B Console Port (EX2200-C only)
- Only one port active at a time (RJ-45 active by default)
- To activate Mini-USB: `set port-type` command, then **reboot**

### Console settings
| Parameter | Value |
|---|---|
| Baud rate | 9600 |
| Flow control | None |
| Data bits | 8 |
| Parity | None |
| Stop bits | 1 |

### RJ-45 Console Port Pinout
| Pin | Signal | Direction |
|---|---|---|
| 1 | RTS | Output |
| 2 | DTR | Output |
| 3 | TxD | Output |
| 4 | GND | — |
| 5 | GND | — |
| 6 | RxD | Input |
| 7 | CD | Input |
| 8 | NC (CTS) | Input |

### RJ-45 to DB-9 Adapter Pinout
| RJ-45 Pin | Signal | DB-9 Pin | Signal |
|---|---|---|---|
| 1 | RTS | 8 | CTS |
| 2 | DTR | 6 | DSR |
| 3 | TxD | 2 | RxD |
| 4 | GND | 5 | GND |
| 6 | RxD | 3 | TxD |
| 7 | DSR | 4 | DTR |
| 8 | CTS | 7 | RTS |

### USB Port
- Supported USB drives: RE-USB-1G-S, RE-USB-2G-S, RE-USB-4G-S
- USB 2.0+, formatted FAT or MS-DOS

## Environmental Requirements

| Parameter | EX2200-C | EX2200 (non-C) |
|---|---|---|
| Altitude max | 5,000 ft (1,524 m) no degradation | 10,000 ft (3,048 m) no degradation |
| Relative humidity | 10–85% noncondensing | 10–85% noncondensing |
| Operating temp | 32–104°F (0–40°C) | 32–113°F (0–45°C) |
| Seismic | Zone 4 (GR-63) | Zone 4 (GR-63) |

Install in restricted-access areas (dedicated equipment rooms) per NEC Articles 110-16, 110-17, 110-18.

## Initial Configuration

### Default Configuration
Out of the box, the EX2200 ships with:
- Ethernet switching on all interfaces
- Storm control on all interfaces
- PoE enabled on all ports (PoE models)
- LLDP, LLDP-MED, RSTP enabled
- IGMP snooping enabled
- Default user: `root` (no password)

### CLI Configuration (ezsetup)
1. Connect console: RJ-45 to DB-9 adapter (supplied) → laptop
2. Console: 9600/8/N/1
3. At `root%` prompt: type **`ezsetup`**
4. Enter hostname (optional)
5. Enter root password (re-enter to confirm)
6. Enable SSH (default: yes), Telnet (default: no)
7. Management options:
   - **In-band**: use default VLAN auto, or create new VLAN
   - **Out-of-band** (MGMT port me0): specify IP and gateway
8. Specify SNMP read community, location, contact (optional)
9. Specify date/time and time zone (optional)
10. Review and commit

### Manual CLI Configuration (J-Web procedure)
```
1. Connect console (9600/8/N/1), power on
2. Connect Ethernet cable from PC to switch port ge-0/0/0
3. Login as root (no password)
4. cli
5. configure
6. delete chassis auto-image-upgrade     # disable ZTP
7. set system root-authentication plain-text-password  # set password
8. commit
9. set system host-name <name>
10. delete interfaces vme
11. set interfaces vme unit 0 family inet address <ip>/<prefix>
12. set routing-options static route 0/0 next-hop <gateway>
13. set system services ssh root-login allow
14. set system services web-management https system-generated-certificate
15. set system name-server <dns>       # optional
16. set snmp community <string>         # optional
17. commit
18. exit
19. Open browser to https://<ip> → J-Web login
```

### Factory Reset
- **Method 1 (hardware)**: Press and hold the Factory reset/Mode button on the front panel during boot to reset to factory defaults
- **Method 2 (CLI)**: Enter configuration mode and run:
  ```
  root# load factory-default
  root# set system root-authentication plain-text-password
  root# commit
  ```

## Junos OS Configuration

This section covers how to configure the EX2200 switch from a software perspective using the Junos OS CLI. All examples assume you are in configuration mode (`configure`).

### Junos CLI Quick Start

The Junos CLI operates in two main modes:
- **Operational mode** (prompt: `>`): show commands, ping, traceroute, request operations
- **Configuration mode** (prompt: `#`): view and modify the active configuration

Enter configuration mode: `configure` (or `edit`).
Navigate to a hierarchy level: `edit <hierarchy-path>`.
Exit a level: `exit`.
Exit configuration mode: `exit configuration-mode`.
Show the effective config: `show`.
Commit changes: `commit`.
Confirm before auto-rollback: `commit confirmed <minutes>`.
Rollback uncommitted changes: `rollback`.
Rollback to a previous committed version: `rollback <n>` (0 = current, 1 = previous).

```
user@switch> configure
user@switch# show interfaces ge-0/0/0
user@switch# set interfaces ge-0/0/0 description "Link to Server-01"
user@switch# commit
```

### Default Configuration

Out of the box, the EX2200 ships with a factory configuration that includes:
- All network interfaces configured with `family ethernet-switching`
- Storm control enabled on all interfaces
- PoE enabled on all interfaces (PoE models only)
- IGMP snooping enabled for `vlan all`
- RSTP enabled globally
- LLDP and LLDP-MED enabled on all interfaces
- Default user `root` with no password

The factory configuration structure is as follows:
```
system {
    syslog {
        user * { any emergency; }
        file messages { any notice; authorization info; }
        file interactive-commands { interactive-commands any; }
    }
    commit {
        factory-settings {
            reset-chassis-lcd-menu;
            reset-virtual-chassis-configuration;
        }
    }
}
interfaces {
    ge-0/0/0 { unit 0 { family ethernet-switching; } }
    ... (all ports, including uplinks ge-0/1/0 through ge-0/1/3)
}
protocols {
    igmp-snooping { vlan all; }
    rstp;
    lldp { interface all; }
    lldp-med { interface all; }
}
ethernet-switching-options {
    storm-control { interface all; }
}
```

### VLAN Configuration

#### Create a VLAN
```
set vlans <vlan-name> vlan-id <vlan-id>
```

**Example — create VLANs for data and voice:**
```
set vlans DATA vlan-id 100
set vlans VOICE vlan-id 200
```

#### Assign a Port as Access Port to a VLAN
```
set interfaces <interface> unit 0 family ethernet-switching vlan members <vlan-name>
```

**Example — assign ge-0/0/1 to DATA VLAN:**
```
set interfaces ge-0/0/1 unit 0 family ethernet-switching vlan members DATA
```

#### Configure a Trunk Port
```
set interfaces <interface> unit 0 family ethernet-switching port-mode trunk
set interfaces <interface> unit 0 family ethernet-switching vlan members [ <vlan-list> ]
```

**Example — trunk port carrying DATA and VOICE VLANs:**
```
set interfaces ge-0/0/24 unit 0 family ethernet-switching port-mode trunk
set interfaces ge-0/0/24 unit 0 family ethernet-switching vlan members [ DATA VOICE ]
```

**Example — trunk with native VLAN:**
```
set interfaces ge-0/0/24 unit 0 family ethernet-switching port-mode trunk
set interfaces ge-0/0/24 unit 0 family ethernet-switching vlan members all
set interfaces ge-0/0/24 unit 0 family ethernet-switching native-vlan-id <vlan-id>
```

#### Configure Voice VLAN
```                                      ↵
set interfaces <interface> unit 0 family ethernet-switching port-mode access
set vlans VOICE vlan-id <vlan-id>
set vlans VOICE l3-interface irb.<vlan-id>
set interfaces <interface> unit 0 family ethernet-switching vlan members DATA
set ethernet-switching-options voip interface <interface> vlan VOICE
```

**Full example — ge-0/0/5 as access port with DATA VLAN 100 and VOICE VLAN 200:**
```
set vlans DATA vlan-id 100
set vlans VOICE vlan-id 200
set interfaces ge-0/0/5 unit 0 family ethernet-switching port-mode access
set interfaces ge-0/0/5 unit 0 family ethernet-switching vlan members DATA
set ethernet-switching-options voip interface ge-0/0/5 vlan VOICE
```

#### Configure VLAN Routing (IRB / SVI)

To route between VLANs, create an IRB (Integrated Routing and Bridging) interface:
```
set interfaces irb unit <vlan-id> family inet address <ip>/<prefix>
set vlans <vlan-name> l3-interface irb.<vlan-id>
```

**Example — routed VLANs with IP addresses:**
```
set interfaces irb unit 100 family inet address 10.0.100.254/24
set interfaces irb unit 200 family inet address 10.0.200.254/24
set vlans DATA vlan-id 100
set vlans DATA l3-interface irb.100
set vlans VOICE vlan-id 200
set vlans VOICE l3-interface irb.200
```

**Example — uplink as Layer 3 routed port (no IRB needed):**
```
set interfaces ge-0/1/0 unit 0 family inet address 192.168.1.1/30
set interfaces ge-0/1/0 unit 0 family inet no-redirects
```

#### Configure Default Gateway
```
set routing-options static route 0/0 next-hop <gateway-ip>
```

**Example — default route via gateway 192.168.1.2:**
```
set routing-options static route 0/0 next-hop 192.168.1.2
```

#### View VLAN Configuration
```
show vlans                       # all VLANs and their member ports
show vlans <vlan-name> detail    # detailed info for a specific VLAN
show ethernet-switching table    # MAC address table per VLAN
show interfaces <interface>      # full interface status
show configuration vlans         # VLAN configuration in CLI syntax
```

### Interface Configuration

#### Set Interface Description
```
set interfaces <interface> description "<string>"
```

**Example:**
```
set interfaces ge-0/0/1 description "Server-01 eth0"
```

#### Set Interface Speed and Duplex
```
set interfaces <interface> speed <10m|100m|1g|auto>
set interfaces <interface> link-mode <full-duplex|half-duplex|auto>
```

**Example:**
```
set interfaces ge-0/0/1 speed 1g
set interfaces ge-0/0/1 link-mode full-duplex
```

#### Disable / Enable a Port
```
set interfaces <interface> disable              # administratively down
delete interfaces <interface> disable           # bring it back up
```

#### Configure Media Type on Dual-Purpose Uplink (EX2200-C)
```
set interfaces <interface> media-type <copper|fiber>
```

**Example — force SFP uplink port on EX2200-C to use fiber:**
```
set interfaces ge-0/1/0 media-type fiber
```

#### Configure Interface Ranges (Bulk Operations)
```
set interfaces interface-range <range-name> member-range <interface-start> to <interface-end>
set interfaces interface-range <range-name> unit 0 family ethernet-switching port-mode access
set interfaces interface-range <range-name> unit 0 family ethernet-switching vlan members DATA
```

**Example — assign ports 1–12 to DATA VLAN:**
```
set interfaces interface-range DATA-PORTS member-range ge-0/0/0 to ge-0/0/11
set interfaces interface-range DATA-PORTS unit 0 family ethernet-switching port-mode access
set interfaces interface-range DATA-PORTS unit 0 family ethernet-switching vlan members DATA
```

### Link Aggregation (LAG / LACP)

#### Configure a Static LAG (without LACP)
```
set interfaces ae0 aggregated-ether-options link-speed <speed>
set interfaces ae0 unit 0 family ethernet-switching port-mode trunk
set interfaces ae0 unit 0 family ethernet-switching vlan members [ DATA VOICE ]
set interfaces ge-0/0/23 ether-options 802.3ad ae0
set interfaces ge-0/0/24 ether-options 802.3ad ae0
```

#### Configure a Dynamic LAG (with LACP)
```
set interfaces ae0 aggregated-ether-options link-speed 1g
set interfaces ae0 aggregated-ether-options lacp active
set interfaces ae0 unit 0 family ethernet-switching port-mode trunk
set interfaces ae0 unit 0 family ethernet-switching vlan members [ DATA VOICE ]
set interfaces ge-0/0/1 ether-options 802.3ad ae0
set interfaces ge-0/0/2 ether-options 802.3ad ae0
```

**Monitor LAG status:**
```
show lacp interfaces
show interfaces ae0 extensive
show ethernet-switching table interface ae0
```

### Spanning Tree Protocol

EX2200 supports RSTP (default) and MSTP. RSTP is enabled by default in the factory configuration.

#### Configure RSTP (default, typically no changes needed)
```
set protocols rstp interface all
```

**Optional — tune RSTP parameters:**
```
set protocols rstp bridge-priority <0-61440>    # step 4096, lower = more likely root
set protocols rstp max-age <6-40>               # default 20
set protocols rstp hello-time <1-10>            # default 2
set protocols rstp forward-delay <4-30>         # default 15
```

#### Configure MSTP (if multiple spanning tree instances are needed)
```
set protocols mstp configuration-name <name>
set protocols mstp revision-level <revision>
set protocols mstp mst-instance <instance-id> vlan-id <vlan-id>
```

**Example — MSTP with two instances:**
```
delete protocols rstp                         # remove RSTP first
set protocols mstp configuration-name EX2200-CORE
set protocols mstp revision-level 1
set protocols mstp interface all
set protocols mstp mst-instance 1 vlan-id 100-200
set protocols mstp mst-instance 2 vlan-id 300-400
```

#### Configure BPDU Guard on Edge Ports
```
set protocols rstp interface <interface> bpdu-block-on-edge
```

**Example — enable BPDU guard on all edge ports:**
```
set protocols rstp interface ge-0/0/0 bpdu-block-on-edge
set protocols rstp interface ge-0/0/1 bpdu-block-on-edge
```

### LLDP and LLDP-MED Configuration

LLDP and LLDP-MED are enabled by default on all interfaces.

#### Configure LLDP
```
set protocols lldp interface all
```

#### Configure LLDP-MED (for VoIP support)
```
set protocols lldp-med interface all
```

#### Customize LLDP
```
set protocols lldp advertisement-interval <seconds>    # default 30
set protocols lldp transmit-delay <seconds>             # default 2
set protocols lldp hold-multiplier <multiplier>         # default 4
```

### PoE Configuration

PoE is enabled by default on all ports of PoE-capable models.

#### Enable/Disable PoE on a Port
```
set poe interface <interface> disable              # disable PoE on a port
delete poe interface <interface> disable           # re-enable PoE
```

**Example — disable PoE on inter-switch port:**
```
set poe interface ge-0/0/24 disable
```

#### Configure PoE Priority
```
set poe interface <interface> priority <low|high|critical>
```

**Example — set high priority for a VoIP port:**
```
set poe interface ge-0/0/5 priority high
```

#### Configure Maximum Power Per Port
```
set poe interface <interface> maximum-power <watts>
```

**Example — limit to 15.4W per port:**
```
set poe interface ge-0/0/5 maximum-power 15400       # in milliwatts
```

#### View PoE Status
```
show poe                   # global PoE status
show poe interface         # per-interface PoE details
show poe telemetries       # real-time PoE power consumption
```

### Management and Security Configuration

#### Remote Access (SSH, Telnet)
```
set system services ssh root-login allow             # allow root SSH access
set system services ssh protocol-version v2           # use SSHv2 only
set system services telnet                            # enable Telnet (not recommended)
```

#### J-Web (Web Management)
```
set system services web-management https system-generated-certificate
set system services web-management http                # HTTP (not recommended)
```

#### SNMP
```
set snmp community <community> authorization read-only
set snmp community <community> clients <ip-prefix>
set snmp location "<string>"
set snmp contact "<string>"
```

**Example:**
```
set snmp community public authorization read-only
set snmp community public clients 10.0.0.0/8
set snmp location "Data Center A - Rack 12"
set snmp contact "noc@company.com"
```

#### Syslog
```
set system syslog file messages any notice
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands any
set system syslog host <syslog-server> any any
```

**Example — send logs to a remote syslog server:**
```
set system syslog host 10.0.100.10 any any
set system syslog host 10.0.100.10 port 514
```

#### NTP
```
set system ntp server <ntp-server> prefer
set system time-zone <timezone>
```

**Example:**
```
set system ntp server 0.pool.ntp.org
set system ntp server 1.pool.ntp.org prefer
set system time-zone America/New_York
```

#### Authentication and Access
```
set system login user <username> class <class>
set system login user <username> authentication plain-text-password
```

**Available classes:** `super-user`, `operator`, `read-only`, `unauthorized`.

**Example — create an admin account:**
```
set system login user admin class super-user
set system login user admin authentication plain-text-password
```

#### Radius / TACACS+ (Network Access Control)
```
set system radius-server <server-ip> secret <secret>
set system authentication-order [ radius password ]
```

**Example — RADIUS authentication for management:**
```
set system radius-server 10.0.100.10 secret SharedSecret123
set system authentication-order radius
set system authentication-order password
```

#### DHCP Snooping
```
set ethernet-switching-options secure-access-port vlan <vlan-name> dhcp-snooping
set ethernet-switching-options secure-access-port interface <interface> dhcp-trusted
```

**Example — trust the uplink and enable DHCP snooping on a VLAN:**
```
set ethernet-switching-options secure-access-port vlan DATA dhcp-snooping
set ethernet-switching-options secure-access-port interface ge-0/0/24 dhcp-trusted
set ethernet-switching-options secure-access-port interface ge-0/1/0 dhcp-trusted
```

### Port Security and Storm Control

#### MAC Limiting
```
set ethernet-switching-options secure-access-port vlan <vlan-name> mac-limit <count>
set ethernet-switching-options secure-access-port interface <interface> mac-limit <count>
set ethernet-switching-options secure-access-port interface <interface> allowed-mac [ <mac-list> ]
```

**Example — limit to 5 MACs per access port:**
```
set ethernet-switching-options secure-access-port interface ge-0/0/1 mac-limit 5
```

#### MAC Move Limiting
```
set ethernet-switching-options secure-access-port interface <interface> mac-move-limit <count>
```

**Example — limit MAC moves per port:**
```
set ethernet-switching-options secure-access-port interface ge-0/0/1 mac-move-limit 3
```

#### Storm Control
Storm control is enabled on all interfaces by default in the factory configuration.
```
set ethernet-switching-options storm-control interface all
set ethernet-switching-options storm-control interface <interface> bandwidth-level <percentage>
```

**Example — custom storm control on trunk:**
```
set ethernet-switching-options storm-control interface ge-0/0/24 bandwidth-level 80
```

### Virtual Chassis Configuration

EX2200 supports Virtual Chassis starting from Junos OS Release 12.2, with up to 4 member switches.

#### Configure a Uplink Port as a Virtual Chassis Port (VCP) on Each Member
On member 1:
```
set interfaces ge-0/1/3 virtual-chassis vcp
```
On member 2:
```
set interfaces ge-0/1/3 virtual-chassis vcp
```
Connect the VCP ports with an SFP cable (copper or fiber).

#### Set Virtual Chassis Member ID (Preprovisioned Model)
```
set virtual-chassis preprovisioned
set virtual-chassis member <id> role <routing-engine | line-card> serial-number <serial>
```

**Example — two-member Virtual Chassis:**
```
delete virtual-chassis no-split-detect
set virtual-chassis preprovisioned
set virtual-chassis member 0 role routing-engine serial-number CV0209096579
set virtual-chassis member 1 role routing-engine serial-number CV0210098321
```

**Example — non-provisioned (automatic member ID assignment):**
```
set virtual-chassis no-split-detect
```

#### Master Election Priority
```
set virtual-chassis member <id> mastership-priority <value>    # 1-255, higher = more likely master
```

**Example — prefer member 0 as master:**
```
set virtual-chassis member 0 mastership-priority 200
set virtual-chassis member 1 mastership-priority 100
```

#### Split Detection (for Virtual Chassis health)
```
set virtual-chassis no-split-detect                    # disable split detection (if needed)
set virtual-chassis graceful-switchover                # enable nonstop routing during switchover
```

#### Virtual Chassis Monitoring Commands
```
show virtual-chassis                                   # display VC member status and roles
show virtual-chassis vc-port                           # display VCP status
show virtual-chassis status                            # member roles, serials, mastership
request virtual-chassis renumber <old-id> <new-id>     # renumber a member
request virtual-chassis vc-port set <interface>        # set a port as VCP (operational mode)
```

### Configuration Management

#### Save the Active Configuration
```
user@switch> save configuration <filename>               # save to /var/tmp/
user@switch> show configuration | save <filename>
```

**Example:**
```
user@switch> save configuration /var/tmp/backup.conf
```

#### Backup Configuration to a Remote Server
```
user@switch> file copy /var/tmp/backup.conf ftp://server/path/
user@switch> file copy /var/tmp/backup.conf scp://user@server/path/
```

#### Load a Configuration from File
```
user@switch# load override /var/tmp/backup.conf          # replace entire config
user@switch# load merge /var/tmp/snippet.conf            # merge from top-level hierarchy
user@switch# load merge relative /var/tmp/snippet.conf   # merge from current hierarchy level
user@switch# load patch /var/tmp/patch.conf               # apply a diff patch
```

**Example — loading a snippet at a specific hierarchy level:**
```
user@switch# edit system scripts
user@switch# load merge relative /var/tmp/script-snippet.conf
```

#### Rollback Configuration
```
user@switch> rollback <n>                                # rollback to revision n (operational mode)
user@switch# rollback <n>                                # rollback (configuration mode)
```

List available rollback points:
```
user@switch> show system rollback
```

**Example — rollback to revision 2 (the third most recent commit):**
```
user@switch> rollback 2
```

#### Rescue Configuration
The rescue configuration is a known-good configuration saved separately.
```
user@switch> request system configuration rescue save       # save current config as rescue
user@switch> request system configuration rescue delete     # delete the rescue config
user@switch# load rescue                                    # load the rescue config
```

#### Revert to Factory Defaults
```
user@switch# load factory-default
user@switch# set system root-authentication plain-text-password
user@switch# commit
```

Or via the hardware button: press and hold Factory reset/Mode button during boot.

#### Graceful System Shutdown
```
user@switch> request system halt
```
Wait for the confirmation message before disconnecting power.

### Software Management

#### Check Current Software Version
```
user@switch> show version
```

#### Upgrade Junos OS
```
user@switch> request system software add <package-url> validate
user@switch> request system software add <package-url> no-validate  # skip validation
user@switch> request system reboot
```

**Example — upgrade from a file:**
```
user@switch> request system software add /var/tmp/jinstall-ex-2200-<version>.tgz validate
user@switch> request system reboot
```

#### Reboot the Switch
```
user@switch> request system reboot
user@switch> request system reboot at <time>               # scheduled reboot
user@switch> request system reboot in <minutes>            # reboot after delay
```

### Monitoring and Diagnostics — Complete Show Command Reference

| Command | What It Shows |
|---|---|
| `show chassis hardware` | Hardware inventory, serial numbers, model info |
| `show chassis environment` | Temperatures, fan RPM, power supply status |
| `show chassis temperature-thresholds` | Warning and shutdown temperature thresholds |
| `show chassis alarms` | Active chassis alarms |
| `show chassis led` | LED status (same as physical front-panel LEDs) |
| `show system alarms` | System alarms (missing rescue config, licenses) |
| `show log messages` | System log messages |
| `show interfaces terse` | All interface status at a glance (up/down, IPs) |
| `show interfaces <name>` | Detailed status for a specific interface |
| `show interfaces diagnostics optics <name>` | Optical transceiver diagnostics (if SFP) |
| `show vlans` | All VLANs and their member ports |
| `show vlans <name>` | Detailed VLAN info |
| `show ethernet-switching table` | MAC address table |
| `show ethernet-switching table vlan <name>` | MAC table for a specific VLAN |
| `show ethernet-switching table interface <name>` | MAC table for a specific interface |
| `show poe` | Global PoE power information |
| `show poe interface` | Per-port PoE status and power draw |
| `show lldp neighbors` | LLDP-discovered neighbors |
| `show lldp neighbors detail` | Detailed LLDP neighbor info |
| `show spanning-tree bridge` | RSTP/MSTP bridge status |
| `show spanning-tree interface` | RSTP/MSTP per-interface state |
| `show configuration` | Current active configuration |
| `show configuration \| display set` | Configuration in set-command format |
| `show system rollback` | List of saved rollback configurations |
| `show system uptime` | Switch uptime and boot time |
| `show system processes` | Running processes |
| `show system storage` | Disk usage on flash partitions |
| `show version` | Junos OS version and model |
| `show version detail` | Detailed version info, build date |

#### Running Diagnostics Remotely (with user consent)
If the user provides remote connectivity details (IP, credentials), use SSH to execute commands directly.

## Transceiver Maintenance

All transceivers are hot-removable and hot-insertable FRUs.
After inserting or changing media type: **wait 6 seconds** for the interface to display.

### Install a Transceiver
1. Wear ESD wrist strap connected to site ESD point
2. Remove from bag (keep rubber safety cap on)
3. Remove dust cover from port (save it); if hot-swapping, wait **≥10 seconds** after removing old transceiver
4. With both hands, carefully insert transceiver (connectors facing chassis); slide gently until fully seated
5. Remove rubber safety caps; insert cable
6. Secure cable so it does not support its own weight

**CAUTION:**
- Do not touch connector pins (ESD damage)
- Ensure correct alignment before sliding (misalignment bends pins)
- Do not leave transceiver uncovered except during insertion/removal
- Do not look directly into fiber-optic transceiver or cable ends
- Do not bend fiber-optic cable beyond minimum bend radius

### Remove a Transceiver
1. Place antistatic bag/mat on flat surface
2. Wear ESD wrist strap
3. **Label the cable** for correct reconnection
4. Remove cable from transceiver; immediately cover transceiver and cable end with rubber caps
5. Secure cable
6. For SFP/SFP+/XFP/QSFP+: pull open ejector lever until it clicks; grasp lever, slide ~0.5 in (1.3 cm) straight out; grasp body, pull straight out
7. Place transceiver in antistatic bag
8. Place dust cover over empty port (or install replacement)

### Fiber-Optic Cable Handling
- Always cap transceiver and cable end after unplugging
- Anchor cables to prevent stress on connectors — never let cable hang free from connector
- Minimum bend radius: do not bend into arcs smaller than a few inches in diameter
- Frequent plugging/unplugging: use a short fiber extension (cheaper to replace)
- Keep connections clean:
  - Clean transceiver canal with RIFOCS Fiber Optic Adaptor Cleaning Wands or equivalent
  - Clean cable connector tip with approved alcohol-free kit (e.g., Opptex Cletop-S)

### Disconnect Fiber-Optic Cable
1. **Disable the port first:**
   ```
   [edit interfaces]
   user@device# set <interface-name> disable
   ```
2. Carefully unplug connector from transceiver
3. Cover transceiver with rubber safety cap
4. Cover cable connector with rubber safety cap

## Troubleshooting

### Alarm Types & Severity
- **Chassis alarms**: hardware failures (preset, cannot be modified)
- **System alarms**: configuration issues (preset but can be configured to auto-display)
- **Major (red ALM LED)**: immediate action required
- **Minor (amber ALM LED)**: needs monitoring

### Chassis Component Alarm Conditions

| Component | Condition | Severity | Remedy |
|---|---|---|---|
| Fan | Not spinning / below speed | Major | Open JTAC case: 1-888-314-5822 |
| Fan | I2C read failure | Major | Open JTAC case |
| Temperature | Below low operating temp | Major | Check fan; open JTAC case |
| Temperature | Above high operating temp | Major | Check fan; open JTAC case |
| Temperature | EX2200-C: Shutdown — 32°F low / 212°F high | Major | Open JTAC case |
| Temperature | EX2200-C: Red alarm ≥203°F (95°C) | Major | Open JTAC case |
| Temperature | EX2200-C: Yellow alarm ≥176°F (80°C) | Minor | Open JTAC case |
| Temperature | Non-C: Shutdown — 32°F low / 194°F high | Major | Check fan; open JTAC case |
| Temperature | Non-C: Red alarm ≥158°F (70°C); with bad fan ≥140°F | Major | Check fan; open JTAC case |
| Temperature | Non-C: Yellow alarm ≥140°F (60°C); with bad fan ≥122°F | Minor | Check fan; open JTAC case |
| Temperature | Sensor failed | Major | Open JTAC case |
| Media | Booted from backup root | Minor | Open JTAC case |
| Media | /var or /config only 10% free | Major | Clean up storage |
| Media | /var or /config only 25% free | Minor | Clean up storage |
| Media | Upgrade bank empty/corrupted | Major | Open JTAC case |
| Media | Firmware version not latest | Minor | Open JTAC case |
| Media | Single-bit ECC error | Major | Open JTAC case |
| RPS | Disconnected | Major | Check RPS connection |
| RPS | Fan failed | Major | Open JTAC case |
| RPS | Power supply failed | Major | Open JTAC case |
| Management Eth | Link down | Major | Check cable; open JTAC case |
| Routing Engine | /var partition usage high | Minor | Clean up system files |
| Routing Engine | /var full | Major | Clean up system files |
| Routing Engine | Rescue config not set | Minor | `request system configuration rescue save` |
| Routing Engine | License required/expired | Minor | Install required license |

### Key CLI Commands for Troubleshooting

Show chassis environment (temperature, fan status, power):
```
show chassis environment
```
Output fields: Class (Temp or Fans), Item (FPC, CB, RE, PEM), Status (OK/Testing/Failed/Absent), Measurement (temp °C or fan RPM).

Show temperature thresholds:
```
show chassis temperature-thresholds
```
Output fields per component: Fan speed Normal/High thresholds, Yellow alarm (Normal/Bad fan) thresholds, Red alarm (Normal/Bad fan) thresholds, Fire Shutdown threshold.

Check active alarms:
```
show chassis alarms          # chassis alarms (CLI)
# J-Web: Monitor > Events and Alarms > View Alarms
```

View system logs:
```
show log                     # all logs
show log messages            # main system log
# J-Web: Monitor > Events and Alarms > View Events
```

Show hardware inventory:
```
show chassis hardware
```
Sample output:
```
Item                Version  Part number    Serial number     Description
Chassis                                    CV0209096579      EX2200-24P-4G
Routing Engine 0    REV 2A   750-026464    CV0209096579      EX2200-24P-4G, POE
FPC 0               REV 2A   750-026464    CV0209096579      EX2200-24P-4G, POE
   PIC 0            BUILTIN  BUILTIN        BUILTIN           24x 10/100/1000 Base-T
   PIC 1            REV 2A   750-026464    CV0209096579       4x GE SFP
Power Supply 0                                                     PS 550W AC
Fan Tray                                                           Fan Tray
```

### Troubleshooting Temperature Alarms
Problem: `FPC 0 EX-PFE1 Temp Too Hot` alarm
1. Run `show chassis environment` — check temperatures and fan status
2. Run `show chassis temperature-thresholds` — identify which threshold was exceeded
3. Check causes:
   - Ambient temp too high → ensure within limits (EX2200: 0–45°C, EX2200-C: 0–40°C)
   - Fan failure → replace fan module; open JTAC case
   - Restricted airflow → ensure minimum 6 in (15.2 cm) clearance around vents

### Troubleshooting PoE
Problem: PoE-powered devices not getting power.
Solution: Disable PoE on all RJ-45 ports used for inter-switch connections:
```
set poe interface <interface-name> disable
```
(This applies to EX2300/EX3400/EX4300 connected to EX2200 via RJ-45; the PoE voltage injection can interfere.)

## Virtual Chassis

- Supported starting Junos OS Release 12.2
- Up to 4 EX2200 switches (including EX2200-C) can form a Virtual Chassis
- Use SFP uplink ports as Virtual Chassis Ports (VCPs):
  ```
  set interfaces <interface-name> virtual-chassis vcp
  ```
- For full Virtual Chassis configuration: refer to *Virtual Chassis User Guide for EX2200, EX3300, EX4200, EX4500 and EX4550 Switches*

## RMA and Returns

### Locate Serial Number
```
show chassis hardware
```
Physical label: rear panel of chassis (shows SN, model, part number, revision, manufacture date, MAC address).

### Contact JTAC
- US/Canada/Mexico toll-free: **+1-888-314-JTAC (+1-888-314-5822)**
- Web: https://support.juniper.net/support
- 24/7 support
- Have ready: serial number, failure details, configuration data from `show` commands

### Packing for Return
1. `request system halt` — wait for confirmation
2. Disconnect power
3. Remove all external cables
4. Remove all optical transceivers
5. If mounted: one person holds while another removes screws
6. Place switch in antistatic bag
7. Place in original shipping carton with packing foam
8. **Write RMA number on carton exterior**

## Cabling Specifications

### Supported Transceivers
- Use only Juniper-qualified optics
- JTAC provides limited support for third-party optics
- Full compatibility list: refer to Juniper Hardware Compatibility Tool for EX2200

### AC Power Cord by Region
| Country | Juniper Model Number |
|---|---|
| North America | CBL-EX-PWR-C13-US |
| Europe | CBL-EX-PWR-C13-EU |
| UK | CBL-EX-PWR-C13-UK |
| Australia | CBL-EX-PWR-C13-AU |
| Japan | CBL-EX-PWR-C13-JP |
| China | CBL-EX-PWR-C13-CH |
| (Others) | CBL-EX-PWR-C13-{country code} |

North America cords must not exceed 4.5 m (NEC compliance).

### Pluggable Transceivers
- EX2200 (non-C): 4 SFP uplink ports
- EX2200-C: 2 dual-purpose uplink ports (1 RJ-45 + 1 SFP each; only one active at a time)
- SFP port is active by default when transceiver plugged in; override with `media-type` command
- Dual-purpose uplink behavior: RJ-45 active by default if no transceiver plugged in
