<h1>Virtual Networking in Azure</h1>
In this lab, I will create virtual networks, subnets, configure a network and an application ssecurity group as well as configure DNS zones.

<h2>Networking Diagram</h2>
<img src="https://imgur.com/8XzQ9KI.png" alt="networking diagram" />

<h2>Steps</h2>

- In the Azure portal, search and select Virtual Networks or vnet
- Select Create
- Fill in the following fields: resource group (name must be globally unique), virtual network name (CoreServicesVnet), region (eg. US East)
- Choose the IP addresses tab and put in 10.20.0.0/16
- Delete the default subnet and create 2 new ones, Name: SharedServicesSubnet 10.20.10.0/24 and Name: DatabaseSubnet 10.20.20.0/24


