---
title: Guardrail di distribuzione di Azure Resource Manager di Service Fabric
description: Questo articolo offre una panoramica degli errori comuni commessi durante la distribuzione di un cluster di Service Fabric tramite Azure Resource Manager e su come evitarli.
services: service-fabric
documentationcenter: .net
author: peterpogorski
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: pepogors
ms.openlocfilehash: a61b0cf30ca46eb77837eb09d6a9a0b6f30e89a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368573"
---
# <a name="service-fabric-guardrails"></a>Guardrail Service Fabric 
Quando si distribuisce un cluster di Service Fabric, vengono posizionati i guardrail, che non riuscirà una distribuzione di Azure Resource Manager in caso di una configurazione cluster non valida. Nelle sezioni seguenti viene fornita una panoramica dei problemi comuni di configurazione del cluster e dei passaggi necessari per ridurre tali problemi. 

## <a name="durability-mismatch"></a>Disallineamento di durata
### <a name="overview"></a>Panoramica
Il valore di durabilità per un tipo di nodo di Service Fabric è definito in due sezioni diverse di un modello di Azure Resource Manager.The durability value for a Service Fabric node type is defined in two different sections of an Azure Resource Manager template. La sezione relativa all'estensione Del set di scalabilità di macchine virtuali della risorsa Set di scalabilità di macchine virtuali e la sezione Tipo di nodo della risorsa cluster Service Fabric. È necessario che il valore di durabilità in queste sezioni corrisponda, altrimenti la distribuzione delle risorse avrà esito negativo.

La sezione seguente contiene un esempio di mancata corrispondenza di durabilità tra l'impostazione di durabilità dell'estensione del set di scalabilità di macchine virtuali e l'impostazione di durabilità del tipo di nodo dell'infrastruttura del servizio:The following section contains an example of a durability mismatch between the Virtual Machine Scale Set extension durability setting and the Service Fabric Node Type durability setting:  

**Impostazione di durata del set di scalabilità della scalabilità della macchina virtualeVirtual Machine Scale Set durability setting**
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

**Impostazione di durabilità del tipo di nodo di Service FabricService Fabric node durability setting** 
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

### <a name="error-messages"></a>messaggi di errore
* Mancata durata del set di scalabilità della macchina virtuale non corrisponde al livello di durabilità del tipo di nodo dell'infrastruttura del servizio correnteVirtual Machine Scale Set durability mismatch does not match the current Service Fabric Node Type durability level
* La durabilità del set di scalabilità di macchine virtuali non corrisponde al livello di durabilità del tipo di nodo dell'infrastruttura del servizio di destinazioneVirtual Machine Scale Set durability does not match the target Service Fabric Node Type durability level
* La durabilità del set di scalabilità della macchina virtuale corrisponde al livello di durabilità dell'infrastruttura del servizio corrente o al livello di durabilità del tipo di nodo dell'infrastruttura di servizio di destinazioneVirtual Machine Scale Set durability does match the current Service Fabric durability level or the target Service Fabric Node Type durability level 

### <a name="mitigation"></a>Strategia di riduzione del rischio
Per correggere una mancata corrispondenza di durabilità, indicata da uno dei messaggi di errore precedenti:
1. Aggiornare il livello di durabilità nella sezione Estensione set di scalabilità macchina virtuale o Tipo di nodo dell'infrastruttura del servizio del modello di Azure Resource Manager per assicurarsi che i valori corrispondano.
2. Ridistribuire il modello di Azure Resource Manager con i valori aggiornati.


## <a name="seed-node-deletion"></a>Eliminazione del nodo di seedina 
### <a name="overview"></a>Panoramica
Un cluster di Service Fabric dispone di una proprietà del [livello di affidabilità](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-reliability-characteristics-of-the-cluster) utilizzata per determinare il numero di repliche dei servizi di sistema in esecuzione nel tipo di nodo primario del cluster. Il numero di repliche richieste determinerà il numero minimo di nodi che devono essere gestiti nel tipo di nodo primario del cluster. Se il numero di nodi nel tipo di nodo primario scende al di sotto del minimo richiesto per il livello di affidabilità, il cluster diventerà instabile.  

### <a name="error-messages"></a>messaggi di errore 
L'operazione di rimozione del seedina del nodo è stata rilevata e verrà rifiutata. 
* Questa operazione comporterebbe solo {0} potenziali nodi di {1} seeding per rimanere nel cluster, mentre sono necessari come minimo.
* La {0} rimozione di nodi di {1} seme da cui si riduce il cluster comporterebbe la perdita del quorum del nodo di seeding. Il numero massimo di nodi di serie {2}che possono essere rimossi contemporaneamente è .
 
### <a name="mitigation"></a>Strategia di riduzione del rischio 
Verificare che il tipo di nodo primario disponga di macchine virtuali sufficienti per l'affidabilità specificata nel cluster. Non sarà possibile rimuovere una macchina virtuale se il set di scalabilità di macchine virtuali viene portato al di sotto del numero minimo di nodi per il livello di affidabilità specificato.
* Se il livello di affidabilità è specificato correttamente, assicurarsi di disporre di un numero sufficiente di nodi nel tipo di nodo primario in base alle esigenze per il livello di affidabilità. 
* Se il livello di affidabilità non è corretto, avviare una modifica nella risorsa di Service Fabric per ridurre il livello di affidabilità prima di avviare qualsiasi operazione del set di scalabilità di macchine virtuali e attenderne il completamento.
* Se il livello di affidabilità è Bronzo, attenersi alla [seguente procedura](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-scale-up-down#manually-remove-vms-from-a-node-typevirtual-machine-scale-set) per ridurre il cluster normalmente.

## <a name="next-steps"></a>Passaggi successivi
* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risolvere i problemi relativi a Service Fabric: [guide alla risoluzione dei problemiTroubleshoot](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) Service Fabric: Troubleshooting guides
