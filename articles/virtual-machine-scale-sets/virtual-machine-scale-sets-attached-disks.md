---
title: "Set di scalabilità di macchine virtuali di Azure e dischi di dati collegati | Documentazione Microsoft"
description: "Informazioni su come usare dischi di dati collegati con set di scalabilità di macchine virtuali"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: guybo
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 22c7e589efa9a9f401549ec9b95c58c4eaf07b94
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Set di scalabilità di macchine virtuali di Azure e dischi di dati collegati
I [set di scalabilità di macchine virtuali](/azure/virtual-machine-scale-sets/) di Azure supportano ora macchine virtuali con dischi di dati collegati. I dischi di dati possono essere definiti nel profilo di archiviazione per i set di scalabilità creati con Azure Managed Disks. In precedenza, le uniche opzioni di archivi collegati direttamente disponibili con le macchine virtuali nei set di scalabilità erano le unità del sistema operativo e le unità temporanee.

> [!NOTE]
>  Quando si crea un set di scalabilità con dischi di dati collegati definiti dall'utente, per usare i dischi è comunque necessario montarli e formattarli all'interno di una macchina virtuale, come per le macchine virtuali di Azure autonome. Un modo pratico per eseguire questa operazione consiste nell'usare un'estensione di script personalizzata che chiama uno script standard per creare partizioni e formattare tutti i dischi di dati in una macchina virtuale.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Creare un set di scalabilità con dischi di dati collegati
Un modo semplice per creare un set di scalabilità con dischi collegati consiste nell'usare il comando _vmss create_ dell'[interfaccia della riga di comando di Azure](https://github.com/Azure/azure-cli). L'esempio seguente crea un gruppo di risorse di Azure e un set di scalabilità di 10 macchine virtuali Ubuntu, ognuna con 2 dischi di dati collegati rispettivamente da 50 GB e 100 GB.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
Si noti che il comando _vmss create_ imposta alcuni valori di configurazione predefiniti se non vengono specificati dall'utente. Per visualizzare le opzioni che possono essere sostituite provare:
```bash
az vmss create --help
```
Un altro modo per creare un set di scalabilità con dischi di dati collegati è definire un set di scalabilità in un modello di Azure Resource Manager, includere una sezione _dataDisks_ in _storageProfile_ e distribuire il modello. L'esempio precedente con dischi da 50 GB e 100 GB sarà definito nel modo seguente nel modello:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
Un esempio completo e pronto per la distribuzione di un modello di set di scalabilità con un disco collegato è disponibile qui: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Aggiungere un disco dati a un set di scalabilità esistente
> [!NOTE]
>  È possibile collegare i dischi dati solo a un set di scalabilità creato con [Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md).

È possibile aggiungere un disco dati a un set di scalabilità di macchine virtuali usando il comando _az vmss disk attach_ dell'interfaccia della riga di comando di Azure. Specificare un lun che non sia già in uso. L'esempio di interfaccia della riga di comando seguente aggiunge un'unità da 50 GB a lun 3:
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

L'esempio di PowerShell seguente aggiunge un'unità da 50 GB a lun 3:
```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Le diverse dimensioni delle macchine virtuali hanno limiti differenti per quanto riguarda il numero di unità collegate supportate. Verificare le [dimensioni delle macchine virtuali](../virtual-machines/windows/sizes.md) prima di aggiungere un nuovo disco.

È anche possibile aggiungere un disco aggiungendo una nuova voce alla proprietà _dataDisks_ dell'elemento _storageProfile_ della definizione di un set di scalabilità e applicando la modifica. Per testare questa configurazione, trovare una definizione di set di scalabilità esistente in [Esplora risorse di Azure](https://resources.azure.com/). Selezionare _Modifica_ e aggiungere un nuovo disco all'elenco dei dischi di dati. Ad esempio usando l'esempio precedente:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

Selezionare quindi _PUT_ per applicare le modifiche al set di scalabilità. Questo esempio funziona se si usano macchine virtuali con dimensioni che supportano più di due dischi di dati collegati.

> [!NOTE]
> Quando si apporta una modifica a una definizione di set di scalabilità, ad esempio l'aggiunta o la rimozione di un disco di dati, la modifica si applica a tutte le nuove macchine virtuali, mentre si applica a quelle esistenti solo se la proprietà _upgradePolicy_ è impostata su "Automatic". Se è impostata su "Manual", è necessario applicare manualmente il nuovo modello alle macchine virtuali esistenti. È possibile eseguire questa operazione nel portale, usando il comando _AzureRmVmssInstance_ di PowerShell o il comando _az vmss update-instances_ dell'interfaccia della riga di comando.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Aggiunta di dischi dati pre-popolati a un set di scalabilità esistente 
> Quando si aggiungono dischi a un modello di set di scalabilità esistente, da progettazione il disco viene sempre creato vuoto. Questo scenario include anche nuove istanze create dal set di scalabilità. Questo comportamento è dovuto alla presenza di un disco dati vuoto nella definizione del set di scalabilità. Per creare unità dati pre-popolate per un modello di set di scalabilità esistente, è possibile scegliere tra le due opzioni seguenti:

* Copiare i dati dalla macchina virtuale dell'istanza 0 ai dischi dati delle altre macchine virtuali eseguendo uno script personalizzato.
* Creare un'immagine gestita con il disco del sistema operativo e un disco dati, con i dati necessari, e creare un nuovo set di scalabilità con l'immagine. In questo modo ogni nuova macchina virtuale creata avrà un disco dati incluso nella definizione del set di scalabilità. Dato che la definizione farà riferimento a un'immagine con un disco dati che contiene dati personalizzati, ogni macchina virtuale nel set di scalabilità includerà automaticamente tali modifiche.

> Per informazioni su come creare un'immagine personalizzata, vedere [Creare un'immagine gestita di una macchina virtuale generalizzata in Azure](/azure/virtual-machines/windows/capture-image-resource/). 

> L'utente deve acquisire la macchina virtuale dell'istanza 0 che contiene i dati necessari e usare tale disco rigido virtuale per la definizione dell'immagine.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Rimuovere un disco dati da un set di scalabilità
È possibile rimuovere un disco dati da un set di scalabilità di macchine virtuali usando il comando _az vmss disk detach_ dell'interfaccia della riga di comando di Azure. Ad esempio il comando seguente rimuove il disco definito in lun 2:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
Allo stesso modo è anche possibile rimuovere un disco da un set di scalabilità rimuovendo una voce dalla proprietà _dataDisks_ dell'elemento _storageProfile_ e applicando la modifica. 

## <a name="additional-notes"></a>Note aggiuntive
Il supporto per Azure Managed Disks e i set di scalabilità con dischi di dati collegati è disponibile nella versione [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) o successiva dell'API Microsoft.Compute.

Nell'implementazione iniziale del supporto di dischi collegati per set di scalabilità, non è possibile collegare o scollegare dischi di dati a/da singole macchine virtuali in un set di scalabilità.

Il supporto del portale di Azure per i dischi di dati collegati nei set di scalabilità è inizialmente limitato. A seconda dei requisiti è possibile usare modelli di Azure, interfaccia della riga di comando, PowerShell, SDK e API REST per gestire i dischi collegati.



