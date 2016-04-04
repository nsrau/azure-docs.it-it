<properties 
   pageTitle="Gestire gruppi di sicurezza di rete con l&#39;interfaccia della riga di comando di Azure in Resource Manager | Microsoft Azure"
   description="Informazioni su come gestire gruppi di sicurezza di rete con l&#39;interfaccia della riga di comando di Azure in Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
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
   ms.author="telmos" />

# Gestire gruppi di sicurezza di rete tramite l'interfaccia della riga di comando di Azure

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Recuperare le informazioni

È possibile visualizzare i gruppi di sicurezza di rete \(NSG, Network Security Group\) esistenti, recuperare le regole relative a un NSG esistente e trovare le risorse a cui un NSG è associato.

### Visualizzare NSG esistenti

Per visualizzare l'elenco di NSG in un gruppo di risorse specifico, eseguire il comando `azure network nsg list` come illustrato di seguito.

	azure network nsg list --resource-group RG-NSG

Output previsto:

	info:    Executing command network nsg list
	+ Getting the network security groups
	data:    Name          Location
	data:    ------------  --------
	data:    NSG-BackEnd   westus
	data:    NSG-FrontEnd  westus
	info:    network nsg list command OK
		 
### Elencare tutte le regole per un NSG

Per visualizzare le regole di un NSG denominato **NSG-FrontEnd**, eseguire il comando `azure network nsg show` come illustrato di seguito.

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd

Output previsto:
	
	info:    Executing command network nsg show
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Name                            : NSG-FrontEnd
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    Tags                            : displayName=NSG - Front End
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
	data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

>[AZURE.NOTE] Per elencare le regole dell'NSG **NSG-FrontEnd**, è anche possibile usare `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd`.

### Visualizzare le associazioni di NSG

Per visualizzare le risorse a cui l'NSG **NSG-FrontEnd** è associato, eseguire il comando `azure network nsg show` come illustrato di seguito. Si noti che l'unica differenza è l'uso del parametro **--json**.

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json

Cercare le proprietà **networkInterfaces** e **subnets** come illustrato di seguito:

	"networkInterfaces": [],
	...
	"subnets": [
		{
			"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
		}
	],
	...

Nell'esempio precedente l'NSG non è associato ad alcuna interfaccia di rete \(scheda di interfaccia di rete\) ed è associato a una subnet denominata **FrontEnd**.

## Gestire le regole

È possibile aggiungere regole a un NSG esistente, modificare le regole esistenti e rimuovere regole.

### Aggiungere una regola

Per aggiungere una regola che consenta traffico **in ingresso** alla porta **443** da qualsiasi computer all'NSG **NSG-FrontEnd**, eseguire il comando `azure network nsg rule create` come illustrato di seguito.

	azure network nsg rule create --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--description "Allow access to port 443 for HTTPS" \
		--protocol Tcp \
		--source-address-prefix * \
		--source-port-range * \
		--destination-address-prefix * \
		--destination-port-range 443 \
		--access Allow \
		--priority 102 \
		--direction Inbound		

Output previsto:

	info:    Executing command network nsg rule create
	+ Looking up the network security rule "allow-https"
	+ Creating a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : *
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule create command OK

### Modificare una regola

Per modificare la regola creata in precedenza per consentire traffico in ingresso solo da **Internet**, eseguire il comando `azure network nsg rule set` come illustrato di seguito.

	azure network nsg rule set --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--source-address-prefix Internet

Output previsto:

	info:    Executing command network nsg rule set
	+ Looking up the network security group "NSG-FrontEnd"
	+ Setting a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : Internet
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule set command OK

### Eliminare una regola

Per eliminare la regola creata in precedenza, eseguire il comando `azure network nsg rule delete` come illustrato di seguito.

	azure network nsg rule delete --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--quiet

>[AZURE.NOTE] Il parametro **--quiet** fa in modo che non sia necessario confermare l'eliminazione.

Output previsto:

	info:    Executing command network nsg rule delete
	+ Looking up the network security group "NSG-FrontEnd"
	+ Deleting network security rule "allow-https"
	info:    network nsg rule delete command OK

## Gestire le associazioni

