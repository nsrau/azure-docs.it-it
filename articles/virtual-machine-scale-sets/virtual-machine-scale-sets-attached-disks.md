---
title: Set di scalabilità di macchine virtuali di Azure e dischi di dati collegati | Documentazione Microsoft
description: Informazioni su come usare dischi di dati collegati con set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Set di scalabilità di macchine virtuali di Azure e dischi di dati collegati
Per espandere lo spazio di archiviazione disponibile, i [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) di Azure supportano le istanze di macchina virtuale con dischi dati collegati. È possibile collegare i dischi dati quando il set di scalabilità viene creato, ma anche a un set di scalabilità esistente.

> [!NOTE]
>  Quando si crea un set di scalabilità con dischi dati collegati definiti, per usare i dischi è necessario montarli e formattarli in una macchina virtuale, come per le macchine virtuali di Azure autonome. Un modo pratico per completare questo processo consiste nell'usare un'estensione di script personalizzato che chiama uno script per creare partizioni e formattare tutti i dischi dati in una macchina virtuale. Per esempi, vedere [Interfaccia della riga di comando di Azure 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Creare e gestire i dischi un set di scalabilità
Per informazioni dettagliate su come creare un set di scalabilità con dischi dati collegati, preparare e formattare oppure aggiungere e rimuovere i dischi dati, vedere una delle esercitazioni seguenti:

- [Interfaccia della riga di comando di Azure 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

La parte rimanente di questo articolo illustra casi d'uso specifici, ad esempio cluster di Service Fabric che richiedono dischi dati o il collegamento di dischi dati con contenuto esistenti a un set di scalabilità.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Creare un cluster di Service Fabric con dischi dati collegati
Ogni [tipo di nodo](../service-fabric/service-fabric-cluster-nodetypes.md) in un cluster di [Service Fabric](/azure/service-fabric) in esecuzione in Azure è supportato da un set di scalabilità di macchine virtuali.  Usando un modello di Azure Resource Manager, è possibile collegare dischi dati ai set di scalabilità che costituiscono il cluster di Service Fabric. Come punto di partenza, è possibile usare un [modello esistente](https://github.com/Azure-Samples/service-fabric-cluster-templates). Nel modello includere una sezione _dataDisks_ nell'elemento _storageProfile_ delle risorse _Microsoft.Compute/virtualMachineScaleSets_ e distribuire il modello. L'esempio seguente consente di collegare un disco dati da 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

È possibile partizionare, formattare e montare i dischi dati automaticamente al momento della distribuzione del cluster.  Aggiungere un'estensione di script personalizzata in _extensionProfile_ per l'elemento _virtualMachineProfile_ dei set di scalabilità.

Per preparare automaticamente i dischi dati in un cluster Windows, aggiungere quanto segue:

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Per preparare automaticamente i dischi dati in un cluster Linux, aggiungere quanto segue:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Aggiunta di dischi dati pre-popolati a un set di scalabilità esistente 
> Quando si aggiungono dischi a un modello di set di scalabilità esistente, da progettazione il disco viene sempre creato vuoto. Questo scenario include anche nuove istanze create dal set di scalabilità. Questo comportamento è dovuto alla presenza di un disco dati vuoto nella definizione del set di scalabilità. Per creare unità dati pre-popolate per un modello di set di scalabilità esistente, è possibile scegliere tra le due opzioni seguenti:

* Copiare i dati dalla macchina virtuale dell'istanza 0 ai dischi dati delle altre macchine virtuali eseguendo uno script personalizzato.
* Creare un'immagine gestita con il disco del sistema operativo e un disco dati, con i dati necessari, e creare un nuovo set di scalabilità con l'immagine. In questo modo ogni nuova macchina virtuale creata avrà un disco dati incluso nella definizione del set di scalabilità. Dato che la definizione fa riferimento a un'immagine con un disco dati che contiene dati personalizzati, ogni macchina virtuale nel set di scalabilità include tali modifiche.

> Per informazioni su come creare un'immagine personalizzata, vedere [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> L'utente deve acquisire la macchina virtuale dell'istanza 0 che contiene i dati necessari e usare tale disco rigido virtuale per la definizione dell'immagine.


## <a name="additional-notes"></a>Note aggiuntive
Il supporto per Azure Managed Disks e i set di scalabilità con dischi di dati collegati è disponibile nella versione [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) o successiva dell'API Microsoft.Compute.

Nell'implementazione iniziale del supporto di dischi collegati per set di scalabilità, non è possibile collegare o scollegare dischi di dati a/da singole macchine virtuali in un set di scalabilità.

Il supporto del portale di Azure per i dischi di dati collegati nei set di scalabilità è inizialmente limitato. A seconda dei requisiti è possibile usare modelli di Azure, interfaccia della riga di comando, PowerShell, SDK e API REST per gestire i dischi collegati.


