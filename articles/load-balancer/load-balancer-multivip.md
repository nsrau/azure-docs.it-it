---
title: Indirizzi VIP multipli per un servizio cloud
description: "Panoramica dell'uso di indirizzi VIP multipli e delle modalità di impostazione di più indirizzi VIP in un servizio cloud"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 85f6d26a-3df5-4b8e-96a1-92b2793b5284
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
ms.translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: f40e0501eed8d5f296e7c79d8a35705a695ae6fd
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurare più indirizzi VIP per un servizio cloud

È possibile accedere ai servizi cloud di Azure tramite la rete Internet pubblica usando un indirizzo IP fornito da Azure. Questo indirizzo IP pubblico è detto indirizzo VIP (Virtual IP, IP virtuale) poiché è collegato al bilanciamento del carico di Azure e non alle istanze di macchine virtuali (VM) nel servizio cloud. È possibile accedere a qualsiasi istanza di macchina virtuale in un servizio cloud usando un singolo indirizzo VIP.

Vi sono tuttavia scenari in cui potrebbe essere necessario più di un indirizzo VIP come punto di ingresso allo stesso servizio cloud. Ad esempio, il servizio cloud può ospitare più siti Web che richiedono connettività SSL tramite la porta predefinita 443, poiché ogni sito è ospitato per un cliente o un tenant diverso. In questo scenario è necessario disporre di un indirizzo IP pubblico diverso per ogni sito Web. Il diagramma seguente illustra un tipico caso di hosting Web multi-tenant che richiede più certificati SSL sulla stessa porta pubblica.

![Scenario SSL con più indirizzi VIP](./media/load-balancer-multivip/Figure1.png)

Nell'esempio qui sopra tutti gli indirizzi VIP usano la stessa porta pubblica (443) e il traffico viene reindirizzato a una o più VM con carico bilanciato su una porta privata univoca per l'indirizzo IP interno del servizio cloud che ospita tutti i siti Web.

> [!NOTE]
> Un'altra situazione che richiede l'uso di più indirizzi VIP è costituito dall'hosting di più listener del gruppo di disponibilità SQL AlwaysOn nello stesso set di macchine virtuali.

Gli indirizzi VIP sono dinamici per impostazione predefinita e ciò significa che l'indirizzo IP effettivo assegnato al servizio cloud potrebbe cambiare nel tempo. Per impedire che ciò accada, è possibile riservare un indirizzo VIP per il servizio. Per altre informazioni sugli indirizzi VIP riservati, vedere [IP pubblico riservato](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Per informazioni sui prezzi di indirizzi VIP e IP riservati, vedere [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses/) .

È possibile usare PowerShell per verificare gli indirizzi VIP usati dai servizi cloud, nonché per aggiungere e rimuovere indirizzi VIP, associare un indirizzo VIP a un endpoint e configurare il bilanciamento del carico su un indirizzo VIP specifico.

## <a name="limitations"></a>Limitazioni

A questo punto, la funzionalità con più indirizzi VIP è limitata agli scenari seguenti:

* **Solo IaaS**. È possibile abilitare più indirizzi VIP per servizi cloud che contengono le macchine virtuali. Non è possibile usare più indirizzi VIP negli scenari PaaS con le istanze del ruolo.
* **Solo PowerShell**. È possibile gestire più indirizzi VIP solo con PowerShell.

Queste limitazioni sono temporanee e possono cambiare in qualsiasi momento. Assicurarsi di visualizzare nuovamente questa pagina per verificare le modifiche future.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Come aggiungere un indirizzo VIP a un servizio cloud
Per aggiungere un indirizzo VIP al servizio, eseguire il comando PowerShell seguente:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Questo comando visualizza un risultato simile all'esempio seguente:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Come rimuovere un indirizzo VIP da un servizio cloud
Per rimuovere l'indirizzo VIP aggiunto al servizio nell'esempio precedente, eseguire il comando PowerShell seguente:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> È possibile rimuovere un indirizzo VIP solo se non dispone di alcun endpoint associato.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Come recuperare informazioni sugli indirizzi VIP da un servizio cloud
Per recuperare gli indirizzi VIP associati a un servizio cloud, eseguire lo script di PowerShell seguente:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

Lo script visualizza un risultato simile all'esempio seguente:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

In questo esempio il servizio cloud dispone di 3 indirizzi VIP:

* **Vip1** è l'indirizzo VIP predefinito. Ciò è indicato dall'impostazione di IsDnsProgrammedName su true.
* **Vip2** e **Vip3** non sono in uso in quanto non dispongono di indirizzi IP. Verranno usati solo se si associa un endpoint all'indirizzo VIP.

> [!NOTE]
> Gli indirizzi VIP aggiuntivi verranno addebitati alla sottoscrizione solo dopo essere stati associati a un endpoint. Per altre informazioni sui prezzi, vedere [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Come associare un indirizzo VIP a un endpoint

Per associare un indirizzo VIP in un servizio cloud a un endpoint, eseguire il comando PowerShell seguente:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

Il comando crea un endpoint collegato all'indirizzo VIP denominato *Vip2* sulla porta *80* e lo collega alla VM denominata *myVM1* in un servizio cloud denominato *myService* usando il protocollo *TCP* sulla porta *8080*.

Per verificare la configurazione, eseguire il comando PowerShell seguente:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

L'output è simile al seguente esempio:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Come abilitare il bilanciamento del carico per un indirizzo VIP specifico

È possibile associare un singolo indirizzo VIP a più macchine virtuali per scopi di bilanciamento del carico. Si supponga, ad esempio, di disporre di un servizio cloud denominato *myService* e di due macchine virtuali denominate *myVM1* e *myVM2*. Il servizio cloud dispone di più indirizzi VIP, uno dei quali è denominato *Vip2*. Se si desidera fare in modo che tutto il traffico verso la porta *81* su *Vip2* venga sottoposto a bilanciamento del carico tra *myVM1* e *myVM2* sulla porta *8181*, eseguire lo script di PowerShell seguente:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

È anche possibile aggiornare il bilanciamento del carico per usare un indirizzo VIP diverso. Ad esempio, se si esegue il comando PowerShell seguente, si modifica il set di bilanciamento del carico in modo che venga usato un indirizzo VIP denominato Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Passaggi successivi

[Log Analytics per Azure Load Balancer](load-balancer-monitor-log.md)

[Panoramica del bilanciamento del carico Internet](load-balancer-internet-overview.md)

[Introduzione al bilanciamento del carico Internet](load-balancer-get-started-internet-arm-ps.md)

[Panoramica di Rete virtuale.](../virtual-network/virtual-networks-overview.md)

[API REST di IP riservati](https://msdn.microsoft.com/library/azure/dn722420.aspx)

