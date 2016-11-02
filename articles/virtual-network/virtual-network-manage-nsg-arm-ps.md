<properties 
   pageTitle="Gestire NSG tramite PowerShell in Resource Manager | Microsoft Azure"
   description="Informazioni su come gestire NSG tramite PowerShell in Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>  
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jdial" />  

# Gestire NSG usando PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Recuperare le informazioni

È possibile visualizzare i gruppi di sicurezza di rete (NSG, Network Security Group) esistenti, recuperare le regole relative a un NSG esistente e trovare le risorse a cui un NSG è associato.

### Visualizzare NSG esistenti
Per visualizzare tutti gli NSG esistenti in una sottoscrizione, eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` come illustrato di seguito.

	Get-AzureRmNetworkSecurityGroup

Risultato previsto:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


Per visualizzare l'elenco di NSG in un gruppo di risorse specifico, eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` come illustrato di seguito.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Output previsto:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### Elencare tutte le regole per un NSG

Per visualizzare le regole di un NSG denominato **NSG-FrontEnd**, eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` come illustrato di seguito.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Output previsto:
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] Per elencare le regole predefinite dell'NSG **NSG-FrontEnd**, è anche possibile usare `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules`.

### Visualizzare le associazioni di NSG

Per visualizzare le risorse a cui l'NSG **NSG-FrontEnd** è associato, eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` come illustrato di seguito.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Cercare le proprietà **NetworkInterfaces** e **Subnets** come illustrato di seguito:

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

Nell'esempio precedente, l'NSG non è associato ad alcuna interfaccia di rete (NIC) ed è associato a una subnet denominata **FrontEnd**.

## Gestire le regole

È possibile aggiungere regole a un NSG esistente, modificare le regole esistenti e rimuovere regole.

### Aggiungere una regola

Per aggiungere una regola che consenta traffico **in ingresso** alla porta **443** da qualsiasi computer all'NSG **NSG-FrontEnd**, attenersi alla procedura seguente.

1. Eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` per recuperare l'NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Eseguire il cmdlet `Add-AzureRmNetworkSecurityRuleConfig` come illustrato di seguito.

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Per salvare le modifiche apportate all'NSG, eseguire il cmdlet `Set-AzureRmNetworkSecurityGroup` come illustrato di seguito.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Output previsto che mostra solo le regole di sicurezza:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Modificare una regola

Per modificare la regola creata in precedenza per consentire traffico in ingresso solo da **Internet**, attenersi alla procedura seguente.

1. Eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` per recuperare l'NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Eseguire il cmdlet `Set-AzureRmNetworkSecurityRuleConfig` come illustrato di seguito.

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Per salvare le modifiche apportate all'NSG, eseguire il cmdlet `Set-AzureRmNetworkSecurityGroup` come illustrato di seguito.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Output previsto che mostra solo le regole di sicurezza:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Eliminare una regola

1. Eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` per recuperare l'NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Eseguire il cmdlet `Remove-AzureRmNetworkSecurityRuleConfig` come illustrato di seguito.

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. Per salvare le modifiche apportate all'NSG, eseguire il cmdlet `Set-AzureRmNetworkSecurityGroup` come illustrato di seguito.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Output previsto che mostra solo le regole di sicurezza. Si noti che **https-rule** non è più presente:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## Gestire le associazioni

È possibile associare un NSG a subnet e schede di interfaccia di rete. È anche possibile annullare l'associazione tra un NSG e qualsiasi risorsa a cui è associato.

### Associare un NSG a una NIC

Per associare l'NSG **NSG-FrontEnd** alla NIC **TestNICWeb1** attenersi alla procedura seguente.

1. Eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` per recuperare l'NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Eseguire il cmdlet `Get-AzureRmNetworkInterface` per recuperare la NIC esistente e archiviarla in una variabile, come illustrato di seguito.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **NIC** sul valore della variabile **NSG**, come illustrato di seguito.

		$nic.NetworkSecurityGroup = $nsg

4. Per salvare le modifiche apportate alla NIC, eseguire il cmdlet `Set-AzureRmNetworkInterface` come illustrato di seguito.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Output previsto che mostra solo la proprietà **NetworkSecurityGroup**:

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### Annullare l'associazione tra un NSG e una NIC

