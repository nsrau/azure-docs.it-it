---
title: Creare una funzione attivata da Azure Cosmos DB | Microsoft Docs
description: Usare Funzioni di Azure per creare una funzione senza server che viene richiamata quando vengono aggiunti i dati al database in Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/07/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 9ee3eddda53052c47f2cecff80f971f5eadac414
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2018
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Creare una funzione attivata da Azure Cosmos DB

Informazioni su come creare una funzione attivata quando i dati vengono aggiunti o modificati in Azure Cosmos DB. Per altre informazioni su Azure Cosmos DB, vedere [Azure Cosmos DB: elaborazione di database senza server con Funzioni di Azure](..\cosmos-db\serverless-computing-database.md).

![Visualizzare il messaggio nei log.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione:

+ Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Creare un'app per le funzioni di Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Si creerà ora una funzione nella nuova app per le funzioni.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Creare i trigger di Azure Cosmos DB

1. Espandere l'app per le funzioni e fare clic sul pulsante **+** accanto a **Funzioni**. Se questa è la prima funzione nell'app per le funzioni, selezionare **Funzione personalizzata**. Verrà visualizzato il set completo di modelli di funzione.

    ![Pagina della guida introduttiva di Funzioni nel portale di Azure](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. Nel campo di ricerca digitare `cosmos` e quindi scegliere la lingua da usare per il modello di attivazione di Azure Cosmos DB.

    ![Scegliere il trigger di Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Configurare il nuovo trigger con le impostazioni come indicato nella tabella sotto l'immagine.

    ![Creare la funzione attivata da Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Impostazione      | Valore consigliato  | Descrizione                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Nome** | Predefinito | Usare il nome della funzione predefinito suggerito dal modello. |
    | **Nome raccolta** | Items | Nome della raccolta da monitorare. |
    | **Creare la raccolta di lease se non esiste** | Selezionato | La raccolta non esiste ancora, quindi crearla. |
    | **Database name** (Nome database) | Attività | Nome del database con la raccolta da monitorare. |

4. Selezionare **Nuovo** accanto all'etichetta **della connessione dell'account Cosmos DB di Azure** e selezionare un account Cosmos DB esistente o fare clic su **+ Crea nuovo**. 
 
    ![Configurare la connessione di Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. Quando si crea un nuovo account Cosmos DB, usare le impostazioni del **nuovo account** come indicato nella tabella.

    | Impostazione      | Valore consigliato  | DESCRIZIONE                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **ID** | Nome del database | ID univoco per il database di Azure Cosmos DB  |
    | **API** | SQL (DocumentDB) | Questo argomento usa l'API del database di documenti.  |
    | **Sottoscrizione** | Sottoscrizione di Azure | Sottoscrizione in cui viene creato questo nuovo account Cosmos DB.  |
    | **Gruppo di risorse** | myResourceGroup |  Usare il gruppo di risorse esistente che contiene l'app per le funzioni. |
    | **Posizione**  | Europa occidentale | Selezionare una località nei pressi dell'app per le funzioni o di altre app che usano i documenti archiviati.  |

6. Fare clic su **OK** per creare il database. La creazione del database può richiedere alcuni minuti. Dopo avere creato il database, la stringa di connessione del database viene archiviata come impostazione dell'app per le funzioni. Il nome di questa impostazione dell'app viene inserito nella **connessione dell'account di Azure Cosmos DB**. 

7. Fare clic su **Crea** per creare la funzione attivata da Azure Cosmos DB. Dopo aver creato la funzione, viene visualizzato il codice della funzione basato sul modello.  

    ![Modello di funzione di Cosmos DB in C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Questo modello di funzione scrive il numero di documenti e l'ID del primo documento nei log. 

Successivamente, connettersi al proprio account di Azure Cosmos DB e creare la raccolta **Attività** nel database. 

## <a name="create-the-items-collection"></a>Creare la raccolta di elementi

1. Aprire una seconda istanza del [portale di Azure](https://portal.azure.com) in una nuova scheda del browser. 

2. Sul lato sinistro del portale espandere la barra delle icone, digitare `cosmos` nel campo di ricerca e selezionare **Azure Cosmos DB**.

    ![Cercare il servizio Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Scegliere l'account di Azure Cosmos DB, quindi selezionare **Esplora dati**. 
 
3. In **Raccolte** scegliere **taskDatabase** e selezionare **Nuova raccolta**.

    ![Creare una raccolta](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. In **Aggiungi raccolta** usare le impostazioni visualizzate nella tabella riportata sotto l'immagine. 
 
    ![Definire taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Impostazione|Valore consigliato|DESCRIZIONE |
    | ---|---|--- |
    | **ID database** | Attività |Nome del nuovo database. Deve corrispondere al nome definito nell'associazione della funzione. |
    | **ID raccolta** | Items | Nome della nuova raccolta. Deve corrispondere al nome definito nell'associazione della funzione.  |
    | **Capacità di archiviazione** | Fissa (10 GB)|Usare il valore predefinito. Questo valore indica la capacità di archiviazione del database. |
    | **Velocità effettiva** |400 UR| Usare il valore predefinito. Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento. |
    | **[Chiave di partizione](../cosmos-db/partition-data.md#design-for-partitioning)** | /category|Chiave di partizione che distribuisce i dati in modo uniforme a ogni partizione. Quando si crea una raccolta ad alte prestazioni è importante selezionare la chiave di partizione corretta. | 

1. Fare clic su **OK** per creare la raccolta **Attività**. La creazione della raccolta potrebbe richiedere del tempo.

Dopo aver creato la raccolta specificata nell'associazione della funzione, è possibile testare la funzione aggiungendo i documenti a questa nuova raccolta.

## <a name="test-the-function"></a>Testare la funzione

1. Espandere la nuova raccolta **taskCollection** in Esplora dati, scegliere **Documenti**, quindi selezionare **Nuovo documento**.

    ![Creare un documento in taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Sostituire il contenuto del nuovo documento con il contenuto seguente, quindi scegliere **Salva**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Passare alla prima scheda del browser che contiene la funzione nel portale. Espandere i log della funzione e verificare che il nuovo documento abbia attivato la funzione. Verificare che il valore dell'ID del documento `task1` sia scritto nel log. 

    ![Visualizzare il messaggio nei log.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Facoltativo) Tornare al documento, apportare una modifica e fare clic su **Aggiorna**. Quindi, tornare ai log della funzione e verificare che l'aggiornamento abbia attivato anche la funzione.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

È stata creata una funzione che viene eseguita quando un documento viene aggiunto o modificato nel database di Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Per altre informazioni sui trigger di Azure Cosmos DB, vedere [Binding di Azure Cosmos DB per Funzioni di Azure](functions-bindings-cosmosdb.md).
