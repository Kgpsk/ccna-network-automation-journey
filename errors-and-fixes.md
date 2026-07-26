LAB 1 ERRORS AND FIXES

Date July 2026
Topic Basic 3 PC Network with Router
Objective Build a working network where PC1 192.168.1.20 can ping PC2 192.168.2.10


ERROR 1 ROUTER INTERFACES ADMINISTRATIVELY DOWN

Symptom
Pings from PC1 to PC2 failed. All pings timed out.

Investigation
On router ran show ip interface brief and saw

GigabitEthernet0 0    192.168.1.1    YES manual administratively down down
GigabitEthernet0 1    192.168.2.1    YES manual administratively down down

Cause
Cisco router interfaces are disabled by default. Even after assigning an IP address the interface will not work until you enable it.

Fix

Router enable
Router configure terminal
Router config interface gig0 0
Router config if no shutdown
Router config if exit
Router config interface gig0 1
Router config if no shutdown
Router config if end
Router write memory

Verification

Router show ip interface brief

Interface              IP Address      OK Method Status                Protocol
GigabitEthernet0 0     192.168.1.1     YES manual up                    up
GigabitEthernet0 1     192.168.2.1     YES manual up                    up

Key Takeaway
Always run no shutdown on router interfaces. This is one of the most common mistakes beginners make.


ERROR 2 WRONG ROUTER CONNECTIONS

Symptom
PC1 could ping PC0 same network but could not ping PC2 different network.

Investigation
Checked physical connections and found

Connection              Actual          Should Be
Router Gig0 0           Connected to PC2 Connected to Switch0
Router Gig0 1           Connected to Switch0 Connected to PC2

Cause
The router was connected backwards. Gig0 0 intended for LAN side was connected to PC2 and Gig0 1 intended for PC2 was connected to Switch0. The router could not route traffic properly.

Fix
Reconnected cables correctly

Port            Connect To
Router Gig0 0   Switch0 LAN side Network A
Router Gig0 1   PC2 Network B

Key Takeaway
Always plan your connections before you plug cables in. Draw your topology first then connect.


ERROR 3 PC2 MISSING DEFAULT GATEWAY

Symptom
PC2 could ping its own IP 192.168.2.10 but could not ping the router 192.168.2.1 or any device outside its network.

Investigation
Checked PC2 IP configuration

IP Address 192.168.2.10
Subnet Mask 255.255.255.0
Default Gateway BLANK   PROBLEM

Cause
PC2 had no default gateway configured. Without a gateway it cannot send traffic outside its own network.

Fix
On PC2 set the default gateway to 192.168.2.1

Key Takeaway
Every PC on a network needs a default gateway to communicate outside its own subnet. The gateway is always the router interface on that network.


ERROR 4 FIRST PING TIMEOUT

Symptom
When pinging PC2 from PC1

C ping 192.168.2.10

Request timed out.           First ping fails
Reply from 192.168.2.10 bytes 32 time 1ms TTL 127
Reply from 192.168.2.10 bytes 32 time 1ms TTL 127
Reply from 192.168.2.10 bytes 32 time 1ms TTL 127

Cause
The first ping always times out due to ARP Address Resolution Protocol.

When PC1 tries to ping PC2 for the first time
1 PC1 checks its ARP cache for the MAC address of its gateway 192.168.1.1
2 The MAC is not in the cache
3 PC1 sends an ARP broadcast Who has 192.168.1.1
4 The router responds with its MAC address
5 PC1 stores it in ARP cache takes about 1 second
6 Now the ping can be sent

This delay causes the first ping to time out.

Fix
No fix needed. This is normal behavior.

To verify run a second ping immediately. It will work perfectly.

Key Takeaway
ARP is a Layer 2 protocol that resolves IP addresses to MAC addresses. It only happens the first time a device needs to communicate with a new IP. This is why professional network engineers always run multiple pings.


ERROR 5 PC0 IP ADDRESS DUPLICATE

Symptom
During initial setup PC0 and PC1 both had IP 192.168.1.10

Cause
Duplicate IP addresses on the same network.

Fix
PC0 192.168.1.10 24
PC1 192.168.1.20 24

Key Takeaway
Every device on the same network must have a unique IP address. Duplicate IPs cause conflicts and connectivity failures.


SUMMARY OF ALL FIXES

Number  Error                                            Fix
1       Router interfaces administratively down          Enable interfaces with no shutdown
2       Wrong router connections                         Reconnect cables Gig0 0 to Switch Gig0 1 to PC2
3       PC2 missing default gateway                      Set PC2 Gateway to 192.168.2.1
4       First ping timeout                               No fix needed Normal ARP behavior
5       Duplicate IP address                             Change IP PC1 to 192.168.1.20


FINAL SUCCESSFUL PING RESULT

C ping 192.168.2.10

Pinging 192.168.2.10 with 32 bytes of data

Reply from 192.168.2.10 bytes 32 time 1ms TTL 127
Reply from 192.168.2.10 bytes 32 time 1ms TTL 127
Reply from 192.168.2.10 bytes 32 time 1ms TTL 127

Ping statistics for 192.168.2.10
    Packets Sent 4 Received 3 Lost 1 25 percent loss
    Approximate round trip times in milli-seconds
    Minimum 0ms Maximum 1ms Average 0ms

Network is working


WHAT I LEARNED FROM THIS LAB

Topic                 Lesson
OSI Layers            Data passes through all 7 layers Layer 1 sends radio and wire signals
MAC vs IP             MAC for same network communication IP for cross network communication
Router Role           Gateway between different networks
Default Gateway       Where PCs send traffic for different networks
ARP                   Resolves IP to MAC causes first ping timeout
no shutdown           Router interfaces are disabled by default
Documentation         Always document errors and fixes this is what professionals do


NEXT STEPS

Python script to SSH into router
Automate show ip interface brief via code
Automate VLAN creation
Backup router config automatically
