---
title: Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni su come i tipi di nodo di Azure Service Fabric sono correlati ai set di scalabilità di macchine virtuali e su come connettersi in remoto a un'istanza del set di scalabilità o a un nodo del cluster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: chackdan
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: a5f8735df2b230de2b0ddcdcccff09430bada9e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684684"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali
I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets) sono una risorsa di calcolo di Azure. I set di scalabilità possono essere usati per distribuire e gestire una raccolta di macchine virtuali come un set. Ogni tipo di nodo definito in un cluster di Azure Service Fabric configura un set di scalabilità separato.  Il runtime di Service Fabric installato in ogni macchina virtuale nel set di scalabilità spostando l'estensione macchina virtuale di sezione. È possibile aumentare o ridurre in modo indipendente ogni nodo, cambiare lo SKU del sistema operativo in esecuzione in ogni nodo del cluster, avere diversi set di porte aperte e usare metriche per la capacità diverse.

La figura seguente mostra un cluster con due tipi di nodo denominati FrontEnd e BackEnd. Ogni tipo di nodo ha cinque nodi.

![Un cluster con due tipi di nodo][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Eseguire il mapping delle istanze dei set di scalabilità di macchine virtuali ai nodi
Come illustrato nella figura precedente, le istanze dei set di scalabilità iniziano con l'istanza 0 per poi aumentare di 1. I nomi dei nodi corrispondono alla numerazione. Ad esempio, il nodo BackEnd_0 è l'istanza 0 del set di scalabilità BackEnd. Questo particolare set di scalabilità ha cinque istanze, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando si aumenta un set di scalabilità, viene creata una nuova istanza. Il nome della nuova istanza del set di scalabilità sarà in genere il nome del set di scalabilità + il successivo numero di istanza. Nell'esempio sarà BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Eseguire il mapping dei servizi di bilanciamento del carico dei set di scalabilità a tipi di nodo e set di scalabilità
Se è stato distribuito il cluster dal portale Azure o è stato usato il modello di Azure Resource Manager di esempio, si otterrà un elenco di tutte le risorse in un gruppo di risorse. Verranno visualizzati i bilanciamenti del carico per ogni set di scalabilità o tipo di nodo. Il nome del servizio di bilanciamento del carico Usa il formato seguente: **-LB&lt;nome del tipo di nodo&gt;** . Ad esempio, LB-sfcluster4doc-0, come in questa figura:

![Risorse][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Estensione macchina virtuale di Service Fabric
Estensione macchina virtuale di Service Fabric consente di eseguire l'avvio di Service Fabric per le macchine virtuali di Azure e configurare la sicurezza del nodo.

Di seguito è riportato un frammento di estensione della macchina virtuale di Service Fabric:

```json
"extensions": [
  {
    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
    "properties": {
      "type": "ServiceFabricLinuxNode",
      "autoUpgradeMinorVersion": true,
      "protectedSettings": {
        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
       },
       "publisher": "Microsoft.Azure.ServiceFabric",
       "settings": {
         "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
         "nodeTypeRef": "[variables('vmNodeType0Name')]",
         "durabilityLevel": "Silver",
         "enableParallelJobs": true,
         "nicPrefixOverride": "[variables('subnet0Prefix')]",
         "certificate": {
           "commonNames": [
             "[parameters('certificateCommonName')]"
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
         }
       },
       "typeHandlerVersion": "1.1"
     }
   },
```

Di seguito sono le descrizioni delle proprietà:

| **Nome** | **Valori consentiti** | ** --- ** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| name | string | --- | nome univoco per l'estensione |
| type | "ServiceFabricLinuxNode" or "ServiceFabricWindowsNode | --- | Identifica OS Service Fabric è il bootstrap per |
| autoUpgradeMinorVersion | true o false | --- | Abilita l'aggiornamento automatico del Runtime di Service Fabric le versioni secondarie |
| publisher | Microsoft.Azure.ServiceFabric | --- | nome del server di pubblicazione dell'estensione Service Fabric |
| clusterEndpont | string | --- | URI:Port all'endpoint di gestione |
| nodeTypeRef | string | --- | nome del tipo di nodo |
| durabilityLevel | bronzo, argento, oro, platinum | --- | periodo di tempo consentito per mettere in pausa non modificabile dell'infrastruttura di Azure |
| enableParallelJobs | true o false | --- | Abilitare ParallelJobs di calcolo come rimuovere una macchina virtuale e riavviare la macchina virtuale nella stesso set di scalabilità in parallelo |
| nicPrefixOverride | string | --- | Prefisso della subnet, ad esempio "10.0.0.0/24" |
| commonNames | string[] | --- | Nomi comuni dei certificati installati cluster |
| x509StoreName | string | --- | Nome del certificato del cluster installati in cui si trova Store |
| typeHandlerVersion | 1.1 | --- | Versione dell'estensione. 1.0 versione classica dell'estensione sono consigliati per l'aggiornamento a 1.1 |

## <a name="next-steps"></a>Passaggi successivi
* Vedere la [panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e un confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md).
* Informazioni sulla [sicurezza del cluster](service-fabric-cluster-security.md).
* [Connettersi in remoto](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) a un'istanza specifica del set di scalabilità
* [Aggiornare i valori dell'intervallo di porte RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nelle macchine virtuali del cluster dopo la distribuzione
* [Cambiare il nome utente e la password dell'amministratore](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) per le macchine virtuali del cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
