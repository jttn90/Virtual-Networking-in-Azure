<h1>Virtual Networking in Azure</h1>
In this lab, I will create virtual networks, subnets, configure a network and an application ssecurity group as well as configure DNS zones.

<h2>Networking Diagram</h2>
<img src="https://imgur.com/8XzQ9KI.png" alt="networking diagram" />

<h2>Steps</h2>

<h3>Creating the first virtual network</h3>

- In the Azure portal, search and select Virtual Networks or vnet
- Select Create
- Fill in the following fields: resource group (name must be globally unique), virtual network name (CoreServicesVnet), region (eg. US East)
- Choose the IP addresses tab and put in 10.20.0.0/16
- Delete the default subnet and create 2 new ones, Name: SharedServicesSubnet, IP: 10.20.10.0/24 and Name: DatabaseSubnet, IP: 10.20.20.0/24
- Review + create and select create

<h3>Deploying a virtual network with ARM templates</h3>

- Select Go to resource or search for vnet then select CoreServicesVnet
- Go to Automation then Export template
- Ensure ARM template is selected and download the template
- Select Parameters and download that as well
- Open the Template.json file in your favourite editor eg. Visual Studio Code
- Search and replace the following:
  - CoreServicesVnet with ManufacturingVnet
  - SharedServiceSubnet with SensorSubnet1
  - DatabaseSubnet with SensorSubnet2
  - 10.20.0.0 with 10.30.0.0
  - 10.20.10.0 with 10.30.20.0
  - 10.20.20.0 with 10.30.21.0

 <img src="https://imgur.com/r4hhPpI.png" alt="template.json file" />
  
- Check that everything is correct then Save
- Open the Parameters.json file
- Search and replace the following:
  - CoreServicesVnet with ManufacturingVnet
 
<img src="https://imgur.com/2FgFOKd.png" alt="parameters.json file" />

- Save changes  
- In the Azure portal, search "Deploy a custom template"
- Select "Build your own template in the editor"
- Load the Template.json file and save
- Select edit parameters and load the Parameters.json file and save
- Ensure the correct resource group is selected eg. az104-rg4
- Select review + create and Create

<h3>Configuring a network and application security group</h3>

- Search for Application security groups in the Azure portal
- Click Create
- Choose the correct resource group eg. az104-rg4, give it a name eg. asg-web, choose the same region eg. US East
- Select review + create and Create
- Search for Network security groups or nsg in the Azure portal
- Click Create
- Choose the correct resource group eg. az104-rg4, give it a name eg. myNSGSecure, choose the same region eg. US East
- Select review + create and Create
- Select Go to resource for the newly created network security group
- Choose Settings > Subnets > +Associate
  - Virtual network: CoreServicesVnet, Subnet: SharedServiceSubnet
- Click OK
- Under settings of the current network security group, click on Inbound security rules then +Add
- Enter the following
  - Source: Application security group
  - Source application security groups: choose your newly create application security group eg. asg-web
  - Leave source, destination and service at default
  - Destination port ranges: 80,443
  - Protocol: TCP
  - Leave action and priority at defaults
  - Give it a name eg. AllowASG
<img src="https://imgur.com/CwFkD5Y.png" alt="nsg inbound rules" />

- Click Add

<br>
<img src="https://imgur.com/lVuMMVp.png" alt="nsg inbound rules list" />
<br>

- Under settings of the current network security group, click on Outbound security rules then +Add
- Enter the following
  - Leave source, source port ranges and protocol at default
  - Destination: Service Tag
  - Destination service tag: Internet
  - Destination port ranges: *
  - Action: Deny
  - Priority: 4096
  - Give it a name eg. DenyInternetOutbound
<img src="https://imgur.com/9QQTnFI.png" alt="nsg outbound rules" />

- Click add

<br>
<img src="https://imgur.com/hDHrwiw.png" alt="nsg outbound rules list" />
<br>

<h3>Configuring public and private DNS zones</h3>

- Search DNS zones in the Azure portal
- Click Create
- Enter the following
  - Choose the correct resource group eg. az104-rg4, give it a unique name eg. contoso1029.com
- Review + create and create
- After DNS has finished deploying, click Go to resource
- Copy or note down one of the name servers eg. ns1-02.azure.dns.com
- Expand DNS management then Recordsets
- Click Add+
- Fill in the following:
  - Leave Type, alias, TTL fields at default
  - Name: www
  - IP address: 10.1.1.14
- Click add
- Open a command prompt on your own machine
- Enter the command: nslookup <domain name> <name server>
- Verify that the domain name resolves to the specified IP address 10.1.1.14

<img src="" alt="" />
<img src="" alt="" />
<img src="" alt="" />
