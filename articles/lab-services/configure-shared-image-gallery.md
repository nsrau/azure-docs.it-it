---
title: Configurare una raccolta di immagini condivise nei laboratori di Azure DevTest Documenti Microsoft
description: Informazioni su come configurare una raccolta di immagini condivise in Azure DevTest Labs
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
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589318"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurare una raccolta immagini condivisa in Azure DevTest Labs
DevTest Labs supporta ora la funzionalità [Raccolta immagini condivise.](../virtual-machines/windows/shared-image-galleries.md) Consente agli utenti del lab di accedere alle immagini da una posizione condivisa durante la creazione delle risorse lab. Consente inoltre di creare strutture e organizzazioni intorno alle immagini delle macchine virtuali gestite su misura. La funzione Galleria di immagini condivise supporta:

- Replica globale gestita delle immagini
- Controllo delle versioni e raggruppamento delle immagini per semplificare la gestione
- Rendere le immagini a disponibilità elevata con gli account di archiviazione con ridondanza di zona nelle aree che supportano le zone di disponibilità. L'RS offre una migliore resilienza contro i guasti delle aree.
- Condivisione tra sottoscrizioni e anche tra tenant, utilizzando il controllo degli accessi in base al ruolo.

Per ulteriori informazioni, consultate la documentazione di [Shared Image Gallery.](../virtual-machines/windows/shared-image-galleries.md) 
 
Se si dispone di un numero elevato di immagini gestite da mantenere e si desidera renderle disponibili in tutta l'azienda, è possibile usare una raccolta di immagini condivise come repository che semplifica l'aggiornamento e la condivisione delle immagini. Il proprietario di un lab può allegare una raccolta di immagini condivise esistente al lab. Una volta collegata questa raccolta, gli utenti del lab possono creare computer da queste immagini più recenti. Uno dei vantaggi principali di questa funzionalità è che DevTest Labs può ora sfruttare la condivisione di immagini tra laboratori, tra sottoscrizioni e tra aree. 

> [!NOTE]
> Per informazioni sui costi associati al servizio Shared Image Gallery, vedere [Fatturazione per raccolta immagini condivise](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Considerazioni
- È possibile allegare una sola raccolta di immagini condivisa a un lab alla volta. Se si desidera allegare un'altra galleria, è necessario scollegare quello esistente e allegarne un altro. 
- DevTest Labs supporta attualmente solo immagini generalizzate della raccolta di immagini condivise.
- DevTest Labs attualmente non supporta il caricamento di immagini nella raccolta tramite il lab. 
- Durante la creazione di una macchina virtuale usando un'immagine della raccolta di immagini condivisa, DevTest Labs usa sempre l'ultima versione pubblicata di questa immagine. Tuttavia, se un'immagine ha più versioni, l'utente può scegliere di creare una macchina da una versione precedente accedendo alla scheda Impostazioni avanzate durante la creazione della macchina virtuale.  
- Anche se DevTest Labs esegue automaticamente un tentativo migliore per garantire che la raccolta di immagini condivise riproduca le immagini nell'area in cui è presente il lab, non è sempre possibile. Per evitare che gli utenti abbiano problemi nella creazione di macchine virtuali da queste immagini, assicurarsi che le immagini siano già replicate nell'area del lab."

## <a name="use-azure-portal"></a>Usare il portale di Azure
1. Accedere al [portale](https://portal.azure.com)di Azure .
1. Selezionare **Tutti i servizi** nel menu di navigazione a sinistra.
1. Selezionare **DevTest Labs** dall'elenco.
1. Nell'elenco dei laboratori selezionare il **lab**.
1. Selezionare **Configurazione e criteri** nella sezione **Impostazioni** del menu a sinistra.
1. Selezionare **Raccolte immagini condivise** in Basi macchina **virtuale** nel menu a sinistra.

    ![Menu Raccolte immagini condivise](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Allegare una raccolta di immagini condivise esistente al lab facendo clic sul pulsante **Allega** e selezionando la raccolta nell'elenco a discesa.

    ![Collegamento](./media/configure-shared-image-gallery/attach-options.png)
1. Passare alla raccolta associata e configurare la raccolta per **abilitare o disabilitare** le immagini condivise per la creazione di macchine virtuali. Selezionare una raccolta immagini dall'elenco per configurarla. 

    Per impostazione predefinita, **consenti l'utilizzo** di tutte le immagini come basi di macchine virtuali è impostato su **Sì**. Ciò significa che tutte le immagini disponibili nella raccolta di immagini condivise allegate saranno disponibili per un utente del lab durante la creazione di una nuova macchina virtuale lab. Se è necessario limitare l'accesso a determinate immagini, impostare Consenti l'uso di **tutte le immagini come basi** di macchine virtuali su **No**e selezionare le immagini che si desidera consentire durante la creazione di macchine virtuali, quindi selezionare il pulsante **Salva.**

    ![Abilitare o disabilitare](./media/configure-shared-image-gallery/enable-disable.png)
1. Gli utenti del lab possono quindi creare una macchina virtuale usando le immagini abilitate facendo clic su **Aggiungi** e trovando l'immagine nella pagina **Scegli la tua base.**

    ![Utenti del lab](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Allegare una raccolta di immagini condivise al lab
Se si usa un modello di Azure Resource Manager per allegare una raccolta di immagini condivise al lab, è necessario aggiungerlo nella sezione delle risorse del modello di Resource Manager, come illustrato nell'esempio seguente:If you're using an Azure Resource Manager template to attach a shared image gallery to your lab, you need to add it under the resources section of your Resource Manager template, as shown in the following example:

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

Per un esempio di modello completo di Resource Manager, vedere questi esempi di modelli di Resource Manager nel repository GitHub pubblico: [Configurare una raccolta di immagini condivise durante la creazione di un lab.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)

## <a name="use-api"></a>Utilizzare l'API

### <a name="shared-image-galleries---create-or-update"></a>Raccolte di immagini condivise - creare o aggiornare

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>Immagini raccolte immagini condivise - Elenco 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sulla creazione di una macchina virtuale usando un'immagine dalla raccolta di immagini condivise allegata: [Creare una macchina virtuale usando un'immagine condivisa dalla raccolta](add-vm-use-shared-image.md)
