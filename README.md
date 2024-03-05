# Portfolio
My portfolio to showcase my skillsets and knowledge

Report 1: tcpdump Incident Report: Network Traffic Analysis

Provide a summary of the problem found in the DNS and ICMP traffic log.

The UDP protocol log reveals that the IP request did not complete due to the port receiving DNS request (port 53) did not allow entry. The ICMP echo reply returned the error message: “domain: 35084+ A?” and “port 53 unreachable”, this ICMP message reply indicates that the DNS request did not get past port 53 and the request was not processed. Port 53 is designated to direct traffic into servers for DNS request processing. The most likely issue might be firewall misconfiguration or traffic overload (DoS attack).


Analysis of the data and identify possible cause of the incident.

Time incident occurred: 13:24:32.192571, which is 1:24 p.m. at 32.192571 seconds. Several customers failed to access the client website and saw an error message “destination port unreachable”, then reported the issue to the company.
IT team immediately investigated to identify affected network protocol utilizing tcpdump to attempt to load the website again, which then led to the discovery of the ICMP reply for the UDP protocol (DNS request) indicating that port 53 was unreachable, which is the port on a server that is used for DNS requests. The likely cause would actually be a DoS attack. Several customers being unable to reach the server would reduce the likelihood that the firewall on the server viewed all requests, including the IT technician’s IP address as suspicious traffic. Multiple devices trying to access a single server being denied where all the requests did not even get to enter the port of the server, would more likely be the result of a DoS attack where the traffic overload has denied entry of genuine requests. 



Report 2: Wireshark Incident Report

Identify the type of attack that may have caused this network interruption

One potential explanation for the website's connection timeout error message is Server failure due to overwhelming workload. The logs show that an abnormally large amount of TCP SYN requests are being sent from an unfamiliar IP address. This could be an IP spoofing coupled with a DoS attack, known as a smurf attack. How the attack caused the website to malfunction. When website visitors tried to establish a connection with the web server, a three-way handshake occurs using the TCP protocol. The visitor device sends a SYN request to the server; server receives the SYN request and responds with a SYN/ACK reply to acknowledge receipt of the SYN request; Client device confirms receipt of SYN request by the website server and replies again with the last ACK reply and a TCP connection is established. Therefore when an attacker sends an enormous amount of SYN packets, the server tries its best to respond to every request but eventually gets overwhelmed by the processing load and fails to process any legitimate request. This causes the TCP connection attempt to time out because while waiting for a reply from the server. 

The logs provided by the packet sniffer showed a large number of TCP SYN requests coming from an unfamiliar IP address, and the server seemed to be overwhelmed. This indicates that the large amount of SYN requests is likely to be malicious and did not spoof an IP from the employees or IT team, this means that the attacker possibly spoofed an IP from customer traffic undetected, likely indicating that the encryption of the data transit for the website is also insecure, warranting an effort to increase security to prevent future similar attacks. Using HTTPS for the website communication protocol can introduce better security.

Overall Interpretation:

In the event of a malicious attack on a website server, it’s standard procedure to utilize a packet sniffer to analyze traffic to understand which part went wrong. The logs on the packer sniffer (Wireshark, tcpdump or others) will show useful information to identify an attack like "504 gateway time-out (http)" or "RST,ACK" if the TCP handshake has not been established yet. Other interpretations of valuable metrics include the understanding that large amounts of single-sided requests without waiting for SYN/ACK reply indicates a DoS attack. If the single sided requests all originate from a single IP, then it is a classic DoS attack which might utilize methods other than SYN flood, like SYN or ICMP flood. If multiple, rapid requests are made to the server originating in multiple different IP addresses then it is a Distributed DoS attack, or DDoS.

Besides that, source IP also tells us if the usual traffic is secured. If a familiar IP was used then spoofing has occurred and should signal the IT team that besides configuring the firewall to prevent future DoS attacks, the connection which got spoofed must be strengthened too. 
