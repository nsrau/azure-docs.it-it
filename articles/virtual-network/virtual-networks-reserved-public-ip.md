<properties
   pageTitle="IP riservato | Microsoft Azure"
   description="Informazioni sugli indirizzi IP riservati e su come gestirli"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="jdial" />

# Panoramica degli indirizzi IP riservati
Gli indirizzi IP in Azure rientrano in due categorie: indirizzi dinamici e indirizzi riservati. Gli indirizzi IP pubblici gestiti da Azure sono dinamici per impostazione predefinita. Questo significa che l'indirizzo IP usato per un determinato servizio cloud (VIP) oppure per accedere direttamente a una macchina virtuale o a un'istanza del ruolo (ILPIP) può cambiare di tanto in tanto.

Per impedire che gli indirizzi IP cambino, è possibile riservarli. Gli indirizzi IP riservati possono essere usati solo come indirizzi VIP, pertanto assicurano che l'indirizzo IP per il servizio cloud resti invariato anche se con il passare del tempo le risorse vengono arrestate o deallocate. È inoltre possibile convertire in indirizzo IP riservato gli indirizzi IP dinamici esistenti usati come indirizzi VIP.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Informazioni su come riservare un indirizzo IP pubblico statico usando [il modello di distribuzione di Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Verificare di conoscere il funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) in Azure.

## Quando è necessario un indirizzo IP riservato?
- **Si vuole essere certi che l'indirizzo IP sia riservato nella sottoscrizione**. Se si intende riservare un indirizzo IP in modo che non venga rilasciato dalla sottoscrizione in alcuna circostanza, è consigliabile usare un indirizzo IP pubblico riservato.
- **Si vuole che l'indirizzo IP resti associato al servizio cloud anche se lo stato delle macchine virtuali è arrestato o deallocato**. Se si vuole che il servizio cloud sia accessibile mediante l'uso di un indirizzo IP che non cambia anche se le macchine virtuali nel servizio cloud vengono arrestate o deallocate.
- **Si vuole essere certi che il traffico in uscita da Azure usi un indirizzo IP prevedibile**. È possibile che il firewall locale sia configurato per consentire solo il traffico proveniente da indirizzi IP specifici. Riservando un indirizzo IP, si conoscerà l'indirizzo IP di origine e non sarà necessario aggiornare le regole del firewall a seguito del cambiamento di indirizzo IP.

## Domande frequenti
1. È possibile usare un indirizzo IP riservato per tutti i servizi di Azure?
  - Gli indirizzi IP riservati possono essere usati solo per le macchine virtuali e per i ruoli delle istanze del servizio cloud esposti mediante un indirizzo VIP.
1. Di quanti indirizzi IP riservati è possibile disporre?
  - Attualmente tutte le sottoscrizioni di Azure sono autorizzate a usare 20 indirizzi IP riservati. È tuttavia possibile richiedere altri indirizzi IP riservati. Per altre informazioni, vedere la pagina relativa ai [Sottoscrizione e limiti del servizio](../azure-subscription-service-limits.md).
