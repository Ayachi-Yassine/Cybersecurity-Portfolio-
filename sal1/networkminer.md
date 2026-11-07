# NetworkMiner

NetworkMiner is an open-source traffic sniffer,

PCAP

handler and protocol analyser.

Developed and maintained by Netresec.

# **Network Miner in Forensics**

The ultimate goal of network forensics is to provide sufficient information to detect malicious activities, security breaches, and network anomalies based on the network traffic. And NetworkMiner is a great tool to help, as it provides quick and useful hints on where to start from, specifically:

- Context of captured hosts like their IP and MAC, hostnames, or  information
    
    OS
    
- List of potential attack indicators or anomalies like traffic spikes or port scans
- Tools or toolkits used to perform the potential attacks like
    
    Nmap
    

## **Supported Data Types**

There are three main data types investigated in Network Forensics:

- **Live Traffic**
- **Traffic Captures**
- **Log Files**

NetworkMiner is capable of processing and handling both packet pictures (PCAP) and live traffic. Therefore, we will focus on live and captured traffic in this room.

## **NetworkMiner in a Nutshell**

| **Capability** | **Description** |
| --- | --- |
| **Traffic sniffing** | It can intercept the traffic, sniff it, and collect and log packets that pass through the network. |
| **Parsing PCAP files** | It can parse pcap files and show the content of the packets in detail. |
| **Protocol analysis** | It can identify the used protocols from the parsed pcap file. |
| **OS fingerprinting** | It can identify the used OS by reading the pcap file. This feature strongly relies on [Satori](https://github.com/xnih/satori/) and [p0f](https://lcamtuf.coredump.cx/p0f3/). |
| **File Extraction** | It can extract images, HTML files and emails from the parsed pcap file. |
| **Credential grabbing** | It can extract credentials from the parsed pcap file. |
| **Clear text keyword parsing** | It can extract cleartext keywords and strings from the parsed pcap file. |

## Operating modes : best for low hanging fruit grabbing

### Sniffer Mode :

not recommanded for sniffing as it is described as Network Forensics tool

### Packet Parsing/Processinf

 NetworkMiner can parse traffic captures to have a quick overview and information on the investigated capture. This operation mode is mainly suggested to grab the "low hanging fruit" before diving into a deeper investigation.

## **Pros and Cons**

As mentioned in the previous task, NetworkMiner is mainly used to gain an overview of the network. Before starting to investigate traffic data, let's look at **the pros and cons of the NetworkMiner.**

**Pros**

- OS fingerprinting
- Easy file extraction
- Credential grabbing
- Clear text keyword parsing
- Overall overview

**Cons**

- Not useful in active sniffing
- Not useful for large pcap investigation
- Limited filtering
- Not built for manual traffic investigation

**Differences Between Wireshark and NetworkMiner**

**Differences Between Wireshark and NetworkMiner**

## navigating the tool

## **Hosts**

The "hosts" menu shows the identified hosts in the pcap file. This section provides information on:

- IP address
- MAC address
- OS type
- Open ports
- Sent/Received packets
- Incoming/Outgoing sessions
- Host details

OS fingerprinting uses the Satori GitHub repo and p0f, and the MAC address database uses the mac-ages GitHub repo.

## **Sessions**

The session menu shows detected sessions in the pcap file. This section provides information on:

- Frame number
- Client and server address
- Source and destination port
- Protocol
- Start time

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/0897d01e900bd9f1fb1b2ec9b54f06eb.png)

You can search for keywords inside frames with the help of the filtering bar. It is possible to filter specific columns of the session menu as well. This menu accepts four types of inputs;

- "ExactPhrase"
- "AllWords"
- "AnyWord"
- "RegExe"

## **DNS**

The DNS menu shows DNS queries with details. This section provides information on:

- Frame number
- Timestamp
- Client and server
- Source and destination port
- IP
    
    TTL
    
- DNS time
- Transaction ID and type
- DNS query and answer
- Alexa Top 1M

Some of the features (Alexa Top 1M) are available only in premium mode. The search bar is available here as well.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/8632f3f01269b805a1a8a7aeee76c14d.png)

## **Credentials**

The credentials menu shows extracted credentials and password [hashes](https://tryhackme.com/room/hashingcrypto101) from investigated pcaps. You can use [Hashcat](https://tryhackme.com/room/crackthehashlevel2) ([GitHub](https://github.com/hashcat/hashcat)) and [John the Ripper](https://tryhackme.com/room/johntheripper0) ([GitHub](https://github.com/openwall/john)) to decrypt extracted credentials. NetworkMiner can extract credentials including:

- Kerberos hashes
- NTLM hashes
- RDP cookies
- HTTP cookies
- HTTP requests
- IMAP
- FTP
- SMTP
- MS
    
    SQL
    

The right-click menu is helpful in this part as well. You can easily copy the username and password values.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/888d7b45713c75d2eb8a1687d2b16e6b.png)

