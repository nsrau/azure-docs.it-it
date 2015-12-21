<properties 
   pageTitle="Come impostare un indirizzo IP privato interno statico"
   description="Informazioni sugli indirizzi IP interni statici (DIP) e su come gestirli"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos" />

# Come impostare un indirizzo IP privato interno statico
Nella maggior parte dei casi non è necessario specificare un indirizzo IP interno statico per la macchina virtuale. Le macchine virtuali in una rete virtuale infatti ricevono automaticamente un indirizzo IP interno da un intervallo specificato. In alcuni casi è tuttavia opportuno specificare un indirizzo IP statico per una determinata macchina virtuale, ad esempio se questa eseguirà DNS o sarà un controller di dominio.

>[AZURE.NOTE]Un indirizzo IP interno statico resta associato alla macchina virtuale anche in caso di passaggio allo stato di arresto/deprovisioning.

## Come verificare la disponibilità di uno specifico indirizzo IP
Per verificare se l'indirizzo IP *10.0.0.7* è disponibile in una rete virtuale denominata *TestVnet*, eseguire il comando PowerShell seguente e controllare il valore per *IsAvailable*:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE]Se si vuole testare il comando precedente in un ambiente sicuro, seguire le linee guida contenute in [Creazione di una rete virtuale](../virtual-network/virtual-networks-create-vnet.md) per creare una rete virtuale denominata *TestVnet* e assicurarsi che usi lo spazio degli indirizzi *10.0.0.0/8*.

## Come specificare un indirizzo IP interno statico durante la creazione di una macchina virtuale
Lo script PowerShell seguente crea un nuovo servizio cloud denominato *TestService*, recupera un'immagine da Azure, quindi crea una macchina virtuale *TestVM* nel nuovo servizio cloud usando l'immagine recuperata, imposta la macchina virtuale in modo che sia posizionata nella subnet *Subnet-1* e infine imposta *10.0.0.7* come indirizzo IP interno statico per la macchina virtuale:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## Come recuperare le informazioni relative all'indirizzo IP interno statico per una macchina virtuale
Per visualizzare le informazioni relative all'indirizzo IP interno statico per la macchina virtuale creata con lo script precedente, eseguire il comando PowerShell seguente e osservare i valori per *IpAddress*:

	Get-AzureVM -Name TestVM -ServiceName TestService

	DeploymentName              : TestService
	Name                        : TestVM
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 10.0.0.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : TestVM
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## Come rimuovere un indirizzo IP interno statico da una macchina virtuale
Per rimuovere l'indirizzo IP interno statico aggiunto alla macchina virtuale nello script precedente, eseguire il comando PowerShell seguente:
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## Come aggiungere un indirizzo IP interno statico a una macchina virtuale esistente
Per aggiungere un indirizzo IP interno statico alla macchina virtuale creata usando lo script precedente, eseguire il comando seguente:

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## Passaggi successivi

[IP riservato](../virtual-networks-reserved-public-ip)

[IP pubblico a livello di istanza (ILPIP)](../virtual-networks-instance-level-public-ip)

[API REST di IP riservati](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=AcomDC_1210_2015-->