È possibile associare un NSG a subnet e schede di interfaccia di rete. È anche possibile annullare l'associazione tra un NSG e qualsiasi risorsa a cui è associato.

### Associare un NSG a una scheda di interfaccia di rete

Per associare l'NSG **NSG-FrontEnd** alla scheda di interfaccia di rete **TestNICWeb1**, eseguire il comando `azure network nic set` come illustrato di seguito.

	azure network nic set --resource-group RG-NSG \
		--name TestNICWeb1 \
		--network-security-group-name NSG-FrontEnd

Output previsto:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Looking up the network security group "NSG-FrontEnd"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### Annullare l'associazione tra un NSG e una scheda di interfaccia di rete

Per annullare l'associazione dell'NSG **NSG-FrontEnd** alla scheda di interfaccia di rete **TestNICWeb1**, eseguire il comando `azure network nic set` come illustrato di seguito.

	azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""

>[AZURE.NOTE] Si noti il valore "" \(vuoto\) per il parametro **network-security-group-id**, che consente di rimuovere un'associazione a un NSG. Non è possibile fare lo stesso con il parametro **network-security-group-name**.

Risultato previsto:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### Annullare l'associazione tra un NSG e una subnet

Per annullare l'associazione dell'NSG **NSG-FrontEnd** alla subnet **FrontEnd**, eseguire il comando `azure network vnet subnet set` come illustrato di seguito.

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-id ""

Output previsto:

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "FrontEnd"
	+ Setting subnet "FrontEnd"
	+ Looking up the subnet "FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:    Type                            : Microsoft.Network/virtualNetworks/subnets
	data:    ProvisioningState               : Succeeded
	data:    Name                            : FrontEnd
	data:    Address prefix                  : 192.168.1.0/24
	data:    IP configurations:
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
	data:
	info:    network vnet subnet set command OK

### Associare un gruppo di sicurezza di rete a una subnet

Per associare l'NSG **NSG-FrontEnd** alla subnet **FrontEnd**, eseguire il comando `azure network vnet subnet set` come illustrato di seguito.

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-name NSG-FronEnd

>[AZURE.NOTE] Il comando precedente funziona solo perché l'NSG **NSG-FrontEnd** si trova nello stesso gruppo di risorse della rete virtuale **TestVNet**. Se l'NSG si trova in un altro gruppo di risorse, è necessario usare invece il parametro **--network-security-group-id** e specificare l'ID completo per l'NSG. Per recuperare l'ID, eseguire **azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json** e cercare la proprietà **id**.

Output previsto:

		info:    Executing command network vnet subnet set
		+ Looking up the subnet "FrontEnd"
		+ Looking up the network security group "NSG-FrontEnd"
		+ Setting subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
		data:
		info:    network vnet subnet set command OK

## Eliminare un gruppo di sicurezza di rete

È possibile eliminare un NSG solo se non è associato ad alcuna risorsa. Per eliminare un NSG, seguire questa procedura.

1. Per controllare le risorse associate a un NSG, eseguire `azure network nsg show` come illustrato in [Visualizzare le associazioni di NSG](#View-NSGs-associations).
2. Se l'NSG è associato a una o più schede di interfacce di rete, eseguire `azure network nic set` come illustrato in [Annullare l'associazione tra un NSG e una scheda di interfaccia di rete](#Dissociate-an-NSG-from-a-NIC) per ogni scheda di interfaccia di rete. 
3. Se l'NSG è associato a una o più subnet, eseguire `azure network vnet subnet set` come illustrato in [Annullare l'associazione tra un NSG e una subnet](#Dissociate-an-NSG-from-a-subnet) per ogni subnet.
4. Per eliminare l'NSG, eseguire il comando `azure network nsg delete` come illustrato di seguito.

		azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet

	Output previsto:

		info:    Executing command network nsg delete
		+ Looking up the network security group "NSG-FrontEnd"
		+ Deleting network security group "NSG-FrontEnd"
		info:    network nsg delete command OK

## Passaggi successivi

- [Abilitare la registrazione](virtual-network-nsg-manage-log.md) per i gruppi di sicurezza di rete.

<!---HONumber=AcomDC_0323_2016-->