### Questions

![image.png](image%2051.png)

![image.png](image%2052.png)

![image.png](image%2053.png)

![image.png](image%2054.png)

![image.png](image%2055.png)

## **Files**

The file menu shows extracted files from investigated pcaps. This section provides information on:

- Frame number
- Filename
- Extension
- Size
- Source and destination address
- Source and destination port
- Protocol
- Timestamp
- Reconstructed path
- Details

Some features (OSINT hash lookup and sample submission) are available only in premium mode. The search bar is available here as well. The right-click menu is helpful in this part as well. You can easily open files and folders and view the file details in-depth.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/d0cb3d76e3dce6139611f8a451edee67.png)

## **Images**

The file menu shows extracted images from investigated pcaps. The right-click menu is helpful in this part as well. You can open files and zoom in & out easily.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/77a5139e9cd949c57105bd42aab2d741.png)

Once you hover over the image, it shows the file's detailed information (source & destination address and file path).

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/9a6ad0aa93c7c700736487a1707ef44b.png)

## **Parameters**

The file menu shows extracted parameters from investigated pcaps. This section provides information on:

- Parameter name
- Parameter value
- Frame number
- Source and destination host
- Source and destination port
- Timestamp
- Details

The right-click menu is helpful in this part as well. You can copy the parameters and values easily.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/f07b50304faeb1767c49a9e002622f31.png)

## **Keywords**

The file menu shows extracted keywords from investigated pcaps. This section provides information on:

- Frame number
- Timestamp
- Keyword
- Context
- Source and destination host
- source and destination port

**How to filter keywords:**

- Add keywords
- Reload case files!

**Note:** You can filter multiple keywords in this section; however, you must reload the case files after updating the search keywords. Keyword search investigates all possible data in the processed pcaps.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/ab513599f2a948ad4e4e8f634f2cb6fd.png)

## **Messages**

The messages menu shows extracted emails, chats and messages from investigated pcaps. This section provides information on:

- Frame number
- Source and destination host
- Protocol
- Sender (From)
- Receiver (To)
- Timestamp
- Size

Once you filter the traffic and get a hit, you will discover additional details like attachments and attributes on the selected message. Note that the search bar is available here as well. The right-click menu is available here. You can use the built-in viewer to investigate overall information and the "open file" option to explore attachments.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/3e4dfffef8d0518ffbfe152275aab8c6.png)

## **Anomalies**

The anomalies menu shows detected anomalies in the processed pcap. Note that NetworkMiner isn't designated as an IDS. However, developers added some detections for EternalBlue exploit and spoofing attempts.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/b7960a4440fd9eb7367b0fc6b2b5ec7b.png)

![image.png](image%2056.png)

![image.png](image%2057.png)

![image.png](image%2058.png)

![image.png](image%2059.png)

# **Version Differences**

## **Mac Address Processing**

NetworkMiner versions after version 2 can process MAC address specific correlation as shown in the picture below. This option will help you identify if there is a MAC Address conflict. This feature is not available before version 2.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/d1588f5c22ae8160fe21a766a719241a.png)

NetworkMiner versions up to version 1.6. can handle packets in much detail. These options will help you investigate the sent/received packets in a more detailed format. This feature is not available after version 1.6.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/41f7765a30763cc91a99fdd3ea63495c.png)

## **Frame Processing**

NetworkMiner versions up to version 1.6. can handle frames. This option provides the number of frames and essential details about the frames. This feature is not available after version 1.6.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/ac78e75cdfa166646029ae0d8166a320.png)

## **Parameter Processing**

NetworkMiner versions after version 2 can handle parameters in a much more extensive form. Therefore version 1.6.xx catches fewer parameters than version 2.

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/ce0f46889c838a759888cb5336278fcc.png)

## 

NetworkMiner versions up to version 1.6. can handle cleartext data. This option provides all extracted cleartext data in a single tab; it is beneficial to investigate cleartext data about the traffic data. However, it is impossible to match the cleartext data and packets. This feature is not available after version 1.6

![](https://cdn-images.tryhackme.com/user-uploads/6131132af49360005df01ae3/room-content/b5631ad5ee54c314297b1f343b2f2913.png)