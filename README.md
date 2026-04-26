<h1>Virtual Networking in Azure</h1>
In this lab, I will create virtual networks, subnets, configure a network and an application security group as well as configure DNS zones.

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
<img src="https://imgur.com/sM9GQmQ.png" alt="nsg inbound rules list" />
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
- Choose the correct resource group eg. az104-rg4, give it a unique name eg. contoso102938.com
- Select review + create and create
- After DNS has finished deploying, click Go to resource
- Copy or note down one of the name servers eg. ns1-02.azure.dns.com
- Expand DNS management then Recordsets
- Click Add+
- Fill in the following:
  - Leave Type, alias, TTL fields at default
  - Name: www
  - IP address: 10.1.1.14
 
<img src="https://imgur.com/xe0qhqn.png" alt="public dns zone" />

- Click add
- Open a command prompt on your own machine
- Enter the command: nslookup [domain name] [name server]
- Verify that the domain name resolves to the specified IP address 10.1.1.14

<img src="https://imgur.com/sAl4A62.png" alt="nslookup command example" />

- Search Private dns zones in the Azure portal
- Select Create+
- Choose the correct resource group eg. az104-rg4, give it a unique name eg. private.contoso102938.com
- Select review + create and create
- After the resource is deployed, click Go to resource
- Expand the DNS management blade, then Virtual network links
- Configure:
  - Link name: manufacturing-link
  - Virtual network: ManufacturingVnet
- Click create
- Expand the DNS management > Recordsets
- Add virtual machines that need private name resolution
  - Name: sensor-vm
  - IP address: 10.1.1.14
  
<img src="https://imgur.com/qf06th2.png" alt="private dns zone" />

<h2>Summary of lab</h2>
A virtual network named CoreServicesNet with an IP address of 10.20.0.0/16 was created inside a new resource group named az104-rg4. Within CoreServicesNet, 2 subnets were created named SharedServicesSubnet with an IP address of 10.20.10.0/24 and DatabaseSubnet with an IP address of 10.20.20.0/24. ARM templates and parameters of this vnet were exported and modified to create another vnet called ManufacturingVnet with an IP address of 10.30.0.0/16. 2 subnets within this vnet were also created from the templates named SensorSubnet1 with an IP address of 10.30.20.0/24 and SensorSubnet2 with an IP address of 10.30.21.0/24. 
<br><br>
To ensure traffic was secure between virtual networks and virtual machines, a network security group was created for CoreServicesNet and associated with the SharedServicesSubnet. An application security group was created and applied to the inbound security rules of the network security group to allow only web-based traffic, for either unencrypted or encrypted connections. An outbound security rule to deny internet access was also created.
<br><br>
To allow public domain name resolution, a public DNS zone was created. The A record was modified to point to a specific IP address, which in reality would have been an IP address of a web server. Also, to allow machines within the network to utilise DNS, a private DNS zone was created. This zone was configured to the ManufacturingVnet and specified an IP address for a virtual machine in this vnet. In reality, the IP address would be one that specified a VM for a manufacturing device.



