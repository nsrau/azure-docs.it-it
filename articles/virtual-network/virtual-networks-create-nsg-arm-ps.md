<properties 
   pageTitle="Come creare gruppi di sicurezza di rete in modalità ARM mediante PowerShell | Microsoft Azure"
   description="Informazioni su come creare e distribuire gruppi di sicurezza di rete in ARM mediante PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Come creare gruppi di sicurezza di rete in PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. È inoltre possibile [creare NSG nel modello di distribuzione classica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

I comandi di esempio PowerShell riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi così come sono visualizzati in questo documento, creare innanzitutto l'ambiente di test distribuendo [questo modello](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), fare clic su **Distribuisci in Azure**, sostituire i valori di parametro predefiniti, se necessario e seguire le istruzioni nel portale.

## Come creare il gruppo di sicurezza di rete per la subnet front-end
Per creare un gruppo di sicurezza di rete denominato *NSG-FrontEnd* in base allo scenario precedente, seguire la procedura riportata di seguito.

1. Se è la prima volta che si utilizza Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione desiderata.
2. Da un prompt di Azure PowerShell, eseguire il cmdlet **Switch-AzureMode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		Switch-AzureMode AzureResourceManager
	
	Output previsto:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Il cmdlet Switch-AzureMode verrà presto dichiarato obsoleto. Di conseguenza, tutti i cmdlet di Gestione risorse verranno rinominati.

3. Creare una regola di sicurezza che consente l'accesso da Internet alla porta 3389.

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. Creare una regola di sicurezza che consente l'accesso da Internet alla porta 80.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. Aggiungere le regole create in precedenza a un nuovo gruppo di sicurezza di rete denominato **NSG-FrontEnd**.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

	Output previsto (che non mostra le regole predefinite):

		Name                 : NSG-FrontEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-FrontEnd
		Etag                 : W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [	
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [ ... ]
		NetworkInterfaces    : []
		Subnets              : []		

6. Associare il gruppo di sicurezza di rete creato in precedenza per la subnet *front-end*.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	Output previsto che mostra solo le impostazioni della subnet *FrontEnd*, notare il valore per la proprietà **NetworkSecurityGroup**:

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

## Come creare il gruppo di sicurezza di rete per la subnet back-end
Per creare un gruppo di sicurezza di rete denominato *NSG-BackEnd* in base allo scenario precedente, seguire la procedura riportata di seguito.

1. Creare una regola di sicurezza che consente l'accesso dalla subnet front-end per la porta 1433 (porta predefinita utilizzata da SQL Server).

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. Creare una regola di sicurezza che blocca l'accesso a Internet.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. Aggiungere le regole create in precedenza a un nuovo gruppo di sicurezza di rete denominato **NSG-BackEnd**.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

	Output previsto (che non mostra le regole predefinite):

		Name                 : NSG-BackEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-BackEnd
		Etag                 : W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [
		                         {
		                           "Name": "frontend-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/frontend-rule",
		                           "Description": "Allow FE subnet",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "1433",
		                           "SourceAddressPrefix": "192.168.1.0/24",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
		                           "Description": "Block Internet",
		                           "Protocol": "*",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "*",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "Internet",
		                           "Access": "Deny",
		                           "Priority": 200,
		                           "Direction": "Outbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [...]
		NetworkInterfaces    : []
		Subnets              : []		

6. Associare il gruppo di sicurezza di rete creato in precedenza per la subnet *BackEnd*.

		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	Output previsto che mostra solo le impostazioni della subnet *BackEnd*, notare il valore per la proprietà **NetworkSecurityGroup**:

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ipConfigurations/ipconfig1"
                          },
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ipConfigurations/ipconfig1"
                          }
                        ],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

<!---HONumber=Oct15_HO2-->