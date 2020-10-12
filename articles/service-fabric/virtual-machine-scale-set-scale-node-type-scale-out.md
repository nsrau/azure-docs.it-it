---
title: Aggiungere un tipo di nodo a un cluster di Azure Service Fabric
description: Informazioni su come ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: efd329c07b4881c6710d4173857b4186965438d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719326"
---
# <a name="scale-a-service-fabric-cluster-out-by-adding-a-virtual-machine-scale-set"></a>Ridimensionare un cluster di Service Fabric aggiungendo un set di scalabilità di macchine virtuali
Questo articolo descrive come ridimensionare un cluster di Azure Service Fabric aggiungendo un nuovo tipo di nodo a un cluster esistente. Un cluster di Service Fabric è un set di computer fisici o macchine virtuali connessi in rete, in cui vengono distribuiti e gestiti i microservizi. Un computer o una macchina virtuale che fa parte di un cluster viene detto nodo. I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Azure viene [configurato come set di scalabilità di macchine virtuali separato](service-fabric-cluster-nodetypes.md). Ogni tipo di nodo può essere gestito separatamente. Dopo aver creato un cluster di Service Fabric, è possibile ridimensionare un cluster orizzontalmente aggiungendo un nuovo tipo di nodo (set di scalabilità di macchine virtuali) a un cluster esistente.  È possibile ridimensionare il cluster in qualsiasi momento, anche quando sono in esecuzione carichi di lavoro nel cluster.  Quando si ridimensiona il cluster, vengono automaticamente ridimensionate anche le applicazioni.

## <a name="add-an-additional-scale-set-to-an-existing-cluster"></a>Aggiungere un altro set di scalabilità a un cluster esistente
L'aggiunta di un nuovo tipo di nodo (supportato da un set di scalabilità di macchine virtuali) a un cluster esistente è simile all' [aggiornamento del tipo di nodo primario](service-fabric-scale-up-primary-node-type.md), con la differenza che non si userà lo stesso NodeTypeRef; Ovviamente non verrà disabilitato alcun set di scalabilità di macchine virtuali usato attivamente e non si perderà la disponibilità del cluster se non si aggiorna il tipo di nodo primario. 

La proprietà NodeTypeRef è dichiarata all'interno delle proprietà dell'estensione di Service Fabric per i set di scalabilità di macchine virtuali:
```json
<snip>
"publisher": "Microsoft.Azure.ServiceFabric",
     "settings": {
     "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
     "nodeTypeRef": "[parameters('vmNodeType2Name')]",
     "dataPath": "D:\\\\SvcFab",
     "durabilityLevel": "Silver",
<snip>
```

Sarà inoltre necessario aggiungere questo nuovo tipo di nodo alla risorsa cluster di Service Fabric:

```json
<snip>
"nodeTypes": [
      {
      "name": "[parameters('vmNodeType2Name')]",
      "applicationPorts": {
                "endPort": "[parameters('nt2applicationEndPort')]",
                "startPort": "[parameters('nt2applicationStartPort')]"
      },
      "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
      "durabilityLevel": "Silver",
       "ephemeralPorts": {
                "endPort": "[parameters('nt2ephemeralEndPort')]",
                "startPort": "[parameters('nt2ephemeralStartPort')]"
      },
      "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
      "isPrimary": false,
      "vmInstanceCount": "[parameters('nt2InstanceCount')]"
},
<snip>
```

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [aumentare le prestazioni del tipo di nodo primario](service-fabric-scale-up-primary-node-type.md)
* Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
* [Ridimensionamento orizzontale di un cluster di Azure](service-fabric-tutorial-scale-cluster.md).
* [Ridimensionamento di un cluster di Azure a livello di codice](service-fabric-cluster-programmatic-scaling.md) tramite l'SDK di calcolo di Azure Fluent.
* [Ridimensionamento orizzontale di un cluster autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

