---
title: Tipi di nodo e set di scalabilità di macchine virtuali
description: Informazioni su come i tipi di nodo di Azure Service Fabric sono correlati ai set di scalabilità delle macchine virtuali e su come connettersi in remoto a un'istanza del set di scalabilità o a un nodo del cluster.
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 37d4c27d3033545c523cefc2f317073af531f095
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199717"
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Tipi di nodo di Azure Service Fabric e set di scalabilità di macchine virtuali

I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets) sono una risorsa di calcolo di Azure. I set di scalabilità possono essere usati per distribuire e gestire una raccolta di macchine virtuali come un set. Ogni tipo di nodo definito in un cluster di Azure Service Fabric configura esattamente un set di scalabilità: più tipi di nodo non possono essere supportati dallo stesso set di scalabilità e un tipo di nodo non deve (nella maggior parte dei casi) essere supportato da più set di scalabilità. Un'eccezione è la rariva situazione di [ridimensionamento verticale](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations) di un tipo `nodeTypeRef` di nodo, quando si dispone temporaneamente di due set di scalabilità con lo stesso valore durante la migrazione delle repliche dal set di scalabilità originale al set di scalabilità aggiornato.

Il runtime di Service Fabric viene installato in ogni macchina virtuale nel set di scalabilità dall'estensione della macchina virtuale *Microsoft.Azure.ServiceFabric.The* Service Fabric runtime is installed on each virtual machine in the scale set by the Microsoft.Azure.ServiceFabric Virtual Machine extension. È possibile aumentare o ridurre in modo indipendente ogni nodo, cambiare lo SKU del sistema operativo in esecuzione in ogni nodo del cluster, avere diversi set di porte aperte e usare metriche per la capacità diverse.

Nella figura seguente viene illustrato un cluster con due tipi di nodo, denominati *FrontEnd* e *BackEnd*. Ogni tipo di nodo ha cinque nodi.

![Un cluster con due tipi di nodo][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Eseguire il mapping delle istanze dei set di scalabilità di macchine virtuali ai nodi

Come illustrato nella figura precedente, le istanze dei set di scalabilità iniziano con l'istanza 0 per poi aumentare di 1. I nomi dei nodi corrispondono alla numerazione. Ad esempio, il nodo BackEnd_0 è l'istanza 0 del set di scalabilità BackEnd. Questo particolare set di scalabilità ha cinque istanze, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Quando si aumenta un set di scalabilità, viene creata una nuova istanza. Il nome della nuova istanza del set di scalabilità sarà in genere il nome del set di scalabilità + il successivo numero di istanza. Nell'esempio sarà BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Eseguire il mapping dei servizi di bilanciamento del carico dei set di scalabilità a tipi di nodo e set di scalabilità

Se è stato distribuito il cluster dal portale Azure o è stato usato il modello di Azure Resource Manager di esempio, si otterrà un elenco di tutte le risorse in un gruppo di risorse. Verranno visualizzati i bilanciamenti del carico per ogni set di scalabilità o tipo di nodo. Il nome del bilanciamento del carico usa il formato seguente: **LB-&lt;nome del tipo di nodo&gt;**. Ad esempio, LB-sfcluster4doc-0, come in questa figura:

![Risorse][Resources]

## <a name="service-fabric-virtual-machine-extension"></a>Service Fabric Virtual Machine Extension

L'estensione della macchina virtuale dell'infrastruttura del servizio viene usata per eseguire il bootstrap di Service Fabric in Macchine virtuali di Azure e configurare la sicurezza dei nodi.

Di seguito è riportato un frammento dell'estensione della macchina virtuale Service Fabric:The following is a snippet of Service Fabric Virtual Machine extension:

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

| **Nome** | **Valori consentiti** | **Linee guida o breve descrizione** |
| --- | --- | --- | --- |
| name | string | Nome univoco per l'estensione |
| type | "ServiceFabricLinuxNode" o "ServiceFabricWindowsNode" | Identifica il bootstrap di OS Service Fabric |
| autoUpgradeMinorVersion | true o false | Abilitare l'aggiornamento automatico delle versioni secondarie di Runtime SFEnable Auto Upgrade of SF Runtime Minor Versions |
| publisher | Microsoft.Azure.ServiceFabric | Nome dell'autore dell'estensione Service Fabric |
| clusterEndon | string | URI:port all'endpoint di gestione |
| nodeTypeRef | string | Nome di nodeType |
| durabilityLevel | bronzo, argento, oro, platino | Tempo consentito per mettere in pausa l'infrastruttura di Azure non modificabileTime allowed to pause immutable Azure Infrastructure |
| enableParallelJobs | true o false | Abilitare Compute ParallelJobs come rimuovere la macchina virtuale e riavviare la macchina virtuale nello stesso set di scalabilità in paralleloEnable Compute ParallelJobs like remove VM and reboot VM in the same scale set in parallel |
| nicPrefixOverride (comando nicPrefixOverride) | string | Prefisso subnet come "10.0.0.0/24" |
| commonNames | string[] | Nomi comuni dei certificati del cluster installatiCommon Names of installed cluster certificates |
| x509StoreName (nome negozio) | string | Nome dell'archivio in cui si trova il certificato del cluster installato |
| typeHandlerVersion | 1.1 | Versione dell'estensione. 1.0 versione classica dell'estensione si consiglia di eseguire l'aggiornamento a 1.1 |
| Percorso dati | string | Percorso dell'unità utilizzata per salvare lo stato per i servizi di sistema di Service Fabric e i dati dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

* Vedere la [panoramica della funzionalità "Distribuzione in qualsiasi ambiente" e un confronto con i cluster gestiti da Azure](service-fabric-deploy-anywhere.md).
* Informazioni sulla [protezione del cluster](service-fabric-cluster-security.md).
* [Connettersi in remoto](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) a un'istanza specifica del set di scalabilità
* [Aggiornare i valori dell'intervallo di porte RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) nelle macchine virtuali del cluster dopo la distribuzione
* [Cambiare il nome utente e la password dell'amministratore](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) per le macchine virtuali del cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
