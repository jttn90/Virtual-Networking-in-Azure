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
- Delete the default subnet and create 2 new ones, Name: SharedServicesSubnet 10.20.10.0/24 and Name: DatabaseSubnet 10.20.20.0/24
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
- Select review+create and Create
