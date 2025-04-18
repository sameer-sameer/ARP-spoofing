ARP Spoofer (Man-in-the-Middle Attack)
This Python script performs ARP spoofing (Man-in-the-Middle attack) to intercept network traffic between a victim and a router. The attacker positions themselves between the two devices and can intercept, modify, or block the traffic between them.

Features
ARP Spoofing: Pretend to be the gateway to the victim and vice versa.

Real-time packet counting: Keeps track of the number of spoofed packets sent.

Automatic ARP table restoration: Ensures that the original ARP tables are restored upon script interruption (Ctrl+C).

Requirements
Python 3.x

scapy library

To install the required library, use the following command:

bash
Copy
Edit
pip install scapy
How to Use
Identify the IP addresses:

Target IP (Victim): The device you want to intercept traffic from (e.g., a computer or smartphone).

Gateway IP (Router): The router or gateway device that connects the local network to the internet.

You can find these IP addresses using the following steps:

Find Target IP (Victim):

If you're on Linux or macOS, open the terminal and use ip a to check the IP addresses of devices on your network.

If you're on Windows, use ipconfig in the command prompt.

Find Gateway IP (Router):

On Linux or macOS, run ip route | grep default or netstat -nr | grep default.

On Windows, run ipconfig and look for the "Default Gateway" section.

Set the IPs in the script: In the script, set the following variables:

python
target_ip = "192.168.1.x"   # Replace with the victim's IP
gateway_ip = "192.168.1.x"  # Replace with your router's IP
Run the script:

bash
sudo python3 arp_spoof.py

This will start the ARP spoofing process, and you'll see real-time packet counts.

Stop the script: Press CTRL + C to stop the script. The ARP tables will be restored automatically.

Warning
This script is intended for educational purposes only. Unauthorized use of ARP spoofing can be illegal and unethical. Always get permission before testing on any network.
