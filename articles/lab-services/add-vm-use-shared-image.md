---
title: Aggiungere una macchina virtuale usando un'immagine condivisa in Azure DevTest Labs. Documenti Microsoft
description: Informazioni su come aggiungere una macchina virtuale (VM) usando un'immagine dalla raccolta di immagini condivise allegata in Azure DevTest Labs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775591"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>Aggiungere una macchina virtuale usando un'immagine dalla raccolta di immagini condivise allegataAdd a VM using an image from the attached shared image gallery
Azure DevTest Labs consente di collegare una raccolta di immagini condivise al lab e quindi usare le immagini nella raccolta come base per le macchine virtuali create nel lab. Per informazioni su come allegare una raccolta di immagini condivise al lab, vedere Configurare una raccolta di [immagini condivise.](configure-shared-image-gallery.md) Questo articolo illustra come aggiungere una macchina virtuale al lab usando un'immagine dalla raccolta di immagini condivise allegata come base. 

## <a name="azure-portal"></a>Portale di Azure
In questa sezione viene illustrato come usare il portale di Azure per aggiungere una macchina virtuale al lab in base a un'immagine della raccolta di immagini condivise allegata. Questa sezione non fornisce istruzioni dettagliate per la creazione di una macchina virtuale tramite il portale di Azure.This section doesn't provide detailed step-by-step instructions for creating a VM using the Azure portal. Per questi dettagli, vedere Creare una macchina virtuale - Portale di Azure.For those details, see [Create a VM - Azure portal.](devtest-lab-add-vm.md) Evidenzia solo i passaggi in cui si seleziona un'immagine dalla galleria di immagini condivise allegata e seleziona una versione dell'immagine che si desidera utilizzare. 

Quando si aggiunge una macchina virtuale al lab, è possibile selezionare un'immagine dalla raccolta di immagini condivise allegata come immagine di base:When adding a VM to your lab, you can select an image from the attached shared image gallery as a base image: 

![Selezionare un'immagine condivisa per la base](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

Quindi, nella scheda **Impostazioni avanzate** della pagina Crea **risorsa lab,** è possibile selezionare la versione dell'immagine che si desidera utilizzare come immagine di base:

![Selezionare la versione dell'immagine](./media/add-vm-use-shared-image/select-version-shared-image.png)

È possibile passare all'uso di una versione diversa dell'immagine dopo la creazione della macchina virtuale. 

## <a name="resource-manager-template"></a>Modello di Resource Manager
Se si usa un modello di Azure Resource Manager per creare una macchina virtuale usando un'immagine della raccolta di immagini condivisa, specificare un valore per sharedImageId nella sezione **Properties.If** you're using an Azure Resource Manager template to create a virtual machine using a shared image gallery image, specify a value for the **sharedImageId** in the Properties section. Vedere l'esempio seguente: 

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

Per un esempio di modello completo di Resource Manager, vedere Creare una macchina virtuale usando un esempio di raccolta immagini condivise nel repository GitHub.For a complete Resource Manager template example, see Create a [virtual machine using a shared image gallery sample](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) in our GitHub repository. 

## <a name="rest-api"></a>API REST

1. In primo luogo, è necessario ottenere l'ID dell'immagine nella galleria di immagini condivise. Un modo consiste nell'elencare tutte le immagini nella raccolta di immagini condivise allegate utilizzando il comando GET seguente. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. Richiamare il metodo PUT sulle macchine virtuali passando all'oggetto dell'ID dell'immagine condivisa ricevuta dalla chiamata precedente all'oggetto `properties.SharedImageId`.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come allegare una raccolta di immagini condivise a un lab e configurarla, vedere Configurare una [raccolta di immagini condivise.](configure-shared-image-gallery.md)