1. È previsto un addebito per gli indirizzi IP riservati?
  - Per informazioni sui prezzi, vedere [Prezzi per gli indirizzi IP riservati](http://go.microsoft.com/fwlink/?LinkID=398482).
1. Come è possibile riservare un indirizzo IP?
  - È possibile usare PowerShell o l'[API REST di gestione di Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) per riservare un indirizzo IP di una determinata area. L'indirizzo IP riservato è associato alla sottoscrizione. Non è possibile riservare un indirizzo IP tramite il portale di gestione.
1. È possibile usare gli indirizzi IP riservati con reti virtuali basate su gruppi di affinità?
  - Gli indirizzi IP riservati sono supportati solo nelle reti virtuali di area. Non sono supportati per le reti virtuali associate a gruppi di affinità. Per altre informazioni sull'associazione di una rete virtuale a una regione o a un gruppo di affinità, vedere [Informazioni sulle reti virtuali di area e sui gruppi di affinità](virtual-networks-migrate-to-regional-vnet.md).

## Come gestire gli indirizzi VIP riservati

Prima di poter usare gli indirizzi IP riservati, è necessario aggiungerli alla propria sottoscrizione. Per creare un indirizzo IP riservato dal pool di indirizzi IP pubblici disponibili nella località (location) *Central US*, eseguire il comando PowerShell seguente:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Si noti tuttavia che non è possibile specificare quale indirizzo IP riservare. Per visualizzare gli indirizzi IP riservati nella propria sottoscrizione, eseguire il comando PowerShell seguente e osservare i valori per *ReservedIPName* e *Address*:

	Get-AzureReservedIP

	ReservedIPName       : MyReservedIP
	Address              : 23.101.114.211
	Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
	Label                :
	Location             : Central US
	State                : Created
	InUse                : False
	ServiceName          :
	DeploymentName       :
	OperationDescription : Get-AzureReservedIP
	OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
	OperationStatus      : Succeeded

Dopo essere stato riservato, un indirizzo IP rimane associato alla sottoscrizione finché non viene eliminato. Per eliminare l'indirizzo IP riservato precedente, eseguire il comando PowerShell seguente:

	Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## Come riservare l'indirizzo IP di un servizio cloud esistente

È possibile riservare l'indirizzo IP di un servizio cloud esistente aggiungendo il parametro *-ServiceName*. Per riservare l'indirizzo IP di un servizio cloud *TestService* nella località *Stati Uniti centrali*, eseguire il comando PowerShell seguente:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## Come associare un indirizzo IP riservato a un nuovo servizio cloud
Lo script seguente crea un nuovo indirizzo IP riservato e quindi lo associa a un nuovo servizio cloud denominato *TestService*.

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Quando si crea un indirizzo IP riservato da usare con un servizio cloud, è comunque necessario fare riferimento alla macchina virtuale mediante *VIP:&lt;numero porta>* per la comunicazione in ingresso. Il fatto di riservare un indirizzo IP non determina la possibilità di connettersi direttamente alla macchina virtuale. L'indirizzo IP riservato viene infatti assegnato al servizio cloud in cui è stata distribuita la macchina virtuale. Per connettersi direttamente a una macchina virtuale tramite indirizzo IP, è necessario configurare un indirizzo IP pubblico a livello di istanza. Si tratta di un tipo di indirizzo IP pubblico (denominato ILPIP) che viene assegnato direttamente alla macchina virtuale. Non può essere riservato. Per altre informazioni, vedere l'articolo relativo all'[indirizzo IP pubblico a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md).

## Come rimuovere un indirizzo IP riservato da una distribuzione in esecuzione
Per rimuovere l'indirizzo IP riservato aggiunto al nuovo servizio creato nello script precedente, eseguire il comando PowerShell seguente:

	Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Rimuovendo un indirizzo IP riservato da una distribuzione in esecuzione, non si rimuove dalla sottoscrizione il fatto che sia riservato. Si libera semplicemente l'indirizzo IP in modo che possa essere usato da un'altra risorsa nella sottoscrizione.

## Come associare un indirizzo IP riservato a una distribuzione in esecuzione
Lo script seguente crea un nuovo servizio cloud denominato *TestService2* con una nuova macchina virtuale denominata *TestVM2* e quindi associa l'indirizzo IP riservato esistente denominato *MyReservedIP* al servizio cloud.

	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService2 -Location "Central US"
	Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## Come associare un indirizzo IP riservato a un servizio cloud usando un file di configurazione del servizio
È possibile anche associare un indirizzo IP riservato a un servizio cloud usando un file di configurazione del servizio (CSCFG). Il file XML di esempio riportato di seguito illustra come configurare un servizio cloud per l'uso di un indirizzo VIP riservato denominato *MyReservedIP*:

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
	  <NetworkConfiguration>
	    <AddressAssignments>
	      <ReservedIPs>
	       <ReservedIP name="MyReservedIP"/>
	      </ReservedIPs>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Passaggi successivi

- Informazioni sul funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.

- Informazioni su [indirizzi IP privati riservati](virtual-networks-reserved-private-ip.md).

- Informazioni su[indirizzo IP pubblico a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md).

<!---HONumber=AcomDC_0824_2016-->