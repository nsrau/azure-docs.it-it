---
title: Guardrails Azure Resource Manager distribuzione Service Fabric | Microsoft Docs
description: Questo articolo fornisce una panoramica degli errori comuni effettuati durante la distribuzione di un cluster di Service Fabric tramite Azure Resource Manager e come evitarli.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: gamonroy
editor: ''
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/30/2019
ms.author: pepogors
ms.openlocfilehash: 3ea6f850685a695644cfc3073fc939a58901658c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73828630"
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

### <a name="error-messages"></a>messaggi di errore
* La mancata corrispondenza della durabilità del set di scalabilità di macchine virtuali non corrisponde al livello di durabilità del tipo di nodo Service Fabric corrente
* La durabilità del set di scalabilità di macchine virtuali non corrisponde al livello di durabilità del tipo di nodo Service Fabric di destinazione
* La durabilità del set di scalabilità di macchine virtuali corrisponde al livello di durabilità del Service Fabric corrente o al livello di durabilità del tipo di nodo Service Fabric di destinazione 


### <a name="mitigation"></a>Mitigazione
Per correggere una mancata corrispondenza di durabilità, indicata da uno dei messaggi di errore seguenti:
1. Aggiornare il livello di durabilità nella sezione estensione del set di scalabilità di macchine virtuali o tipo di nodo Service Fabric del modello di Azure Resource Manager per assicurarsi che i valori corrispondano.
2. Ridistribuire il modello di Azure Resource Manager con i valori aggiornati.

## <a name="next-steps"></a>Passaggi successivi
* Creare un cluster nelle VM o nei computer che eseguono Windows Server: [Creazione di cluster di Service Fabric per Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Creare un cluster nelle VM o nei computer che eseguono Linux: [Creare un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* Risoluzione dei problemi Service Fabric: [guide alla risoluzione dei problemi](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides)
