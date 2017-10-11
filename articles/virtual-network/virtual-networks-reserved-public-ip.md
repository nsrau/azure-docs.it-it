---
title: Gestire gli indirizzi IP riservati di Azure (versione classica) - PowerShell | Microsoft Docs
description: Comprendere gli indirizzi IP riservati (classica) e come gestirli tramite PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 5e9c83cebec96c6bc8afd53b0c637d7af899746f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2017
---
# <a name="reserved-ip-addresses-classic"></a>Indirizzi IP riservati (classica)

> [!div class="op_single_selector"]
> * [Portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfaccia della riga di comando di Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modello](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Classic)](virtual-networks-reserved-public-ip.md) (PowerShell (classico))

Gli indirizzi IP in Azure rientrano in due categorie: indirizzi dinamici e indirizzi riservati. Gli indirizzi IP pubblici gestiti da Azure sono dinamici per impostazione predefinita. Questo significa che l'indirizzo IP usato per un determinato servizio cloud (VIP) oppure per accedere direttamente a una macchina virtuale o a un'istanza del ruolo (ILPIP) può essere arrestato o interrotto (deallocato).

Per impedire che gli indirizzi IP cambino, è possibile riservarli. Gli indirizzi IP riservati possono essere usati solo come indirizzi VIP, pertanto assicurano che l'indirizzo IP per il servizio cloud resti invariato anche se le risorse vengono arrestate o interrotte (deallocate). È inoltre possibile convertire in indirizzo IP riservato gli indirizzi IP dinamici esistenti usati come indirizzi VIP.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come riservare un indirizzo IP pubblico statico usando [il modello di distribuzione di Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Per altre informazioni sugli indirizzi IP in Azure, leggere l'articolo sugli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Quando è necessario un indirizzo IP riservato?
* **Si vuole essere certi che l'indirizzo IP sia riservato nella sottoscrizione**. Se si intende riservare un indirizzo IP in modo che non venga rilasciato dalla sottoscrizione in alcuna circostanza, è consigliabile usare un indirizzo IP pubblico riservato.  
* **Si vuole che l'indirizzo IP resti associato al servizio cloud anche se lo stato delle macchine virtuali è arrestato o deallocato**. Se si vuole che il servizio cloud sia accessibile mediante l'uso di un indirizzo IP che non cambia, anche se le macchine virtuali nel servizio cloud vengono arrestate o interrotte (deallocate).
* **Si vuole essere certi che il traffico in uscita da Azure usi un indirizzo IP prevedibile**. È possibile che il firewall locale sia configurato per consentire solo il traffico proveniente da indirizzi IP specifici. Riservando un indirizzo IP, si conoscerà l'indirizzo IP di origine e non sarà necessario aggiornare le regole del firewall a seguito del cambiamento di indirizzo IP.

## <a name="faq"></a>domande frequenti
1. È possibile usare un indirizzo IP riservato per tutti i servizi di Azure? <br>
    No. Gli indirizzi IP riservati possono essere usati solo per le macchine virtuali e per i ruoli delle istanze del servizio cloud esposti mediante un indirizzo VIP.
