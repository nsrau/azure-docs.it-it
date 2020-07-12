---
title: Tipi di nodo e set di scalabilità di macchine virtuali
description: Informazioni su come i tipi di nodo Service Fabric di Azure sono correlati ai set di scalabilità di macchine virtuali e su come connettersi in remoto a un'istanza del set di scalabilità o a un nodo
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 4efa8626e80cbd64cd6216faa1869d7210f32cf2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261100"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali

I [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/index.yml) sono una risorsa di calcolo di Azure. I set di scalabilità possono essere usati per distribuire e gestire una raccolta di macchine virtuali come un set. Ogni tipo di nodo definito in un cluster di Azure Service Fabric configura esattamente un set di scalabilità: non è possibile eseguire il backup di più tipi di nodo dallo stesso set di scalabilità e un tipo di nodo (nella maggior parte dei casi) deve essere supportato da più set di scalabilità. Un'eccezione a questo problema si verifica in rari casi di [ridimensionamento verticale](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) di un tipo di nodo, quando si dispone temporaneamente di due set di scalabilità con lo stesso `nodeTypeRef` valore durante la migrazione delle repliche dal set di scalabilità originale a quello aggiornato.

Il runtime di Service Fabric viene installato in ogni macchina virtuale nel set di scalabilità dall'estensione della macchina virtuale *Microsoft. Azure. ServiceFabric* . È possibile aumentare o ridurre in modo indipendente ogni nodo, cambiare lo SKU del sistema operativo in esecuzione in ogni nodo del cluster, avere diversi set di porte aperte e usare metriche per la capacità diverse.

La figura seguente illustra un cluster con due tipi di nodo *, denominato front* - *end e back-end*. Ogni tipo di nodo ha cinque nodi.

![Un cluster con due tipi di nodo][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Eseguire il mapping delle istanze dei set di scalabilità di macchine virtuali ai nodi

Come illustrato nella figura precedente, le istanze dei set di scalabilità iniziano con l'istanza 0 per poi aumentare di 1. I nomi dei nodi corrispondono alla numerazione. Ad esempio, il nodo BackEnd_0 è l'istanza 0 del set di scalabilità BackEnd. Questo particolare set di scalabilità ha cinque istanze, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando si aumenta la scalabilità orizzontale di un set di scalabilità, viene creata una nuova istanza. Il nome della nuova istanza del set di scalabilità sarà in genere il nome del set di scalabilità + il successivo numero di istanza. Nell'esempio sarà BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Eseguire il mapping dei servizi di bilanciamento del carico dei set di scalabilità a tipi di nodo e set di scalabilità

Se è stato distribuito il cluster dal portale Azure o è stato usato il modello di Azure Resource Manager di esempio, si otterrà un elenco di tutte le risorse in un gruppo di risorse. Verranno visualizzati i bilanciamenti del carico per ogni set di scalabilità o tipo di nodo. Il nome del bilanciamento del carico usa il formato seguente: **LB-&lt;nome del tipo di nodo&gt;**. Ad esempio, LB-sfcluster4doc-0, come in questa figura:

![Risorse][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Estensione della macchina virtuale Service Fabric

Service Fabric estensione della macchina virtuale viene usata per avviare Service Fabric macchine virtuali di Azure e configurare la sicurezza del nodo.

Di seguito è riportato un frammento di Service Fabric estensione della macchina virtuale:

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
         "dataPath": "D:\\\\SvcFab",
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

Di seguito sono riportate le descrizioni delle proprietà:

| **Nome** | **Valori consentiti** | **Indicazioni o breve descrizione** |
| --- | --- | --- | --- |
| name | string | Nome univoco per l'estensione |
| tipo | "ServiceFabricLinuxNode" o "ServiceFabricWindowsNode" | Identifica Service Fabric del sistema operativo per il bootstrap |
| autoUpgradeMinorVersion | true o false | Abilita l'aggiornamento automatico delle versioni secondarie di runtime SF |
| publisher | Microsoft. Azure. ServiceFabric | Nome del server di pubblicazione dell'estensione Service Fabric |
| clusterEndpont | string | URI: porta per l'endpoint di gestione |
| nodeTypeRef | string | Nome di nodeType |
| Durabilità | bronzo, argento, oro, platino | Tempo consentito per sospendere un'infrastruttura di Azure non modificabile |
| enableParallelJobs | true o false | Abilitare ParallelJobs di calcolo, ad esempio rimuovere una macchina virtuale e riavviare la macchina virtuale nello stesso set di scalabilità in parallelo |
| nicPrefixOverride | string | Prefisso della subnet, ad esempio "10.0.0.0/24" |
| commonNames | string[] | Nomi comuni dei certificati del cluster installati |
| x509StoreName | string | Nome dell'archivio in cui si trova il certificato del cluster installato |
| typeHandlerVersion | 1.1 | Versione dell'estensione. 1,0 versione classica dell'estensione consigliata per l'aggiornamento a 1,1 |
| Percorso dati | string | Percorso dell'unità utilizzata per salvare lo stato per Service Fabric i servizi di sistema e i dati dell'applicazione.

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
