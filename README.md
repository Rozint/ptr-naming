# PTR Record Naming Standard
An open and standardized way to name and identify your devices like network equipment, network tools, hypervisors, looking glasses and PTRs.

## Introduction
Always struggling with naming your routers, switches and looking glasses? Standardize them now using this easy-to-use naming convention. The goal of this standard is to be a go-to set of rules that can be applied for your network deployment, allowing you not to worry about ending up inconsistent. It also offers all people involved, including customers to have some useful hostnames for example.
Remember that everything is optional and you are free to fork it and customize it to your liking. We recommend you to not use `.` in hostnames, as they are trimmed when you log in into a server/switch like: `[root@edge1 ~]#`.

## Changelog
| Version | Changes          |
|:-------:|------------------|
|  DIS-1  | Initial release. |

## Syntax
### Devices
The general syntax for equipment like routers, switches and servers is as follows:

```
{NAME}-[FACILITY]-{CITY}-{COUNTRY}
```

|     Tag      | Required | Description                                                                                                                               |
|:------------:|----------|-------------------------------------------------------------------------------------------------------------------------------------------|
| `[FACILITY]` | Optional | Short name for facility. Could be useful for networks with several point-of-presences in a metro to have a distinction between facilities. |
|`{NAME}`|✅| Name for the devices, like router, switch, edge, but it may also be a name for a specific device. Usually includes a number as well.      |
|`{CITY}`|✅| Code based on UN/LOCODE, more about this [below](#codes).                                                               |
|`{COUNTRY}`|✅| Code based on the well-known ISO 3166-1-alpha-2 standard, this is also a part of the UN/LOCODE.                                           |

*Examples*:
`edge1-us-ny`, `core2-doi-nl`, `colosseum-rom-it`, `reactor-che-ua` or with the optional facility tag: `edge2-imdc-haa-nl`

### Hostnames
Of course, most of these devices must have a hostname as well, which may be shown in traceroutes as hops. It's based on the Devices syntax, but with an additional hostname and optionally an interface tag:

```
[CIRCUIT].[INTERFACE].{NAME}-[FACILITY]-{CITY}-{COUNTRY}.{DOMAIN}.{TLD}
```

|      Tag      | Required | Description                                                             |
|:-------------:|----------|-------------------------------------------------------------------------|
|  `[CIRCUIT]`  | Optional | Freeform circuit identification.                                        |
| `[INTERFACE]` | Optional | Interface name as addition with identifier.                             |
|  `{DOMAIN}`   | ✅        | Domain name related to your business.                                   |
|    `{TLD}`    | ✅        | TLD is usually a .NET domain, but you're free to use whatever you want. |

#### Interface Syntax
|  Type   | Description                       |
|:-------:|-----------------------------------|
|  `et#`  | Ethernet interfaces.              |
|  `lo#`  | Loopback interfaces.              |
|  `ae#`  | Juniper aggregated ethernet.      |
| `lag#`  | Vendor agnostic lag/port channel. |
| `vlan#` | VLAN interfaces.                  |

*Example*: `edge1-us-ny.rozint.com`, or with interface `vlan1337.edge1-us-ny.rozint.com`, or with the whole shebang `cust9.vlan1337.edge1-us-ny.rozint.com`

### TXT
For network operators with a lot of point-of-presences, it may be useful to offer TXT responses on hostnames showing the location more extensively including geo-location data. We have a little scheme for this as well:

```
[FACILITY-ID]|{CITY}|{COUNTRY}|{LATITUDE,LONGITUDE}
```

|        Tag        | Required | Description                                                      |
|:-----------------:|----------|------------------------------------------------------------------|
|  `[FACILITY-ID]`  | 🟧 | Facility ID from PeeringDB, or `0` if you do not want to use it. |
|   `{CITY}`   | ✅ | City name written in full.                                       |
|    `{COUNTRY}`     | ✅        | Country name written in full.                                    |
|      `{LATITUDE,LONGITUDE}`      | ✅        | Consists of the latitude and longitude of the location.          |

*Example*:
Let's take the popular facility Nikhef in Amsterdam as example: `nikhef-ams-nl-core9.rozint.com`. A TXT query towards this hostname should respond with `29179|Amsterdam|Netherlands|52.356268940475466,4.951248897980831` like shown here:
```
; <<>> DiG 9.10.6 <<>> txt nikhef-ams-nl-core9.rozint.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 25287
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;nikhef-ams-nl-core9.rozint.com.			IN	TXT

;; ANSWER SECTION:
nikhef-ams-nl-core9.rozint.com.		300	IN	TXT	"29179|Amsterdam|Netherlands|52.356268940475466,4.951248897980831"

;; Query time: 33 msec
;; SERVER: 2001:4860:4860::8888#53(2001:4860:4860::8888)
;; WHEN: Mon Feb 12 21:56:49 CET 2024
;; MSG SIZE  rcvd: 243
```

### Looking Glasses
The syntax for looking glass is:

```
LG-{CITY}-{COUNTRY}.{DOMAIN}.{TLD}
```

*Example*: Assume a looking glass for a network in Amsterdam, this would end up like: `lg-us-ny.rozint.com`.

Please note that this hostname must point towards an IP within your network, do not put a looking glass behind a reverse proxy like Cloudflare.

### Hypervisors
To make this standard more complete, we've also a scheme for hypervisor nodes. This is a rather simple syntax with optional city or country tag, depending on your business/marketing. For those who have different categories we have the custom tag, as used in the last example below.

```
NODE#-[CUSTOM]-[CITY]-[COUNTRY].{DOMAIN}.{TLD}
```

*Examples*: `node1-us-ny1.rozint.com` or `node2-ny1.rozint.com` or `node3-premium-ny1.rozint.com`

#### Codes
City codes are based on *United Nations Code for Trade and Transport Locations*, also known as UN/LOCODE.
Country codes are based on the well-known ISO 3166-1-alpha-2 standard, which is actually a part of the UN/LOCODE as well.

A list of UN/LOCODE codes can be found on the UNECE website [here](https://unece.org/trade/cefact/unlocode-code-list-country-and-territory).
For those who want to play with more raw data, you may check Core Data repository [here](https://github.com/datasets/un-locode/tree/main/data).

#### DNS
To do this naming properly, it is expected to properly set up DNS, meaning that a hostname must end up with A or AAAA records to the IP of the device.
Vice versa it is recommended to maintain a reverse PTR record from IP address to the hostname.

### Contribute
Feedback is welcome. The standard is not expected to change as it's quite basic, but we are always open for discussions.

## License
Mozilla Public License Version 2.0
