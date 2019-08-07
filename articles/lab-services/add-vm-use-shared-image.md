---
title: Aggiungere una macchina virtuale usando un'immagine condivisa in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere una macchina virtuale (VM) usando un'immagine dalla raccolta di immagini condivise collegata in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775591"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Aggiungere una macchina virtuale usando un'immagine dalla raccolta di immagini condivise collegata
Azure DevTest Labs consente di aggiungere una raccolta di immagini condivise al Lab e quindi di usare le immagini nella raccolta come basi per le macchine virtuali create nel Lab. Per informazioni su come aggiungere una raccolta di immagini condivise al Lab, vedere [configurare la raccolta di immagini condivise](configure-shared-image-gallery.md). Questo articolo illustra come aggiungere una macchina virtuale al Lab usando un'immagine della raccolta di immagini condivise collegata come base. 

## <a name="azure-portal"></a>Portale di Azure
Questa sezione illustra come usare la portale di Azure per aggiungere una macchina virtuale al Lab in base a un'immagine dalla raccolta di immagini condivise collegata. Questa sezione non fornisce istruzioni dettagliate per la creazione di una macchina virtuale usando il portale di Azure. Per informazioni dettagliate, vedere [creare una VM-portale di Azure](devtest-lab-add-vm.md). Vengono evidenziati solo i passaggi in cui si seleziona un'immagine dalla raccolta di immagini condivise collegata e si seleziona una versione dell'immagine che si vuole usare. 

Quando si aggiunge una macchina virtuale al Lab, è possibile selezionare un'immagine dalla raccolta di immagini condivise collegata come immagine di base: 

![Selezionare un'immagine condivisa per la base](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Quindi, nella scheda **Impostazioni avanzate** della pagina **Crea risorsa lab** è possibile selezionare la versione dell'immagine che si vuole usare come immagine di base:

![Seleziona versione immagine](./media/add-vm-use-shared-image/select-version-shared-image.png)

Dopo la creazione della macchina virtuale, è possibile passare all'uso di una versione diversa dell'immagine. 

## <a name="resource-manager-template"></a>Modello di Resource Manager
Se si usa un modello di Azure Resource Manager per creare una macchina virtuale usando un'immagine della raccolta di immagini condivise, specificare un valore per **sharedImageId** nella sezione **Properties (proprietà** ). Vedere l'esempio seguente: 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

Per un esempio di modello di Gestione risorse completo, vedere l'esempio [creare una macchina virtuale usando un'immagine della raccolta immagini condivisa](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) nel repository GitHub. 

## <a name="rest-api"></a>API REST

1. In primo luogo, è necessario ottenere l'ID dell'immagine nella raccolta immagini condivise. Un modo consiste nell'elencare tutte le immagini nella raccolta di immagini condivise collegata usando il comando GET seguente. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Richiamare il metodo PUT sulle macchine virtuali passando l'ID dell'immagine condivisa ricevuta dalla chiamata precedente a `properties.SharedImageId`.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come aggiungere una raccolta di immagini condivise a un Lab e configurarla, vedere [configurare la raccolta di immagini condivise](configure-shared-image-gallery.md).