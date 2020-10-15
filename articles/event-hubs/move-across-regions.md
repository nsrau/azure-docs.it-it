---
title: Spostare uno spazio dei nomi di hub eventi di Azure in un'altra area | Microsoft Docs
description: Questo articolo illustra come spostare uno spazio dei nomi di hub eventi di Azure dall'area corrente a un'altra area.
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: b177c3916919e3d97325f9d8c6b6027c00cb476f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375194"
---
# <a name="move-an-azure-event-hubs-namespace-to-another-region"></a>Spostare uno spazio dei nomi di hub eventi di Azure in un'altra area
Questo articolo illustra come esportare un modello di Azure Resource Manager per uno spazio dei nomi di hub eventi esistente e quindi usare il modello per creare uno spazio dei nomi con le stesse impostazioni di configurazione in un'altra area. Tuttavia, questo processo non sposta gli eventi non ancora elaborati. È necessario elaborare gli eventi dallo spazio dei nomi originale prima di eliminarlo.
 
Se sono presenti altre risorse nel gruppo di risorse di Azure che contiene lo spazio dei nomi di hub eventi, è possibile esportare il modello a livello di gruppo di risorse in modo che tutte le risorse correlate possano essere spostate nella nuova area in un unico passaggio. I passaggi di questo articolo illustrano come esportare uno **spazio dei nomi** nel modello. I passaggi per l'esportazione di un **gruppo di risorse** nel modello sono simili. 

## <a name="prerequisites"></a>Prerequisiti

- Assicurarsi che i servizi e le funzionalità usati dall'account siano supportati nell'area di destinazione.
- Se è stata abilitata la **funzionalità di acquisizione** per hub eventi nello spazio dei nomi, spostare [Azure Storage o Azure Data Lake Store](../storage/common/storage-account-move.md) gli account di generazione 2 o [Azure Data Lake Store di generazione 1](../data-lake-store/data-lake-store-migration-cross-region.md) prima di spostare lo spazio dei nomi di hub eventi. È anche possibile spostare il gruppo di risorse che contiene gli spazi dei nomi dell'hub eventi e di archiviazione nell'altra area seguendo questa procedura in modo analogo a quelli descritti in questo articolo. 
- Se lo spazio dei nomi di hub eventi si trova in un **cluster di hub eventi**, [spostare il cluster dedicato](move-cluster-across-regions.md) nell' **area di destinazione** prima di procedere con i passaggi descritti in questo articolo. È anche possibile usare il [modello di avvio rapido in GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-cluster-namespace-eventhub/) per creare un cluster di hub eventi. Nel modello rimuovere la parte relativa allo spazio dei nomi di JSON per creare solo il cluster. 

## <a name="prepare"></a>Preparazione
Per iniziare, esportare un modello di Gestione risorse. Questo modello contiene le impostazioni che descrivono lo spazio dei nomi di hub eventi.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **tutte le risorse** e quindi selezionare lo spazio dei nomi di hub eventi.
3. Selezionare > **Impostazioni**  >  **Esporta modello**.
4. Scegliere **download** nella pagina **Esporta modello** .

    ![Scarica Gestione risorse modello](./media/move-across-regions/download-template.png)
5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta.

   Questo file zip contiene i file con estensione JSON che includono il modello e gli script per distribuire il modello.


## <a name="move"></a>Spostamento

Distribuire il modello per creare uno spazio dei nomi di hub eventi nell'area di destinazione. 


1. Nella portale di Azure selezionare **Crea una risorsa**.
2. In **Cerca nel Marketplace**Digitare **distribuzione modello**e selezionare **distribuzione modelli (Distribuisci usando modelli personalizzati)**.
5. Selezionare **Creare un modello personalizzato nell'editor**.
6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il **template.js** nel file scaricato nell'ultima sezione.
1. Aggiornare il valore della `location` Proprietà in modo che punti alla nuova area. Per ottenere i codici di località, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/). Il codice per un'area è il nome dell'area senza spazi, ad esempio, `West US` è uguale a `westus` .
1. Selezionare **Save (Salva** ) per salvare il modello. 
1. Nella pagina **distribuzione personalizzata** , attenersi alla seguente procedura: 
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
        3. Se l'hub eventi nello spazio dei nomi usa un account di archiviazione per l'acquisizione di eventi, specificare il nome del gruppo di risorse e l'account di archiviazione per il `StorageAccounts_<original storage account name>_external` campo. 
            
            ```
            /subscriptions/0000000000-0000-0000-0000-0000000000000/resourceGroups/<STORAGE'S RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>
            ```    
    5. Selezionare **Rivedi e crea** nella parte inferiore della pagina. 
    1. Nella pagina **Verifica e crea** verificare le impostazioni e quindi selezionare **Crea**.   

## <a name="discard-or-clean-up"></a>Eliminare o pulire
Dopo la distribuzione, se si desidera ricominciare, è possibile eliminare lo **spazio dei nomi di hub eventi di destinazione**e ripetere i passaggi descritti nelle sezioni [preparare](#prepare) e [spostare](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di uno spazio dei nomi di hub eventi, eliminare lo **spazio dei nomi di hub eventi** nell'area originale. Prima di eliminare lo spazio dei nomi, verificare di aver elaborato tutti gli eventi nello spazio dei nomi. 

Per eliminare uno spazio dei nomi di hub eventi (origine o destinazione) usando il portale di Azure:

1. Nella finestra di ricerca nella parte superiore di portale di Azure digitare **Hub eventi**e selezionare **Hub eventi** nei risultati della ricerca. Gli spazi dei nomi di hub eventi vengono visualizzati in un elenco.
2. Selezionare lo spazio dei nomi di destinazione da eliminare e selezionare **Elimina** dalla barra degli strumenti. 

    ![Pulsante Elimina spazio dei nomi](./media/move-across-regions/delete-namespace-button.png)
3. Nella pagina **Elimina spazio dei nomi** confermare l'eliminazione digitando il **nome dello spazio dei**nomi e quindi selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato uno spazio dei nomi di hub eventi di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo spostamento di risorse tra aree e sul ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Spostare macchine virtuali di Azure in un'altra area](../site-recovery/azure-to-azure-tutorial-migrate.md)
