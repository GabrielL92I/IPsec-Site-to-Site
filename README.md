# Site-to-Site VPN using IPsec + PSK on Mikrotik by Gabriel Lami
Configure Site to Site(Main Office and Branch Office) VPN connection using IPsec + PSK

Site A - Main Office | Site B - Branch Office

1- First, we will create Policy, Proposal, Peer, Identities, Profile. `IP->>IPsec`

 - Profile Creation
![Profile](https://user-images.githubusercontent.com/44748406/193414552-2e201c25-00ea-48b0-8d8b-62ed61cbed36.png)
 - Peer creation
![peer](https://user-images.githubusercontent.com/44748406/193414750-deb072fa-b5e0-4a8f-8835-b1a79168329f.png)
 - Identity creation(Pre-shared key must be same on both routers)
![identity](https://user-images.githubusercontent.com/44748406/193414860-47b7c8c5-22e8-4030-a28c-5ead38a0c61e.png)
 - Proposal creation
![proposal](https://user-images.githubusercontent.com/44748406/193415219-f5ead7d0-7219-419c-8b08-8c2e3efe4426.png)
 - Policy creation(Src is local lan of site A and Dst is local lan of Site B, Src is local lan of site B and Dst is local lan of Site A)
![policy](https://user-images.githubusercontent.com/44748406/193415227-ce5ec561-afce-416f-a1c4-4781ef1dcde9.png)

2- Secondly, we have to create the firewall and NAT rules for the local and remote site lan's to be able to communicate and ping with each other).

- Creation of the firewall filter rule. `IP->Firewall->Filter Rules`

  -Chain: Forward
  -IPsec Policy: IN / ipsec
  -Action: Accept

  -Chain: Forward
  -IPsec Policy: OUT / ipsec
  -Action: Accept

  -Chain: Forward
  -Connection State: estabilished, related
  -Action: fasttrack connection

  -Chain: Forward
  -Connection State: estabilished, related
  -Action: Accept

  -Chain: Input
  -Src. Address: 192.168.70.0/24
  -Action: Accept

  -Chain: Input
  -Src. Address: 192.168.1.0/24
  -Action: Accept

![firewall](https://user-images.githubusercontent.com/44748406/193416342-1a589747-90fb-4813-a612-6dabf1647542.png)

- Creation of the NAT rule(only if you don't have this rule already). `IP->Firewall->NAT`

  -SITE A

  -Chain: srcnat
  -Src. 192.168.70.0/24
  -Dst. 192.168.1.0/24
  -Action: Accept

  -Chain: srcnat
  -Out. Interface: ether1-WAN
  -Action: masquerade


  -SITE B

  -Chain: srcnat
  -Src. 192.168.1.0/24
  -Dst. 192.168.70.0/24
  -Action: Accept

  -Chain: srcnat
  -Out. Interface: ether1-WAN
  -Action: masquerade
  
![nat](https://user-images.githubusercontent.com/44748406/193416374-c3bce23b-ff75-4f97-b879-b3856487d4ae.png)
  
3- Third, we will create routes.

![routes](https://user-images.githubusercontent.com/44748406/193416820-a70822f8-0f0e-4013-a32e-e829c48330c8.png)

  
  Finally everything should be OK and you will be able to ping and access resources between two Sites and local networks.
  
  Both sites authenticated and connected
  ![connection](https://user-images.githubusercontent.com/44748406/193417048-2dacda41-06fa-4efe-8497-c9b531a344e9.png)
  
  Lan's between sites can ping each other
  ![pingok](https://user-images.githubusercontent.com/44748406/193417165-8b2fc338-e386-4b1c-a8f1-81053801fd97.png)
 

  Thank you.
