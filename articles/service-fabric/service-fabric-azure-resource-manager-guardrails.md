---
title: Service Fabric di distribuzione Azure Resource Manager Guardrails
description: Questo articolo fornisce una panoramica degli errori comuni effettuati durante la distribuzione di un cluster di Service Fabric tramite Azure Resource Manager e come evitarli.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: aa1499c57ead28bfcee90a2f224ef9c3bb1d7f58
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247825"
---
# <a name="service-fabric-guardrails"></a>Service Fabric Guardrails 
Quando si distribuisce un cluster di Service Fabric, vengono messi a posto Guardrails, che non riusciranno a eseguire una distribuzione Azure Resource Manager in caso di configurazione del cluster non valida. Nelle sezioni seguenti viene fornita una panoramica dei problemi comuni di configurazione del cluster e dei passaggi necessari per attenuare tali problemi. 

## <a name="durability-mismatch"></a>Durabilità non corrispondente
### <a name="overview"></a>Panoramica
Il valore di durabilità per un tipo di nodo Service Fabric viene definito in due sezioni diverse di un modello di Azure Resource Manager. La sezione estensione del set di scalabilità di macchine virtuali della risorsa del set di scalabilità di macchine virtuali e il tipo di nodo della risorsa cluster Service Fabric. È necessario che il valore di durabilità in queste sezioni corrisponda, in caso contrario la distribuzione delle risorse avrà esito negativo.

La sezione seguente contiene un esempio di una mancata corrispondenza di durabilità tra l'impostazione di durabilità dell'estensione del set di scalabilità di macchine virtuali e il Service Fabric impostazione di durabilità del tipo di nodo:  

**Impostazione della durabilità del set di scalabilità di macchine virtuali**
```json 
{
  "extensions": [
    {
      "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
      "properties": {
        "type": "ServiceFabricNode",
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
          "durabilityLevel": "Silver",
        }
      }
    }
  ]
}
```

**Impostazione della durabilità del tipo di nodo Service Fabric** 
```json
{
  "nodeTypes": [
    {
      "name": "[variables('vmNodeType0Name')]",
      "durabilityLevel": "Bronze",
      "isPrimary": true,
      "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    }
  ]
}
```

### <a name="error-messages"></a>Messaggi di errore
* La mancata corrispondenza della durabilità del set di scalabilità di macchine virtuali non corrisponde al livello di durabilità del tipo di nodo Service Fabric corrente
* La durabilità del set di scalabilità di macchine virtuali non corrisponde al livello di durabilità del tipo di nodo Service Fabric di destinazione
* La durabilità del set di scalabilità di macchine virtuali corrisponde al livello di durabilità del Service Fabric corrente o al livello di durabilità del tipo di nodo Service Fabric di destinazione 

### <a name="mitigation"></a>Strategia di riduzione del rischio
Per correggere una mancata corrispondenza di durabilità, indicata da uno dei messaggi di errore seguenti:
1. Aggiornare il livello di durabilità nella sezione estensione del set di scalabilità di macchine virtuali o tipo di nodo Service Fabric del modello di Azure Resource Manager per assicurarsi che i valori corrispondano.
2. Ridistribuire il modello di Azure Resource Manager con i valori aggiornati.


## <a name="seed-node-deletion"></a>Eliminazione del nodo di inizializzazione 
### <a name="overview"></a>Panoramica
Un cluster Service Fabric dispone di una proprietà del [livello di affidabilità](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster) che consente di determinare il numero di repliche dei servizi di sistema in esecuzione sul tipo di nodo primario del cluster. Il numero di repliche necessarie determinerà il numero minimo di nodi che devono essere mantenuti nel tipo di nodo primario del cluster. Se il numero di nodi nel tipo di nodo primario scende al di sotto del valore minimo necessario per il livello di affidabilità, il cluster diventerà instabile.  

### <a name="error-messages"></a>Messaggi di errore 
L'operazione di rimozione del nodo di inizializzazione è stata rilevata e verrà rifiutata. 
* Questa operazione comporterebbe {0} la permanenza solo dei nodi di inizializzazione potenziali nel cluster, mentre {1} sono necessari come minimo.
* La rimozione dei {0} nodi di inizializzazione da {1} comporterebbe il rallentamento del cluster a causa della perdita del quorum del nodo di inizializzazione. Il numero massimo di nodi di inizializzazione che è possibile rimuovere alla volta è {2} .
 
### <a name="mitigation"></a>Strategia di riduzione del rischio 
Verificare che il tipo di nodo primario disponga di macchine virtuali sufficienti per l'affidabilità specificata nel cluster. Non sarà possibile rimuovere una macchina virtuale se il set di scalabilità di macchine virtuali sarà inferiore al numero minimo di nodi per il livello di affidabilità specificato.
* Se il livello di affidabilità è specificato correttamente, assicurarsi di disporre di un numero sufficiente di nodi nel tipo di nodo primario secondo le esigenze per il livello di affidabilità. 
* Se il livello di affidabilità non è corretto, avviare una modifica nella risorsa Service Fabric per abbassare il livello di affidabilità prima di avviare qualsiasi operazione del set di scalabilità di macchine virtuali e attenderne il completamento.
* Se il livello di affidabilità è bronzo, attenersi alla [procedura seguente](./service-fabric-cluster-scale-in-out.md#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) per ridimensionare il cluster in modo normale.

## <a name="next-steps"></a>Passaggi successivi
* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risoluzione dei problemi Service Fabric: [guide alla risoluzione dei problemi](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
