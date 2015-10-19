<properties 
   pageTitle="Come creare route e attivare l'inoltro IP in Azure"
   description="Informazioni su come gestire UDR e l'inoltro IP"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2015"
   ms.author="telmos" />

# Come creare route e attivare l'inoltro IP in Azure
È possibile utilizzare i dispositivi virtuali in Azure per gestire il traffico della rete virtuale di Azure. Tuttavia, è necessario creare route che consentono le macchine virtuali e servizi cloud nella rete virtuale per l'invio di pacchetti per il dispositivo virtuale, anziché la destinazione desiderata per il pacchetto. È inoltre necessario attivare l'inoltro IP dell'accessorio virtuale macchina virtuale in modo da poter ricevere e inoltrare i pacchetti che non sono indirizzati al dispositivo virtuale effettivo macchina virtuale.

## Come gestire le route
È possibile aggiungere, rimuovere e modificare le route in Azure utilizzando PowerShell. Prima di poter creare una route, è necessario creare una tabella di route per ospitare la route.

### Come creare una tabella di route
Per creare una tabella di route denominata *FrontEndSubnetRouteTable*, eseguire il comando PowerShell seguente:

	```powershell
	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
		-Location uscentral `
		-Label "Route table for front end subnet"
	```

L'output del comando precedente dovrebbe essere simile al seguente:

	Name                      Location   Label                          
	----                      --------   -----                          
	FrontEndSubnetRouteTable  West US    Route table for front end subnet

### Come aggiungere una route a una tabella di route
Per aggiungere una route che imposta *10.1.1.10* come hop successivo per la *10.2.0.0/16* subnet nella tabella della route creata in precedenza, eseguire il comando PowerShell seguente:

	```powershell
	Get-AzureRouteTable FrontEndSubnetRouteTable `
		|Set-AzureRoute -RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
		-NextHopType VirtualAppliance `
		-NextHopIpAddress 10.1.1.10
	```

L'output del comando precedente dovrebbe essere simile al seguente:

	Name     : FrontEndSubnetRouteTable
	Location : Central US
	Label    : Route table for frontend subnet
	Routes   : 
	           Name                 Address Prefix    Next hop type        Next hop IP address
	           ----                 --------------    -------------        -------------------
	           firewallroute        10.2.0.0/16       VirtualAppliance     10.1.1.10    

### Come associare una route a una subnet
Una tabella di route deve essere associata a una o più subnet per poter essere utilizzato. Per associare il *FrontEndSubnetRouteTable* tabella di route per una subnet denominata *FrontEndSubnet* nella rete virtuale *ProductionVnet*, eseguire il comando PowerShell seguente:

	```powershell
	Set-AzureSubnetRouteTable -VirtualNetworkName ProductionVnet `
		-SubnetName FrontEndSubnet `
		-RouteTableName FrontEndSubnetRouteTable
	```

### Come visualizzare le route applicate in una macchina virtuale
È possibile eseguire query di Azure per visualizzare le route effettive applicate per un'istanza di macchina virtuale o un ruolo specifica. Le route illustrate includono route predefinite Azure fornisce e route annunciati da un Gateway VPN. Il limite di route illustrato è 800.

Per visualizzare i percorsi associati alla scheda di interfaccia di rete primaria in una macchina virtuale denominata *FWAppliance1*, eseguire il comando PowerShell seguente:

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable
	```

L'output del comando precedente dovrebbe essere simile al seguente:

	Effective routes : 
	                   Name            Address Prefix    Next hop type    Next hop IP address Status   Source     
	                   ----            --------------    -------------    ------------------- ------   ------     
	                                   {10.0.0.0/8}      VNETLocal                            Active   Default    
	                                   {0.0.0.0/0}       Internet                             Active   Default    
	                                   {25.0.0.0/8}      Null                                 Active   Default    
	                                   {100.64.0.0/10}   Null                                 Active   Default    
	                                   {172.16.0.0/12}   Null                                 Active   Default    
	                                   {192.168.0.0/16}  Null                                 Active   Default    
	                   firewallroute   {10.2.0.0/16}     Null             10.1.1.10           Active   User      

Per visualizzare i percorsi associati a una scheda di interfaccia di rete secondaria denominata *backendnic* su una macchina virtuale denominata *FWAppliance1*, eseguire il comando PowerShell seguente:

	```powershell
	Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
		| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic
	```

Per visualizzare i percorsi associati alla NIC primaria in un'istanza del ruolo denominato *myRole* che fa parte di un servizio cloud denominato *ProductionVMs*, eseguire il comando PowerShell seguente:

	```powershell
	Get-AzureEffectiveRouteTable -ServiceName ProductionVMs `
		-RoleInstanceName myRole
	```

## Come gestire l'inoltro IP
Come accennato in precedenza, è necessario attivare l'inoltro IP su qualsiasi istanza di macchina virtuale o un ruolo che verrà utilizzato come un dispositivo virtuale.

### Come attivare l'inoltro IP
Per attivare l'inoltro IP in una macchina virtuale denominata *FWAppliance1*, eseguire il comando PowerShell seguente:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Set-AzureIPForwarding -Enable
```

Per attivare l'inoltro IP in un'istanza del ruolo denominato *FWAppliance* in un servizio cloud denominato *DMZService*, eseguire il comando PowerShell seguente:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Enable
```

### Come disattivare l'inoltro IP
Per disattivare l'inoltro IP in una macchina virtuale denominata *FWAppliance1*, eseguire il comando PowerShell seguente:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName DMZService `
	| Set-AzureIPForwarding -Disable
```

Per disattivare l'inoltro IP in un'istanza del ruolo denominato *FWAppliance* in un servizio cloud denominato *DMZService*, eseguire il comando PowerShell seguente:

```powershell
Set-AzureIPForwarding -ServiceName DMZService `
	-RoleName FWAppliance -Disable
```

### Come visualizzare lo stato di inoltro IP
Per visualizzare lo stato dell'inoltro IP su una macchina virtuale denominata *FWAppliance1*, eseguire il comando PowerShell seguente:

```powershell
Get-AzureVM -Name FWAppliance1 -ServiceName ProductionVMs `
	| Get-AzureIPForwarding
``` 

<!---HONumber=Oct15_HO2-->