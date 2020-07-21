---
title: Dischi dati collegati dei set di scalabilità di macchine virtuali di Azure
description: Informazioni su come usare i dischi dati collegati con i set di scalabilità di macchine virtuali tramite strutture di casi di utilizzo specifici.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 4/25/2017
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 9e4bdf868d3f8ddf3a049509ead30a4b1ba341b7
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527439"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Set di scalabilità di macchine virtuali di Azure e dischi di dati collegati
Per espandere lo spazio di archiviazione disponibile, i [set di scalabilità di macchine virtuali](./index.yml) di Azure supportano le istanze di macchina virtuale con dischi dati collegati. È possibile collegare i dischi dati quando il set di scalabilità viene creato, ma anche a un set di scalabilità esistente.

> [!NOTE]
> Quando si crea un set di scalabilità con dischi dati collegati definiti, per usare i dischi è necessario montarli e formattarli in una macchina virtuale, come per le macchine virtuali di Azure autonome. Un modo pratico per completare questo processo consiste nell'usare un'estensione di script personalizzato che chiama uno script per creare partizioni e formattare tutti i dischi dati in una macchina virtuale. Per alcuni esempi, vedere [Interfaccia della riga di comando di Azure](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks).


## <a name="create-and-manage-disks-in-a-scale-set"></a>Creare e gestire i dischi un set di scalabilità
Per informazioni dettagliate su come creare un set di scalabilità con dischi dati collegati, preparare e formattare oppure aggiungere e rimuovere i dischi dati, vedere una delle esercitazioni seguenti:

- [Interfaccia della riga di comando di Azure](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

La parte rimanente di questo articolo illustra casi d'uso specifici, ad esempio cluster di Service Fabric che richiedono dischi dati o il collegamento di dischi dati con contenuto esistenti a un set di scalabilità.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Creare un cluster di Service Fabric con dischi dati collegati
Ogni [tipo di nodo](../service-fabric/service-fabric-cluster-nodetypes.md) in un cluster [Service Fabric](../service-fabric/index.yml) in esecuzione in Azure è supportato da un set di scalabilità di macchine virtuali. Usando un modello di Azure Resource Manager, è possibile collegare dischi dati ai set di scalabilità che costituiscono il cluster di Service Fabric. Come punto di partenza, è possibile usare un [modello esistente](https://github.com/Azure-Samples/service-fabric-cluster-templates). Nel modello includere una sezione _dataDisks_ nell'elemento _storageProfile_ delle risorse _Microsoft.Compute/virtualMachineScaleSets_ e distribuire il modello. L'esempio seguente consente di collegare un disco dati da 128 GB:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

È possibile partizionare, formattare e montare i dischi dati automaticamente al momento della distribuzione del cluster. Aggiungere un'estensione di script personalizzata in _extensionProfile_ per l'elemento _virtualMachineProfile_ dei set di scalabilità.

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


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Aggiunta di dischi dati pre-popolati a un set di scalabilità esistente
I dischi dati specificati nel modello del set di scalabilità sono sempre vuoti. Tuttavia è possibile collegare un disco dati esistente a una macchina virtuale specifica in un set di scalabilità. Per propagare i dati tra tutte le macchine virtuali nel set di scalabilità, è possibile duplicare il disco dati e collegarlo a ogni macchina virtuale nel set di scalabilità oppure creare un'immagine personalizzata che contenga i dati ed eseguire il provisioning del set di scalabilità da questa immagine personalizzata oppure è possibile usare File di Azure o un'offerta di archiviazione dati simile.


## <a name="additional-notes"></a>Note aggiuntive
Il supporto per Azure Managed Disks e i set di scalabilità con dischi di dati collegati è disponibile nella versione [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) o successiva dell'API Microsoft.Compute.

Il supporto portale di Azure per i dischi dati collegati nei set di scalabilità è limitato. A seconda dei requisiti è possibile usare modelli di Azure, interfaccia della riga di comando, PowerShell, SDK e API REST per gestire i dischi collegati.
