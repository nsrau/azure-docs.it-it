<properties 
   pageTitle="Come spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet"
   description="Informazioni su come spostare macchine virtuali e istanze del ruolo in un'altra subnet"
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
   ms.date="05/29/2015"
   ms.author="telmos" />

# Come spostare una macchina virtuale o un'istanza del ruolo in un'altra subnet

È possibile usare PowerShell per spostare le proprie macchine virtuali da una subnet a un'altra nella stessa rete virtuale (VNet). Le istanze del ruolo possono essere spostate modificando il file CSCFG invece di usare PowerShell.

Perché spostare le macchine virtuali in un'altra subnet? La migrazione in un'altra subnet è utile quando la subnet corrente è troppo piccola e non può essere espansa a causa delle macchine virtuali in esecuzione al suo interno. In tal caso, è possibile creare una nuova subnet più grande, migrarvi le macchine virtuali e al termine eliminare la precedente subnet ormai vuota.

## Come spostare una macchina virtuale in un'altra subnet

Per spostare una macchina virtuale, eseguire il cmdlet di PowerShell Set-AzureSubnet usando l'esempio seguente come modello. In tale esempio TestVM viene spostata dalla subnet corrente a Subnet-2. Ricordarsi di modificare l'esempio in base all'ambiente in uso. Si noti che, ogni volta che il cmdlet Update-AzureVM viene eseguito in una procedura, riavvierà la macchina virtuale come parte del processo di aggiornamento.

	Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
	| Set-AzureSubnet –SubnetNames Subnet-2 `
	| Update-AzureVM

Se è stato specificato un indirizzo DIP statico per la macchina virtuale, sarà necessario cancellare tale impostazione prima di poter spostare la macchina virtuale in una nuova subnet. In questo caso, usare quanto segue:

	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM
	Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
	| Set-AzureSubnet -SubnetNames Subnet-2 `
	| Update-AzureVM

## Per spostare un'istanza del ruolo in un'altra subnet

Per spostare un'istanza del ruolo, modificare il file CSCFG. Nell'esempio seguente "Role0" nella rete virtuale *VNETName* viene spostato dalla subnet corrente a *Subnet-2*. Poiché l'istanza del ruolo è già stata distribuita, sarà necessario solo modificare Subnet name = Subnet-2. Ricordarsi di modificare l'esempio in base all'ambiente in uso.

	<NetworkConfiguration>
	    <VirtualNetworkSite name="VNETName" />
	    <AddressAssignments>
	       <InstanceAddress roleName="Role0">
	            <Subnets><Subnet name="Subnet-2" /></Subnets>
	       </InstanceAddress>
	    </AddressAssignments>
	</NetworkConfiguration> 

<!---HONumber=July15_HO2-->