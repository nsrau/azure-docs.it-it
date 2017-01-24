---
title: "Tipi di nodo di Service Fabric e set di scalabilità di macchine virtuali | Documentazione Microsoft"
description: "Descrive come i tipi di nodo di Service Fabric siano correlati ai set di scalabilità di macchine virtuali e come connettersi in remoto a un&quot;istanza del set di scalabilità di macchine virtuali o a un nodo del cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: 6670076d14f1f3f3991dd004d488f69fb1c44cb6


---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Relazione tra i tipi di nodo di Service Fabric e i set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Service Fabric è configurato come un set di scalabilità di macchine virtuali separato. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse.

Lo screenshot seguente illustra un cluster con due tipi di nodo: front-end e back-end.  Ogni tipo di nodo ha cinque nodi.

![Screenshot di un cluster con due tipi di nodo][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mapping dei set di scalabilità di macchine virtuali ai nodi
Come si può notare dalla figura precedente, le istanze dei set di scalabilità di macchine virtuali iniziano con l'istanza 0 per poi aumentare. La numerazione viene rispecchiata dai nomi. Ad esempio, BackEnd_0 è l'istanza 0 del set di scalabilità di macchine virtuali BackEnd. Questo particolare set di scalabilità di macchine virtuali ha cinque istanze, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando si aumenta un set di scalabilità di macchine virtuali, viene creata una nuova istanza. La nuova istanza del set di scalabilità di macchine virtuali sarà in genere il nome del set di scalabilità di macchine virtuali + il successivo numero di istanza. Nell'esempio sarà BackEnd_5.

## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mapping dei servizi di bilanciamento del carico dei set di scalabilità di macchine virtuali a ogni tipo di nodo/set di scalabilità di macchine virtuali
Se il cluster è stato distribuito dal portale o è stato usato il modello di Resource Manager di esempio fornito, quando si ottiene un elenco di tutte le risorse di un gruppo di risorse, verranno visualizzati i servizi di bilanciamento del carico per ogni set di scalabilità di macchine virtuali o tipo di nodo.

Il nome sarà simile a: **LB-&lt;nome tipo di nodo&gt;**. ad esempio, LB-sfcluster4doc-0, come in questo screenshot:

![Risorse][Resources]

## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Connessione remota a un'istanza di set di scalabilità di macchine virtuali o a un nodo del cluster
Ogni tipo di nodo definito in un cluster viene configurato come set di scalabilità di macchine virtuali separato.  I tipi di nodo possono quindi essere aumentati o ridotti in modo indipendente ed essere costituiti da SKU di VM diverse. Diversamente dalle VM a istanza singola, le istanze dei set di scalabilità di macchine virtuali non ottengono un proprio indirizzo IP virtuale. Può quindi essere difficile cercare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica.

Ecco i passaggi che è possibile seguire per trovarli.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Passaggio 1: Trovare l'indirizzo IP virtuale per il tipo di nodo e quindi le regole NAT in ingresso per RDP
A questo scopo, è necessario ottenere i valori delle regole NAT in ingresso definiti nell'ambito della definizione di risorse per **Microsoft.Network/loadBalancers**.

Nel portale passare al pannello del servizio di bilanciamento del carico e quindi fare clic su **Impostazioni**.

![Pannello servizio di bilanciamento del carico][LBBlade]

In **Impostazioni** fare clic su **Regole NAT in ingresso**. Si ottengono così l'indirizzo IP e la porta che è possibile usare per connettersi in remoto alla prima istanza del set di scalabilità di macchine virtuali. Nello screenshot seguente sono **104.42.106.156** e **3389**

![Regole NAT][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Passaggio 2: Trovare la porta che è possibile usare per connettersi in remoto all'istanza del set di scalabilità di macchine virtuali/nodo specifico
Nella prima parte di questo documento si è parlato di come venga eseguito il mapping delle istanze dei set di scalabilità di macchine virtuali ai nodi. Questa operazione verrà usata per trovare la porta esatta.

Le porte vengono allocate in ordine crescente nell'istanza del set di scalabilità di macchine virtuali, quindi nell'esempio, per il tipo di nodo FrontEnd, le porte per ognuna delle cinque istanze sono le seguenti. È ora necessario eseguire lo stesso mapping per l'istanza del set di scalabilità di macchine virtuali.

| **Istanza del set di scalabilità di macchine virtuali** | **Porta** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Passaggio 3: Connettersi in remoto all'istanza specifica del set di scalabilità di macchine virtuali
Nello screenshot seguente viene usata Connessione Desktop remoto per connettersi a FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Come modificare i valori dell'intervallo di porte RDP
### <a name="before-cluster-deployment"></a>Prima della distribuzione cluster
Quando si configura il cluster usando un modello di Resource Manager, è possibile specificare l'intervallo in **inboundNatPools**.

Passare alla definizione di risorse per **Microsoft.Network/loadBalancers**, in cui si troverà la descrizione per **inboundNatPools**.  Sostituire i valori *frontendPortRangeStart* e *frontendPortRangeEnd*.

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Dopo la distribuzione cluster
È una procedura un po' più complessa ed è possibile che le VM vengano riciclate. Sarà necessario impostare nuovi valori usando Azure PowerShell. Verificare che nel computer sia installato Azure PowerShell 1.0 o versione successiva. Se non è ancora installato, si consiglia vivamente di seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs)

Accedere all'account Azure. Se per qualche motivo questo comando PowerShell genera un errore, verificare che Azure PowerShell sia installato correttamente.

```
Login-AzureRmAccount
```

Eseguire quanto segue per ottenere i dettagli sul servizio di bilanciamento del carico e visualizzare i valori della descrizione per **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

Impostare *frontendPortRangeEnd* e *frontendPortRangeStart* sui valori desiderati.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Passaggi successivi
* [Panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md)
* [Sicurezza del cluster](service-fabric-cluster-security.md)
* [ Service Fabric SDK e introduzione](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png



<!--HONumber=Dec16_HO1-->


