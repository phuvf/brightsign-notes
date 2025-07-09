# How to update the NTP server on a BrightSign using SSH

Last updated: 9th July 2025

> [!NOTE]  
> In this guide I'm going to use `192.168.1.135` as an example for the BrightSign device's IP address. Change it to your device's actual IP address.
>
> I'm also assuming the Diagnostic Web Server is enabled on the BrightSign device. If you don't have access to the web interface, you'll need to enable that first.

## 1. Enable SSH on the BrightSign device

- Log in to the BrightSign device's web interface at `http://192.168.1.135`. Default credentials are:
  - Username: `admin`
  - Password: (your player's serial number) (e.g. `R3E6D8001339`)
- Navigate to the `Diagnostics` section.
- In the `Telnet and SSH Access` section, select `Enable SSH`
- You can leave the port as `22` and don't need to set a password
- Click `Update` to apply the changes. The player will reboot.

## 2. Add a custom `autorun.brs` file

We need to run a custom `autorun.brs` file to enable shell access.

- Download the current `autorun.brs` file from the BrightSign via the `SD` section. Save this somewhere on your computer as we'll be putting it back later.
- Create a new file named `autorun.brs` on your machine. Open in a text editor and add the single word `end` to this file.
- Using the web interface, navigate to the `SD` section and delete the existing `autorun.brs` file.
- Upload the new `autorun.brs` file you just created to the BrightSign device
- Navigate to the `Control` section and click on `Reboot` to restart the device

## 3. Connect to the BrightSign device via SSH

- Once the device has rebooted, you can now connect to it via SSH and should get shell access.
- Open Putty or any other SSH client.
- Connect to the BrightSign device using it's IP address `brightsign@192.168.1.135`
- Log in with the default credentials:
  - Username: `brightsign`
  - Password: (none)

You should see a welcome message similar to the following:

```bash
Using username "brightsign".
brightsign@192.168.1.135's password:
BrightSign XD233 R3E6D8001339 v8.5.64

Welcome to the BrightSign Shell version 8.5.64 (2025-02-19)
Type '?' for help or 'help <command>' for help on <command>.

BrightSign>
```
## 4. Update the NTP server
- Check the current NTP server configuration by running `ifconfig`. Here's an example output:

```bash
Interface:      eth0
 Type:           Wired
 Link:           Yes
 Configuration:  DHCP
 Client ID:      BrightSign:R3E6D8001339
 MAC:            90:ac:3f:0a:0b:2e
 Current IP:     192.168.1.135
 Netmask:        255.255.255.0
 Broadcast:      192.168.1.255
 Gateway:        192.168.1.1
 IP6 Address:    fd2b:df65:2c67:36c:92ac:3fff:fe0a:b2e/64
 IP6 Address:    fe80::92ac:3fff:fe0a:b2e/64
 Metric:         202
 Domains:        localdomain
 DNS:            192.168.1.1
 Shape inbound:  No

Host
 Host Name:      BrightSign-R3E6D8001339
 mDNS Host Name: BrightSign-R3E6D8001339.local
 Time server:    time.brightsignnetwork.com
 Proxy Bypass:   <none>

BSN
 Crash URL:      https://crashes.brightsignnetwork.com/bs/crashdump
 Error URL:
 Recovery URL:

Other
 Http Port:
 BrightSign Ssh Port: 22

```
To edit the NTP server, use `ifconfig time-server time.brightsignnetwork.com`. Examples:
```bash
ifconfig time-server time.brightsignnetwork.com # BrightSign default
ifconfig time-server clock.fopnet.com  # Old F1 NTP server, no longer available
ifconfig time-server track-ntp.fix8group.com # FIX8Group F1 NTP server
ifconfig time-server 172.166.214.56 # You can use an IP as well, but it's better to use a domain name
```

## 5. Verify the NTP server update
To verify that the NTP server has been updated, you can run `ifconfig` again and check the `Time server` line.

You can also check in the BrightSign web interface under the `Diagnostics` >> `Network Configuration` section to see if the NTP server has been updated there as well.

Finally, run the `Network Diagnostics` tool from the web interface to ensure that the device can reach the new NTP server. In the `Internet` results, you should see: 

```bash
INTERNET
Result: OK

Checking DNS servers...
PASS: At least one DNS server is valid
Checking DNS server (cached)...
PASS: DNS servers responded to 100% of potentially cached requests
Checking DNS server (hopefully not cached)...
PASS: DNS servers responded to 100% of hopefully uncached requests
Checking Internet connectivity...
PASS: PING 10/10: 87990/88411/88949us
Checking HTTP...
PASS: External IP is 31.111.73.67
Checking time server...
PASS
```

Probably best to reboot the device again to ensure everything is working correctly.

## 6. Restore the original `autorun.brs` file

- Navigate to the `SD` section in the BrightSign web interface.
- Delete the current `autorun.brs` file (the one with the single `end` in it).
- Upload the original `autorun.brs` file you downloaded in step 2.
- Navigate to the `Control` section and click on `Reboot` to restart the device

## 7. Disable SSH (optional)
If you no longer need SSH access, you can disable it now.

