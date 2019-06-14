---
title: Configurare una raccolta di immagini condivisi in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come configurare una raccolta di immagini condivisi in Azure DevTest Labs
services: devtest-lab
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: de857498aeb51c9b3711c90338d983e85b61cb70
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065424"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurare una raccolta immagini condivisa in Azure DevTest Labs
DevTest Labs sono ora supportate le [raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md) funzionalità. Consente agli utenti dei lab di accedere a immagini da una posizione condivisa durante la creazione di risorse del lab. Permette anche di creare la struttura e l'organizzazione per le immagini di macchine virtuali gestite e personalizzate. Supporta la funzionalità di raccolta di immagini condivise:

- Replica globale gestita delle immagini
- Controllo delle versioni e raggruppamento delle immagini per una gestione più semplice
- Disponibilità elevata per le immagini con gli account di archiviazione con ridondanza della zona in aree che supportano le zone di disponibilità. L'archiviazione con ridondanza della zona offre una resilienza migliore in caso di errori specifici di una zona.
- Condivisione tra sottoscrizioni e addirittura tra tenant con il Controllo degli accessi in base al ruolo.

Per altre informazioni, vedere [documentazione di raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md). 
 
Se si dispone di un numero elevato di immagini gestite da mantenere e si desidera renderle disponibili in tutta l'azienda, è possibile usare una raccolta di immagini condivise come repository che semplifica l'aggiornamento e la condivisione delle immagini. In quanto proprietario di lab, puoi associare una raccolta immagini condivisa esistente al tuo lab. Dopo l'associazione della raccolta, gli utenti del lab possono creare computer usando le immagini più recenti. Un vantaggio essenziale di questa funzionalità consiste nel fatto che DevTest Labs può ora approfittare della condivisione di immagini tra lab, sottoscrizioni e aree geografiche. 

## <a name="considerations"></a>Considerazioni
- È possibile collegare solo una raccolta di immagini condivise a un lab alla volta. Se si vuole collegare un'altra raccolta, è necessario un altro di collegamento e scollegamento di quello esistente. 
- DevTest Labs non supporta attualmente le immagini di caricamento nella raccolta tramite il lab. 
- Durante la creazione di una macchina virtuale usando un'immagine della raccolta immagini condivise, DevTest Labs Usa sempre la versione pubblicata più recente di questa immagine.
- Sebbene in DevTest Labs vengano automaticamente un miglior tentativo per garantire una raccolta di immagini condivise replicata nell'area in cui si trova il Lab di immagini, non è sempre possibile. Per evitare che gli utenti con problemi di creazione di macchine virtuali da queste immagini, assicurarsi che le immagini già replicate all'area del lab."

## <a name="use-azure-portal"></a>Usare il portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **tutti i servizi** dal menu di spostamento a sinistra.
1. Selezionare **DevTest Labs** dall'elenco.
1. Nell'elenco di Lab, selezionare i **lab**.
1. Selezionare **configurazione e criteri** nel **impostazioni** sezione nel menu a sinistra.
1. Selezionare **raccolte di immagini condivise** sotto **macchina virtuale si basa** nel menu a sinistra.

    ![Menu di raccolte di immagini condiviso](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Collegare una raccolta di immagini condivise esistenti nel lab facendo clic sui **Attach** pulsante e selezionando la raccolta nell'elenco a discesa.

    ![Collegamento](./media/configure-shared-image-gallery/attach-options.png)
1. Passare alla raccolta associata e configurare la raccolta **Abilita o disabilita** le immagini per la creazione di macchine Virtuali condivise.

    ![Abilitazione o disabilitazione](./media/configure-shared-image-gallery/enable-disable.png)
1. Gli utenti del lab possono quindi creare una macchina virtuale usando le immagini abilitate facendo clic su **+ Aggiungi** e come trovare l'immagine nel **Scegli la base** pagina.

    ![Utenti del lab](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Collegare una raccolta di immagini condivise nel lab
Se si usa un modello Azure Resource Manager per associare una raccolta di immagini condivise al lab, è necessario aggiungerlo sotto la sezione delle risorse del modello di Resource Manager, come illustrato nell'esempio seguente:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

Per un esempio di modello di Resource Manager completo, vedere questi esempi di modelli di Resource Manager nel repository GitHub pubblico: [Configurare una raccolta di immagini condivise durante la creazione di un lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>Creare una VM usando un'immagine dalla raccolta immagini condivise
Se si usa un modello Azure Resource Manager per creare una macchina virtuale usando un'immagine della raccolta immagini condivise, usare l'esempio seguente:

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

Per altre informazioni, vedere questi esempi di modelli di Resource Manager in GitHub pubblico.
[Creare una macchina virtuale usando un'immagine della raccolta immagini condivise](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage).

## <a name="use-api"></a>Usare l'API

- Usare l'API versione 2018-10-15-preview.
- Per collegare la raccolta, inviare la richiesta, come illustrato nel frammento di codice seguente:
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- Per visualizzare tutte le immagini nella raccolta immagini condivise, è possibile elencare tutte le immagini condivise insieme ai relativi ID di risorsa da

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- Per creare una macchina virtuale usando le immagini condivise, è possibile eseguire un'operazione PUT su macchine virtuali e nelle proprietà della macchina virtuale, passare l'ID delle immagini condivise ottenuto dalla chiamata precedente. Per le proprietà. SharedImageId


## <a name="next-steps"></a>Passaggi successivi
Sugli elementi, vedere gli articoli seguenti:

- [Specificare gli elementi obbligatori per l'ambiente lab](devtest-lab-mandatory-artifacts.md)
- [Creare elementi personalizzati](devtest-lab-artifact-author.md)
- [Aggiungere un repository di elementi a un lab](devtest-lab-artifact-author.md)
- [Diagnosticare errori degli elementi](devtest-lab-troubleshoot-artifact-failure.md)