2. Di quanti indirizzi IP riservati è possibile disporre? <br>
    Per informazioni dettagliate, vedere il [Azure limita](../azure-subscription-service-limits.md#networking-limits) articolo.
3. È previsto un addebito per gli indirizzi IP riservati? <br>
    In alcuni casi sì. Per informazioni dettagliate sui prezzi, consultare la pagina [Prezzi per gli indirizzi IP riservati](http://go.microsoft.com/fwlink/?LinkID=398482).
4. Come è possibile riservare un indirizzo IP? <br>
    È possibile usare PowerShell, la [API REST di gestione di Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), o [portale di Azure](https://portal.azure.com) per riservare un indirizzo IP in un'area di Azure. Un indirizzo IP riservato è associato alla sottoscrizione.
5. È possibile usare gli indirizzi IP riservati con reti virtuali basate su gruppi di affinità? <br>
    No. Gli indirizzi IP riservati sono supportati solo nelle reti virtuali di area. Gli indirizzi IP riservati non sono supportati per le reti virtuali associate a gruppi di affinità. Per altre informazioni sull'associazione di una rete virtuale a una regione o a un gruppo di affinità, vedere l'articolo sulle [reti virtuali dell'area e sui gruppi di affinità](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Gestire gli indirizzi VIP riservati

Verificare di aver installato e configurato Azure PowerShell eseguendo i passaggi descritti nell'articolo [Installare e configurare PowerShell](/powershell/azure/overview). 

Prima di poter usare gli indirizzi IP riservati, è necessario aggiungerli alla propria sottoscrizione. Per creare un indirizzo IP riservato dal pool di indirizzi IP pubblici disponibili nella posizione *Stati Uniti centrali*, eseguire il comando seguente:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Si noti tuttavia che non è possibile specificare quale indirizzo IP riservare. Per visualizzare gli indirizzi IP riservati nella propria sottoscrizione, eseguire il comando PowerShell seguente e osservare i valori per *ReservedIPName* e *Address*:

```powershell
Get-AzureReservedIP
```

Output previsto:

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

>[!NOTE]
>Quando si crea un indirizzo IP riservato con PowerShell, non è possibile specificare un gruppo di risorse in cui creare l'indirizzo IP riservato. Azure lo posiziona automaticamente in un gruppo di risorse denominato *Default-Networking*. Se si crea l'indirizzo IP riservato usando il [portale Azure](http://portal.azure.com), è possibile specificare qualsiasi gruppo di risorse desiderato. Tuttavia, se si crea l'indirizzo IP riservato in un gruppo di risorse diverso da *Default-Networking*, quando si fa riferimento all'indirizzo IP riservato con comandi quali `Get-AzureReservedIP` e `Remove-AzureReservedIP`, è necessario fare riferimento al nome *Group resource-group-name reserved-ip-name*.  Ad esempio, se si crea un indirizzo IP riservato denominato *myReservedIP* in un gruppo di risorse denominato *myResourceGroup*, è necessario fare riferimento al nome di un IP riservato come ad esempio *Group myResourceGroup myReservedIP*.   

Dopo essere stato riservato, un indirizzo IP rimane associato alla sottoscrizione finché non viene eliminato. Per eliminare l'indirizzo IP riservato, eseguire il comando PowerShell seguente:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Riservare l'indirizzo IP di un servizio cloud esistente
È possibile riservare l'indirizzo IP di un servizio cloud esistente aggiungendo il parametro `-ServiceName`. Per riservare l'indirizzo IP di un servizio cloud *TestService* nella posizione *Stati Uniti centrali*, eseguire il comando PowerShell seguente:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associare un indirizzo IP riservato a un nuovo servizio cloud
Lo script seguente crea un nuovo indirizzo IP riservato e quindi lo associa a un nuovo servizio cloud denominato *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Quando si crea un indirizzo IP riservato da usare con un servizio cloud, è comunque necessario fare riferimento alla macchina virtuale tramite *VIP:&lt;numero porta>* per la comunicazione in ingresso. Il fatto di riservare un indirizzo IP non determina la possibilità di connettersi direttamente alla macchina virtuale. L'indirizzo IP riservato viene infatti assegnato al servizio cloud in cui è stata distribuita la macchina virtuale. Per connettersi direttamente a una macchina virtuale tramite indirizzo IP, è necessario configurare un indirizzo IP pubblico a livello di istanza. Si tratta di un tipo di indirizzo IP pubblico (denominato ILPIP) che viene assegnato direttamente alla macchina virtuale. Non può essere riservato. Per altre informazioni, vedere l'articolo [IP pubblico a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md).
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Rimuovere un indirizzo IP riservato da una distribuzione in esecuzione
Per rimuovere un indirizzo IP riservato aggiunto a un nuovo servizio cloud, eseguire il comando PowerShell seguente:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Rimuovendo un indirizzo IP riservato da una distribuzione in esecuzione, non si rimuove dalla sottoscrizione il fatto che sia riservato. Si libera semplicemente l'indirizzo IP in modo che possa essere usato da un'altra risorsa nella sottoscrizione.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associare un indirizzo IP riservato a una distribuzione in esecuzione
I seguenti comandi creano un servizio cloud denominato *TestService2* con una nuova macchina virtuale denominata *TestVM2*. L'indirizzo IP riservato esistente denominato *MyReservedIP* viene quindi associato al servizio cloud.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associare un indirizzo IP riservato a un servizio cloud usando un file cscfg
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

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sul funzionamento degli [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.
* Informazioni su [indirizzi IP privati riservati](virtual-networks-reserved-private-ip.md).
* Informazioni su [indirizzo IP pubblico a livello di istanza (ILPIP)](virtual-networks-instance-level-public-ip.md).

