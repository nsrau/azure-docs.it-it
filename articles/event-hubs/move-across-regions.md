---
title: Spostare uno spazio dei nomi di Hub eventi di Azure in un'altra area. Documenti Microsoft
description: Questo articolo illustra come spostare uno spazio dei nomi di Hub eventi di Azure dall'area corrente a un'altra area.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 04/14/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 2dfc9c517605bbb48bee0b306fb275464cfebe39
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606808"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Spostare uno spazio dei nomi Hub eventi di Azure in un'altra areaMove an Azure Event Hubs namespace to another region
Esistono vari scenari in cui si desidera spostare lo spazio dei nomi Hub eventi esistente da un'area a un'altra. Ad esempio, è possibile creare uno spazio dei nomi con la stessa configurazione per il test. È anche possibile creare uno spazio dei nomi secondario in un'altra area come parte della pianificazione del ripristino di [emergenza.](event-hubs-geo-dr.md#setup-and-failover-flow)

> [!NOTE]
> Questo articolo illustra come esportare un modello di Azure Resource Manager per uno spazio dei nomi Hub eventi esistente e quindi usare il modello per creare uno spazio dei nomi con le stesse impostazioni di configurazione in un'altra area. Tuttavia, questo processo non sposta gli eventi che non sono ancora stati elaborati. È necessario elaborare gli eventi dallo spazio dei nomi originale prima di eliminarli.

## <a name="prerequisites"></a>Prerequisiti

- Verificare che i servizi e le funzionalità utilizzati dall'account siano supportati nell'area geografica di destinazione.
- Per le funzionalità di anteprima, verificare che la sottoscrizione sia inserita nella whitelist per l'area di destinazione.
- Se la funzionalità di **acquisizione** è abilitata per gli hub eventi nello spazio dei nomi, spostare gli account [di Archiviazione di Azure o Azure Data Lake Store Gen 2](../storage/common/storage-account-move.md) o Azure Data Lake Store Gen [1](../data-lake-store/data-lake-store-migration-cross-region.md) prima di spostare lo spazio dei nomi Hub eventi. È anche possibile spostare il gruppo di risorse che contiene gli spazi dei nomi Storage e Event Hubs nell'altra area seguendo passaggi simili a quelli descritti in questo articolo. 
- Se lo spazio dei nomi Hub eventi si trova in un **cluster Hub eventi,** [creare un cluster dedicato](event-hubs-dedicated-cluster-create-portal.md) nell'area di **destinazione** prima di eseguire i passaggi di questo articolo. 

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Resource Manager.To get started, export a Resource Manager template. Questo modello contiene impostazioni che descrivono lo spazio dei nomi Hub eventi.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Tutte le risorse** e quindi selezionare lo spazio dei nomi Hub eventi.

3. Selezionare >**modello di esportazione** **impostazioni** > .

4. Scegliere **Scarica** nella pagina **Esporta modello.**

    ![Scaricare il modello di Resource Manager](./media/move-across-regions/download-template.png)

5. Individuare il file .zip scaricato dal portale e decomprimerlo in una cartella di propria scelta.

   Questo file zip contiene i file .json che includono il modello e gli script per distribuire il modello.


## <a name="move"></a>Spostamento

Distribuire il modello per creare uno spazio dei nomi Hub eventi nell'area di destinazione. 


1. Nel portale di Azure selezionare **Crea una risorsa**.

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Selezionare **Distribuzione modello**.

4. Selezionare **Create** (Crea).

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il file **template.json** scaricato nell'ultima sezione.

7. Selezionare **Salva** per salvare il modello. 

8. Nella pagina **Distribuzione personalizzata** eseguire la procedura seguente:On the Custom deployment page, follow these steps: 

    1. Selezionare una **sottoscrizione**di Azure . 

    2. Selezionare un **gruppo di risorse** esistente o crearne uno. Se lo spazio dei nomi di origine si trovava in un cluster Hub eventi, selezionare il gruppo di risorse che contiene il cluster nell'area di destinazione. 

    3. Selezionare la **posizione** o l'area geografica di destinazione. Se è stato selezionato un gruppo di risorse esistente, questa impostazione è di sola lettura. 

    4. Nella sezione **SETTINGS** eseguire le operazioni seguenti:
    
        1. Immettere il nuovo **nome dello spazio dei nomi**. 

            ![Distribuire il modello di Resource ManagerDeploy Resource Manager template](./media/move-across-regions/deploy-template.png)

        2. Se lo spazio dei nomi di origine si trovava in un **cluster Hub eventi**, immettere i nomi del gruppo di **risorse** e del cluster **Hub eventi** come parte dell'ID **esterno.** 

              ```
              /subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<CLUSTER'S RESOURCE GROUP>/providers/Microsoft.EventHub/clusters/<CLUSTER NAME>
              ```   
        3. Se l'hub eventi nello spazio dei nomi usa un account di `StorageAccounts_<original storage account name>_external` archiviazione per l'acquisizione degli eventi, specificare il nome del gruppo di risorse e l'account di archiviazione per il campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selezionare la casella di controllo **Accetto le condizioni riportate sopra**. 
    
    6. A questo punto, **selezionare Acquista** per avviare il processo di distribuzione. 

## <a name="discard-or-clean-up"></a>Scartare o pulire
Dopo la distribuzione, se si desidera ricominciare da capo, è possibile eliminare lo spazio dei nomi **Hub eventi**di destinazione e ripetere i passaggi descritti nelle sezioni [Preparazione](#prepare) e [spostamento](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di uno spazio dei nomi Hub eventi, eliminare lo spazio dei **nomi Hub eventi di origine.** Assicurarsi di aver elaborato tutti gli eventi nello spazio dei nomi prima di eliminare lo spazio dei nomi. 

Per eliminare uno spazio dei nomi Hub eventi (origine o destinazione) tramite il portale di Azure:To delete an Event Hubs namespace (source or target) by using the Azure portal:

1. Nella finestra di ricerca nella parte superiore del portale di Azure digitare **Hub eventi**e selezionare **Hub eventi** dai risultati della ricerca. Gli spazi dei nomi Hub eventi vengono visualizzati in un elenco.

2. Selezionare lo spazio dei nomi di destinazione da eliminare e selezionare **Elimina** dalla barra degli strumenti. 

    ![Elimina spazio dei nomi - pulsante](./media/move-across-regions/delete-namespace-button.png)

3. Nella pagina **Elimina risorse**, verificare le risorse selezionate e confermare l'eliminazione digitando **sì**, quindi selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato uno spazio dei nomi Hub eventi di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
