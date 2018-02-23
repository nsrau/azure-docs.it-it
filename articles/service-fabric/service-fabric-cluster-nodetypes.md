---
title: "Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali | Microsoft Docs"
description: "Informazioni su come i tipi di nodo di Azure Service Fabric sono correlati ai set di scalabilità di macchine virtuali e su come connettersi in remoto a un'istanza del set di scalabilità o a un nodo del cluster."
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
ms.date: 2/5/2018
ms.author: chackdan
ms.openlocfilehash: 720bb83c9d8540549852ce78ee1709f8c8717348
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure. I set di scalabilità possono essere usati per distribuire e gestire una raccolta di macchine virtuali come un set. Configurare un set di scalabilità di macchine virtuali separato per ogni tipo di nodo definito in un cluster di Azure Service Fabric. È possibile aumentare o ridurre in modo indipendente ogni nodo, avere diversi set di porte aperte e usare metriche per la capacità diverse.

La figura seguente mostra un cluster con due tipi di nodo denominati FrontEnd e BackEnd. Ogni tipo di nodo ha cinque nodi.

![Un cluster con due tipi di nodo][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Eseguire il mapping delle istanze dei set di scalabilità di macchine virtuali ai nodi
Come illustrato nella figura precedente, le istanze dei set di scalabilità iniziano con l'istanza 0 per poi aumentare di 1. I nomi dei nodi corrispondono alla numerazione. Ad esempio, il nodo BackEnd_0 è l'istanza 0 del set di scalabilità BackEnd. Questo particolare set di scalabilità ha cinque istanze, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando si aumenta un set di scalabilità, viene creata una nuova istanza. Il nome della nuova istanza del set di scalabilità sarà in genere il nome del set di scalabilità + il successivo numero di istanza. Nell'esempio sarà BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Eseguire il mapping dei servizi di bilanciamento del carico dei set di scalabilità a tipi di nodo e set di scalabilità
Se è stato distribuito il cluster dal portale Azure o è stato usato il modello di Azure Resource Manager di esempio, si otterrà un elenco di tutte le risorse in un gruppo di risorse. Verranno visualizzati i bilanciamenti del carico per ogni set di scalabilità o tipo di nodo. Il nome del bilanciamento del carico usa il formato seguente: **LB-&lt;nome del tipo di nodo&gt;**. Ad esempio, LB-sfcluster4doc-0, come in questa figura:

![Risorse][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connessione remota a un'istanza di set di scalabilità di macchine virtuali o a un nodo del cluster
Configurare un set di scalabilità separato per ogni tipo di nodo definito in un cluster. I tipi di nodo possono quindi essere aumentati o ridotti in modo indipendente. È anche possibile usare SKU di macchine virtuali diverse. Diversamente dalle macchine virtuali a istanza singola, le istanze dei set di scalabilità non ottengono un proprio indirizzo IP virtuale. Può quindi essere difficile cercare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica.

Per trovare un indirizzo IP e una porta da usare per connettersi in remoto a un'istanza specifica, completare questi passaggi.

**Passaggio 1**: trovare l'indirizzo IP virtuale per il tipo di nodo ottenendo le regole NAT in ingresso per Remote Desktop Protocol (RDP).

Innanzitutto, ottenere i valori delle regole NAT in ingresso definiti nell'ambito della definizione di risorse per `Microsoft.Network/loadBalancers`.

Nella pagina del bilanciamento del carico nel portale di Azure selezionare **Impostazioni** > **Regole NAT in ingresso**. Si ottengono così l'indirizzo IP e la porta che è possibile usare per connettersi in remoto alla prima istanza del set di scalabilità. 

![Bilanciamento del carico][LBBlade]

Nella figura seguente l'indirizzo IP e la porta sono **104.42.106.156** e **3389**.

![Regole NAT][NATRules]

**Passaggio 2**: trovare la porta che è possibile usare per connettersi in remoto all'istanza del set di scalabilità o nodo specifico.

Mapping dei set di scalabilità ai nodi. Usare le informazioni del set di scalabilità per determinare la porta esatta da usare.

Le porte vengono allocate in un ordine crescente che corrisponde all'istanza del set di scalabilità. Nell'esempio precedente del tipo di nodo FrontEnd, le porte per ognuna delle cinque istanze del nodo sono elencate nella tabella seguente. Applicare lo stesso mapping all'istanza del set di scalabilità.

| **Istanza del set di scalabilità della macchina virtuale** | **Porta** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**Passaggio 3**: connettersi in remoto all'istanza specifica del set di scalabilità.

Nella figura seguente viene usata Connessione Desktop remoto per connettersi al set di istanza di scalabilità FrontEnd_1:

![Connessione Desktop remoto][RDP]

## <a name="change-the-rdp-port-range-values"></a>Modificare i valori dell'intervallo di porte RDP

### <a name="before-cluster-deployment"></a>Prima della distribuzione cluster
Quando si configura il cluster usando un modello di Resource Manager, è possibile specificare l'intervallo in `inboundNatPools`.

Passare alla definizione della risorsa per `Microsoft.Network/loadBalancers`. Individuare la descrizione per `inboundNatPools`.  Sostituire i valori `frontendPortRangeStart` e `frontendPortRangeEnd`.

![Valori inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Dopo la distribuzione cluster
È più complesso modificare i valori dell'intervallo di porte RDP dopo che il cluster è stato distribuito. Per assicurarsi che le macchine virtuali non vengano riciclate, usare Azure PowerShell per impostare i nuovi valori. 

> [!NOTE]
> Verificare che nel computer sia installato Azure PowerShell 1.0 o versione successiva. Se non si dispone di Azure PowerShell versione 1.0 o successiva, si consiglia di seguire la procedura descritta in [Come installare e configurare Azure PowerShell](/powershell/azure/overview)

1. Accedere all'account Azure. Se il seguente comando PowerShell non viene eseguito, verificare che Azure PowerShell sia installato correttamente.

    ```
    Login-AzureRmAccount
    ```

2. Eseguire il codice seguente per ottenere i dettagli sul servizio di bilanciamento del carico e visualizzare i valori della descrizione per `inboundNatPools`:

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Impostare `frontendPortRangeEnd` e `frontendPortRangeStart` sui valori desiderati.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Modificare il nome utente e la password RDP per i nodi

Per modificare la password per tutti i nodi di un determinato tipo di nodo, completare questi passaggi. Queste modifiche verranno applicate a tutti i nodi correnti e futuri nel set di scalabilità.

1. Aprire PowerShell come amministratore. 
2. Eseguire i comandi seguenti per accedere e selezionare la sottoscrizione per la sessione. Modificare il parametro `SUBSCRIPTIONID` per l'ID sottoscrizione. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Eseguire lo script seguente. Usare i valori `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` e `PASSWORD`. I valori `USERNAME` e `PASSWORD` saranno le nuove credenziali da usare per le sessioni RDP future. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Passaggi successivi
* Vedere la [panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e un confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md).
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* Informazioni su [Service Fabric SDK e introduzione](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