Per annullare l'associazione tra l'NSG **NSG-FrontEnd** e la NIC **TestNICWeb1** attenersi alla procedura seguente.

1. Eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` per recuperare l'NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Eseguire il cmdlet `Get-AzureRmNetworkInterface` per recuperare la NIC esistente e archiviarla in una variabile, come illustrato di seguito.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **NIC** su **$null**, come illustrato di seguito.

		$nic.NetworkSecurityGroup = $null

4. Per salvare le modifiche apportate alla NIC, eseguire il cmdlet `Set-AzureRmNetworkInterface` come illustrato di seguito.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Output previsto che mostra solo la proprietà **NetworkSecurityGroup**:

		NetworkSecurityGroup : null

### Annullare l'associazione tra un NSG e una subnet

Per annullare l'associazione tra l'NSG **NSG-FrontEnd** e la subnet **FrontEnd** attenersi alla procedura seguente.

1. Eseguire il cmdlet `Get-AzureRmVirtualNetwork` per recuperare la rete virtuale esistente e archiviarla in una variabile, come illustrato di seguito.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Eseguire il cmdlet `Get-AzureRmVirtualNetworkSubnetConfig` per recuperare la subnet **FrontEnd** esistente e archiviarla in una variabile, come illustrato di seguito.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **subnet** su **$null**, come illustrato di seguito.

		$subnet.NetworkSecurityGroup = $null

4. Per salvare le modifiche apportate alla subnet, eseguire il cmdlet `Set-AzureRmVirtualNetwork` come illustrato di seguito.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Output previsto che mostra solo le proprietà della subnet **FrontEnd**. Si noti che non esiste una proprietà per **NetworkSecurityGroup**:

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### Associare un gruppo di sicurezza di rete a una subnet

Per associare di nuovo l'NSG **NSG-FrontEnd** alla subnet **FronEnd** attenersi alla procedura seguente.

1. Eseguire il cmdlet `Get-AzureRmVirtualNetwork` per recuperare la rete virtuale esistente e archiviarla in una variabile, come illustrato di seguito.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Eseguire il cmdlet `Get-AzureRmVirtualNetworkSubnetConfig` per recuperare la subnet **FrontEnd** esistente e archiviarla in una variabile, come illustrato di seguito.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. Eseguire il cmdlet `Get-AzureRmNetworkSecurityGroup` per recuperare l'NSG esistente e archiviarlo in una variabile, come illustrato di seguito.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. Impostare la proprietà **NetworkSecurityGroup** della variabile **subnet** su **$null**, come illustrato di seguito.

		$subnet.NetworkSecurityGroup = $nsg

4. Per salvare le modifiche apportate alla subnet, eseguire il cmdlet `Set-AzureRmVirtualNetwork` come illustrato di seguito.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Output previsto che mostra solo la proprietà **NetworkSecurityGroup** della subnet **FrontEnd**:

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## Eliminare un gruppo di sicurezza di rete

È possibile eliminare un NSG solo se non è associato ad alcuna risorsa. Per eliminare un NSG, seguire questa procedura.

1. Per controllare le risorse associate a un NSG, eseguire `azure network nsg show` come illustrato in [Visualizzare le associazioni di NSG](#View-NSGs-associations).
2. Se l'NSG è associato a una o più NIC, eseguire `azure network nic set` come illustrato in [Annullare l'associazione tra un NSG e una NIC](#Dissociate-an-NSG-from-a-NIC) per ognuna delle NIC.
3. Se l'NSG è associato a una o più subnet, eseguire `azure network vnet subnet set` come illustrato in [Annullare l'associazione tra un NSG e una subnet](#Dissociate-an-NSG-from-a-subnet) per ognuna delle subnet.
4. Per eliminare l'NSG, eseguire il cmdlet `Remove-AzureRmNetworkSecurityGroup` come illustrato di seguito.

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] Il parametro **-Force** fa in modo che non sia necessario confermare l'eliminazione.

## Passaggi successivi

- [Abilitare la registrazione](virtual-network-nsg-manage-log.md) per gli NSG.

<!---HONumber=AcomDC_0810_2016-->