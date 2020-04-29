---
title: Spostare uno spazio dei nomi di hub eventi di Azure in un'altra area | Microsoft Docs
description: Questo articolo illustra come spostare uno spazio dei nomi di hub eventi di Azure dall'area corrente a un'altra area.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606808"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Spostare uno spazio dei nomi di hub eventi di Azure in un'altra area
Esistono diversi scenari in cui si vuole spostare lo spazio dei nomi di hub eventi esistente da un'area a un'altra. Ad esempio, è possibile creare uno spazio dei nomi con la stessa configurazione per il test. È anche possibile creare uno spazio dei nomi secondario in un'altra area nell'ambito della [pianificazione del ripristino di emergenza](event-hubs-geo-dr.md#setup-and-failover-flow).

> [!NOTE]
> Questo articolo illustra come esportare un modello di Azure Resource Manager per uno spazio dei nomi di hub eventi esistente e quindi usare il modello per creare uno spazio dei nomi con le stesse impostazioni di configurazione in un'altra area. Tuttavia, questo processo non sposta gli eventi non ancora elaborati. È necessario elaborare gli eventi dallo spazio dei nomi originale prima di eliminarlo.

## <a name="prerequisites"></a>Prerequisiti

- Verificare che i servizi e le funzionalità usati dall'account siano supportati nell'area di destinazione.
- Per le funzionalità di anteprima, assicurarsi che la sottoscrizione sia consentita per l'area di destinazione.
- Se è stata abilitata la **funzionalità di acquisizione** per hub eventi nello spazio dei nomi, spostare [Azure Storage o Azure Data Lake Store](../storage/common/storage-account-move.md) gli account di generazione 2 o [Azure Data Lake Store di generazione 1](../data-lake-store/data-lake-store-migration-cross-region.md) prima di spostare lo spazio dei nomi di hub eventi. È anche possibile spostare il gruppo di risorse che contiene gli spazi dei nomi dell'hub eventi e di archiviazione nell'altra area seguendo questa procedura in modo analogo a quelli descritti in questo articolo. 
- Se lo spazio dei nomi di hub eventi si trova in un **cluster di hub eventi**, [creare un cluster dedicato](event-hubs-dedicated-cluster-create-portal.md) nell' **area di destinazione** prima di procedere con i passaggi descritti in questo articolo. 

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse. Questo modello contiene le impostazioni che descrivono lo spazio dei nomi di hub eventi.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **tutte le risorse** e quindi selezionare lo spazio dei nomi di hub eventi.

3. Selezionare > **Impostazioni** > **Esporta modello**.

4. Scegliere **download** nella pagina **Esporta modello** .

    ![Scarica Gestione risorse modello](./media/move-across-regions/download-template.png)

5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta.

   Questo file zip contiene i file con estensione JSON che includono il modello e gli script per distribuire il modello.


## <a name="move"></a>Sposta

Distribuire il modello per creare uno spazio dei nomi di hub eventi nell'area di destinazione. 


1. Nella portale di Azure selezionare **Crea una risorsa**.

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Selezionare **Distribuzione modello**.

4. Selezionare **Crea**.

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il file **template. JSON** scaricato nell'ultima sezione.

7. Selezionare **Save (Salva** ) per salvare il modello. 

8. Nella pagina **distribuzione personalizzata** , attenersi alla seguente procedura: 

    1. Selezionare una **sottoscrizione**di Azure. 

    2. Selezionare un **gruppo di risorse** esistente o crearne uno. Se lo spazio dei nomi di origine si trova in un cluster di hub eventi, selezionare il gruppo di risorse che contiene il cluster nell'area di destinazione. 

    3. Selezionare la **località** o l'area di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura. 

    4. Nella sezione **Impostazioni** seguire questa procedura:
    
        1. Immettere il nome del nuovo **spazio dei nomi**. 

            ![Distribuisci modello di Gestione risorse](./media/move-across-regions/deploy-template.png)

        2. Se lo spazio dei nomi di origine si trova in un **cluster di hub eventi**, immettere i nomi del **gruppo di risorse** e del **cluster di hub eventi** come parte dell' **ID esterno**. 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se l'hub eventi nello spazio dei nomi usa un account di archiviazione per l'acquisizione di eventi, specificare il nome del gruppo `StorageAccounts_<original storage account name>_external` di risorse e l'account di archiviazione per il campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selezionare la casella di controllo **Accetto le condizioni riportate sopra**. 
    
    6. Selezionare quindi **Seleziona Acquista** per avviare il processo di distribuzione. 

## <a name="discard-or-clean-up"></a>Elimina o Pulisci
Dopo la distribuzione, se si desidera ricominciare, è possibile eliminare lo **spazio dei nomi di hub eventi di destinazione**e ripetere i passaggi descritti nelle sezioni [preparare](#prepare) e [spostare](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di uno spazio dei nomi di hub eventi, eliminare lo **spazio dei nomi di hub eventi di origine**. Prima di eliminare lo spazio dei nomi, verificare di aver elaborato tutti gli eventi nello spazio dei nomi. 

Per eliminare uno spazio dei nomi di hub eventi (origine o destinazione) usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **Hub eventi**e selezionare **Hub eventi** nei risultati della ricerca. Gli spazi dei nomi di hub eventi vengono visualizzati in un elenco.

2. Selezionare lo spazio dei nomi di destinazione da eliminare e selezionare **Elimina** dalla barra degli strumenti. 

    ![Pulsante Elimina spazio dei nomi](./media/move-across-regions/delete-namespace-button.png)

3. Nella pagina **Elimina risorse*** verificare le risorse selezionate e confermare l'eliminazione digitando **Sì**, quindi selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato uno spazio dei nomi di hub eventi di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
