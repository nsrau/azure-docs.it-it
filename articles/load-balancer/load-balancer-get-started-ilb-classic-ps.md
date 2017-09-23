---
title: Creare un servizio di bilanciamento del carico interno di Azure - Distribuzione classica con PowerShell | Documentazione Microsoft
description: Informazioni su come creare un servizio di bilanciamento del carico interno usando PowerShell nel modello di distribuzione classica
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 3be93168-3787-45a5-a194-9124fe386493
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: f701fb3564c62cf8088cc4362a10c5e2c2301ae6
ms.contentlocale: it-it
ms.lasthandoff: 02/16/2017

---

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Introduzione alla creazione di un servizio di bilanciamento del carico interno (classico) tramite PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Interfaccia della riga di comando di Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Servizi cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md).  Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Informazioni su come [eseguire questa procedura con il modello di Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Creare un set con servizio di bilanciamento del carico interno per le macchine virtuali

Per creare un set con servizio di bilanciamento del carico e i server che invieranno il traffico a esso, è necessario eseguire le operazioni seguenti:

1. Creare un'istanza della funzionalità di bilanciamento del carico interno che sarà l'endpoint del traffico in ingresso da configurare con carico bilanciato tra i server di un set con carico bilanciato.
2. Aggiungere gli endpoint corrispondenti alle macchine virtuali che riceveranno il traffico in ingresso.
3. Configurare i server che invieranno il traffico con carico bilanciato all'indirizzo IP virtuale (indirizzo VIP) dell'istanza del bilanciamento del carico interno.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Passaggio 1: Creare un'istanza del bilanciamento del carico interno

Per un servizio cloud esistente o un servizio cloud distribuito in una rete virtuale dell'area, è possibile creare un'istanza del bilanciamento del carico interno con i comandi di Windows PowerShell seguenti:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Si noti che questo uso del cmdlet di Windows PowerShell [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) richiede il set di parametri DefaultProbe. Per altre informazioni sui set di parametri aggiuntivi, vedere [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Passaggio 2: Aggiungere endpoint all'istanza del bilanciamento del carico interno

Di seguito è fornito un esempio:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Passaggio 3: Configurare i server per l'invio del traffico al nuovo endpoint del bilanciamento del carico interno

Per poter usare il nuovo indirizzo IP (indirizzo VIP) dell'istanza del bilanciamento del carico interno, è necessario configurare i server il cui traffico verrà configurato con carico bilanciato. Si tratta dell'indirizzo su cui è in ascolto l'istanza del bilanciamento del carico interno. Nella maggior parte dei casi, è sufficiente aggiungere o modificare un record DNS per l'indirizzo VIP dell'istanza del bilanciamento del carico interno.

Se l'indirizzo IP è stato specificato durante la creazione dell'istanza del bilanciamento del carico interno, si ha già l'indirizzo VIP. In caso contrario, è possibile visualizzare l'indirizzo VIP eseguendo i comandi seguenti:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Per usare questi comandi, inserire i valori e rimuovere < and >. Di seguito è fornito un esempio:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Dalla visualizzazione del comando Get-AzureInternalLoadBalancer prendere nota dell'indirizzo IP e apportare le modifiche necessarie ai server o ai record DNS per assicurarsi che il traffico venga inviato all'indirizzo VIP.

> [!NOTE]
> La piattaforma Microsoft Azure usa un indirizzo IPv4 statico e instradabile pubblicamente per un'ampia gamma di scenari di amministrazione. L'indirizzo IP è 168.63.129.16. Questo indirizzo IP non deve essere bloccato da alcun firewall, perché potrebbe causare un comportamento imprevisto.
> Per quanto riguarda il bilanciamento del carico interno di Azure, questo indirizzo IP viene usato da probe di monitoraggio del servizio di bilanciamento del carico per determinare lo stato di integrità delle macchine virtuali in un set con carico bilanciato. Se si usa un gruppo di sicurezza di rete per limitare il traffico alle macchine virtuali di Azure in un set con carico bilanciato internamente o lo si applica a una subnet di rete virtuale, assicurarsi di aggiungere una regola di sicurezza di rete per consentire il traffico dall'indirizzo 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Esempio di bilanciamento del carico interno

Per una descrizione del processo end-to-end di creazione di un set con carico bilanciato per due configurazioni di esempio, vedere le sezioni seguenti.

### <a name="an-internet-facing-multi-tier-application"></a>Applicazione multilivello con connessione Internet

Si vuole offrire un servizio di database con carico bilanciato per un gruppo di server Web con connessione Internet. Entrambi i set di server sono ospitati in un unico servizio cloud di Azure. Il traffico dei server Web verso la porta TCP 1433 deve essere distribuito tra due macchine virtuali nel livello database. La figura 1 illustra la configurazione.

![Set con carico bilanciato interno per il livello database](./media/load-balancer-internal-getstarted/IC736321.png)

La configurazione è costituita dai seguenti elementi:

* Il servizio cloud esistente che ospita le macchine virtuali è denominato mytestcloud.
* I due server di database esistenti sono denominati DB1, DB2.
* I server Web nel livello Web si connettono ai server di database nel livello database usando l’indirizzo IP privato. Un'altra opzione consiste nell'utilizzare il proprio DNS per la rete virtuale e registrare manualmente un record A per il set di bilanciamento del carico interno.

I comandi seguenti configurano una nuova istanza del bilanciamento del carico interno denominata **ILBset** e aggiungono endpoint alle macchine virtuali corrispondenti ai due server di database:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Rimuovere una configurazione del bilanciamento del carico interno

Per rimuovere una macchina virtuale come endpoint da un'istanza del servizio di bilanciamento del carico interno, usare i comandi seguenti:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Per usare questi comandi, inserire i valori rimuovendo < and >.

Di seguito è fornito un esempio:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Per rimuovere un'istanza del servizio di bilanciamento del carico interno da un servizio cloud, usare i comandi seguenti:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Per usare questi comandi, inserire il valore e rimuovere < and >.

Di seguito è fornito un esempio:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Altre informazioni sui cmdlet per servizio di bilanciamento del carico interno

Per ottenere altre informazioni sui cmdlet per il bilanciamento del carico interno, eseguire i comandi seguenti da un prompt di Windows PowerShell:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Passaggi successivi

[Configurare una modalità di distribuzione del servizio di bilanciamento del carico utilizzando l’affinità dell’IP di origine](load-balancer-distribution-mode.md)

[Configurare le impostazioni del timeout di inattività TCP per il bilanciamento del carico](load-balancer-tcp-idle-timeout.md)


