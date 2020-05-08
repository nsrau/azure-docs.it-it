---
title: Configurare una raccolta di immagini condivise in Azure DevTest Labs | Microsoft Docs
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
ms.openlocfilehash: 7591f22286f9ac451a15dd926adab0212adb190e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691291"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Configurare una raccolta immagini condivisa in Azure DevTest Labs
DevTest Labs supporta ora la funzionalità [raccolta immagini condivise](../virtual-machines/windows/shared-image-galleries.md) . Consente agli utenti del Lab di accedere alle immagini da un percorso condiviso durante la creazione di risorse Lab. Consente inoltre di creare strutture e organizzazioni per le immagini di macchina virtuale gestite in modo personalizzato. La funzionalità raccolta immagini condivise supporta:

- Replica globale gestita delle immagini
- Controllo delle versioni e raggruppamento di immagini per una gestione più semplice
- Rendere le immagini a disponibilità elevata con account di archiviazione con ridondanza della zona (ZRS) in aree che supportano le zone di disponibilità. ZRS offre una migliore resilienza per gli errori di zona.
- Condivisione tra sottoscrizioni e anche tra tenant, usando il controllo degli accessi in base al ruolo (RBAC).

Per ulteriori informazioni, vedere la [documentazione relativa alla raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md). 
 
Se si dispone di un numero elevato di immagini gestite da mantenere e si desidera renderle disponibili in tutta l'azienda, è possibile usare una raccolta di immagini condivise come repository che semplifica l'aggiornamento e la condivisione delle immagini. In qualità di proprietario del Lab, è possibile alleghi una raccolta di immagini condivise esistente al Lab. Una volta collegata questa raccolta, gli utenti del Lab possono creare computer da queste immagini più recenti. Un vantaggio fondamentale di questa funzionalità è che DevTest Labs ora può sfruttare i vantaggi della condivisione delle immagini tra i Lab, le sottoscrizioni e le aree. 

> [!NOTE]
> Per informazioni sui costi associati al servizio raccolta immagini condivise, vedere [fatturazione per la raccolta di immagini condivise](../virtual-machines/windows/shared-image-galleries.md#billing).

## <a name="considerations"></a>Considerazioni
- È possibile alleghi solo una raccolta di immagini condivise a un Lab alla volta. Se si vuole allontanare un'altra raccolta, sarà necessario scollegare quella esistente e alleghirne un'altra. 
- DevTest Labs supporta attualmente solo le immagini generalizzate della raccolta immagini condivise.
- DevTest Labs attualmente non supporta il caricamento di immagini nella raccolta tramite il Lab. 
- Quando si crea una macchina virtuale usando un'immagine della raccolta di immagini condivise, DevTest Labs USA sempre la versione pubblicata più recente dell'immagine. Tuttavia, se un'immagine include più versioni, l'utente può scegliere di creare un computer da una versione precedente passando alla scheda Impostazioni avanzate durante la creazione della macchina virtuale.  
- Anche se DevTest Labs esegue automaticamente un tentativo migliore di verificare che la raccolta di immagini condivise replica le immagini nell'area in cui è presente il Lab, non è sempre possibile. Per evitare che si verifichino problemi durante la creazione di macchine virtuali da queste immagini, assicurarsi che le immagini siano già replicate nell'area del Lab ".

## <a name="use-azure-portal"></a>Usare il portale di Azure
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **tutti i servizi** nel menu di spostamento a sinistra.
1. Selezionare **DevTest Labs** dall'elenco.
1. Dall'elenco dei Lab selezionare il **Lab**.
1. Selezionare **configurazione e criteri** nella sezione **Impostazioni** nel menu a sinistra.
1. Selezionare **raccolte immagini condivise** in **base macchina virtuale** nel menu a sinistra.

    ![Menu raccolte immagini condivise](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. Alleghi una raccolta di immagini condivise esistente al Lab facendo clic sul pulsante **Connetti** e selezionando la raccolta nell'elenco a discesa.

    ![Attach](./media/configure-shared-image-gallery/attach-options.png)
1. Passare alla raccolta collegata e configurare la raccolta per **abilitare o disabilitare** le immagini condivise per la creazione di macchine virtuali. Selezionare una raccolta di immagini dall'elenco per configurarla. 

    Per impostazione predefinita, **Consenti l'uso di tutte le immagini come basi della macchina virtuale** è impostato su **Sì**. Ciò significa che tutte le immagini disponibili nella raccolta di immagini condivise collegate saranno disponibili per un utente del Lab quando si crea una nuova macchina virtuale Lab. Se è necessario limitare l'accesso a determinate immagini, modificare **Consenti l'uso di tutte le immagini come basi della macchina virtuale** e selezionare le immagini che si desidera consentire durante la **creazione di macchine**virtuali, quindi selezionare il pulsante **Salva** .

    ![Abilita o Disabilita](./media/configure-shared-image-gallery/enable-disable.png)
1. Gli utenti del Lab possono quindi creare una macchina virtuale usando le immagini abilitate facendo clic su **+ Aggiungi** e individuando l'immagine nella pagina **Scegli la base** .

    ![Utenti del Lab](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Usare un modello di Azure Resource Manager

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>Alleghi una raccolta di immagini condivise al Lab
Se si usa un modello di Azure Resource Manager per alleghi una raccolta di immagini condivise al Lab, è necessario aggiungerlo nella sezione delle risorse del modello di Gestione risorse, come illustrato nell'esempio seguente:

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

Per un esempio di modello di Gestione risorse completo, vedere questi esempi di modelli di Gestione risorse nel repository pubblico di GitHub: [configurare una raccolta di immagini condivise durante la creazione di un Lab](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured).

## <a name="use-rest-api"></a>Usare l'API REST

### <a name="get-a-list-of-labs"></a>Ottenere un elenco di Lab 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs?api-version= 2018-10-15-preview
```

### <a name="get-the-list-of-shared-image-galleries-associated-with-a-lab"></a>Ottenere l'elenco di raccolte di immagini condivise associate a un Lab

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries?api-version= 2018-10-15-preview
   ```

### <a name="create-or-update-shared-image-gallery"></a>Crea o Aggiorna raccolta immagini condivise

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

### <a name="list-images-in-a-shared-image-gallery"></a>Elencare le immagini in una raccolta di immagini condivise

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```



## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti sulla creazione di una macchina virtuale usando un'immagine dalla raccolta di immagini condivise collegata: [creare una VM usando un'immagine condivisa dalla raccolta](add-vm-use-shared-image.md)
