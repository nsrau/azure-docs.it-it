<properties 
   pageTitle="Come creare route e attivare l&#39;inoltro IP in Azure"
   description="Come creare route e attivare l&#39;inoltro IP in Azure"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Come creare route e attivare l'inoltro IP in Azure
È possibile utilizzare i dispositivi virtuali in Azure per gestire il traffico della rete virtuale di Azure. Tuttavia, è necessario creare route che consentono le macchine virtuali e servizi cloud nella rete virtuale per l'invio di pacchetti per il dispositivo virtuale, anziché la destinazione desiderata per il pacchetto. È inoltre necessario attivare l'inoltro IP dell'accessorio virtuale macchina virtuale in modo da poter ricevere e inoltrare i pacchetti che non sono indirizzati al dispositivo virtuale effettivo macchina virtuale.

##Come gestire le route
È possibile aggiungere, rimuovere e modificare le route in Azure utilizzando PowerShell. Prima di poter creare una route, è necessario creare una tabella di route per ospitare la route.

### Come creare una tabella di route
Per creare una tabella di route denominata *FrontEndSubnetRouteTable*, eseguire il comando PowerShell seguente:

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### Come aggiungere una route a una tabella di route
Per aggiungere una route che imposta *10.1.1.10* come hop successivo per la *10.2.0.0/16* subnet nella tabella della route creata in precedenza, eseguire il comando PowerShell seguente:

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### Come associare una route a una subnet
Una tabella di route deve essere associata a una o più subnet per poter essere utilizzato. Per associare il *FrontEndSubnetRouteTable* tabella di route per una subnet denominata *FrontEndSubnet* nella rete virtuale *ProductionVnet*, eseguire il comando PowerShell seguente:

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### Come visualizzare le route applicate in una macchina virtuale
È possibile eseguire query di Azure per visualizzare le route effettive applicate per un'istanza di macchina virtuale o un ruolo specifica. Le route illustrate includono route predefinite Azure fornisce e route annunciati da un Gateway VPN. Il limite di route illustrato è 800.

Per visualizzare i percorsi associati alla NIC primario in una macchina virtuale denominata *FirewallAppliance1*, eseguire il comando PowerShell seguente:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

Per visualizzare i percorsi associati a una scheda di rete secondaria denominata *backendnic* su una macchina virtuale denominata *FirewallAppliance1*, eseguire il comando PowerShell seguente:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

Per visualizzare i percorsi associati alla NIC primaria in un'istanza del ruolo denominato *myRole* che fa parte di un servizio cloud denominato *myservice*, eseguire il comando PowerShell seguente:

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## Come gestire l'inoltro IP
Come accennato in precedenza, è necessario attivare l'inoltro IP su qualsiasi istanza di macchina virtuale o un ruolo che verrà utilizzato come un dispositivo virtuale.

### Come attivare l'inoltro IP
Per attivare l'inoltro IP in una macchina virtuale denominata *FirewallAppliance1*, eseguire il comando PowerShell seguente:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

Per attivare l'inoltro IP in un'istanza del ruolo denominato *FirewallAppliance1* in un servizio cloud denominato *myService*, eseguire il comando PowerShell seguente:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### Come disattivare l'inoltro IP
Per disattivare l'inoltro IP in una macchina virtuale denominata *FirewallAppliance1*, eseguire il comando PowerShell seguente:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

Per disattivare l'inoltro IP in un'istanza del ruolo denominato *FirewallAppliance1* in un servizio cloud denominato *myService*, eseguire il comando PowerShell seguente:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### Come visualizzare lo stato di inoltro IP
Per visualizzare lo stato dell'inoltro IP su una macchina virtuale denominata *FirewallAppliance1*, eseguire il comando PowerShell seguente:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## Vedere anche

[Definito dall'utente IP Cenni preliminari sull'inoltro e route](../virtual-networks-udr-overview)

[Indirizzi IP pubblici a livello di istanza](../virtual-networks-instance-level-public-ip)

[Panoramica di Rete virtuale](https://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=52-->
 