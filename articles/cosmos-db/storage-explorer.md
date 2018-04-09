---
title: Gestire Azure Cosmos DB in Azure Storage Explorer
description: Informazioni su come gestire Azure Cosmos DB in Azure Storage Explorer.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: 18f580f1eae31c9bf3626e100217467bb48ca881
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Gestire Azure Cosmos DB in Azure Storage Explorer (anteprima)

L'uso di Azure Cosmos DB in Azure Storage Explorer consente agli utenti di gestire le entità di Azure Cosmos DB, modificare i dati, aggiornare le stored procedure e attivarle assieme ad altre entità come code e BLOB di archiviazione di Azure. È ora possibile usare lo stesso strumento per gestire le diverse entità di Azure in un'unica posizione. Attualmente Azure Storage Explorer supporta account SQL, MongoDB, Graph e di tabella.

In questo articolo viene illustrato come usare Azure Storage Explorer per la gestione di Azure Cosmos DB.


## <a name="prerequisites"></a>prerequisiti

Un account Azure Cosmos DB per l'API SQL<!--or MongoDB API-->. Se non si ha un account, è possibile crearne uno nel portale di Azure, come descritto in [Azure Cosmos DB: Creare un'app Web per l'API con .NET e il portale di Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Installazione

Installare i bit più recenti di Azure Storage Explorer qui: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). È ora supportata la versione per Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

1. Dopo l'installazione di **Azure Storage Explorer**, fare clic sull'icona del **plug-in** a sinistra, come illustrato nell'immagine seguente:
       
   ![Icona plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Selezionare **Add an Azure Account** (Aggiungi un account Azure), quindi fare clic su **Accedi**.

   ![Connettersi a una sottoscrizione di Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. Nella finestra di dialogo **Azure Sign In** (Accesso ad Azure), fare clic su **Accedi** e immettere le credenziali di Azure.

    ![Accesso](./media/storage-explorer/sign-in.png)

3. Selezionare la sottoscrizione dall'elenco e quindi fare clic su **Applica**.

    ![Applica](./media/storage-explorer/apply-subscription.png)

    Il riquadro di Explorer si aggiorna e consente di visualizzare gli account nella sottoscrizione selezionata.

    ![Elenco degli account](./media/storage-explorer/account-list.png)

    La connessione all'**account Cosmos DB** nella sottoscrizione di Azure è stata eseguita correttamente.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Connettersi ad Azure Cosmos DB usando una stringa di connessione

Un modo alternativo per connettersi a un Azure Cosmos DB consiste nell'usare una stringa di connessione. Usare la procedura seguente per connettersi mediante una stringa di connessione.

1. Individuare **Local and Attached** (Locale e collegato) nella struttura ad albero a sinistra, fare clic con il pulsante destro del mouse su **Cosmos DB Accounts** (Account Cosmos DB) e quindi scegliere **Connect to Cosmos DB...** (Connetti a Cosmos DB).

    ![Connettersi a Cosmos DB usando una stringa di connessione](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Attualmente sono supportate solo API SQL e di tabella. Scegliere API, incollare la **stringa di connessione**, immettere un valore in **Account label** (Etichetta account), fare clic su **Next** (Avanti) per verificare il riepilogo e quindi fare clic su **Connect** (Connetti) per connettersi all'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [Ottenere la stringa di connessione](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Connection-string](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Connettersi ad Azure Cosmos DB usando un emulatore locale
Usare la procedura seguente per connettersi ad Azure Cosmos DB tramite un emulatore. Attualmente sono supportati solo account SQL.
1. Installare l'emulatore e avviarlo. Per informazioni su come installare l'emulatore, vedere [Emulatore di Cosmos DB](https://docs.microsoft.com/en-us/azure/cosmos-db/local-emulator).
2. Individuare **Local and Attached** (Locale e collegato) nella struttura ad albero a sinistra, fare clic con il pulsante destro del mouse su **Cosmos DB Accounts** (Account Cosmos DB) e quindi scegliere **Connect to Cosmos DB Emulator...** (Connetti all'emulatore di Cosmos DB).

    ![Connettersi a Cosmos DB tramite un emulatore](./media/storage-explorer/emulator-entry.png)

3. Attualmente sono supportate solo API SQL. Incollare la **stringa di connessione**, immettere un valore in **Account label** (Etichetta account), fare clic su **Next** (Avanti) per verificare il riepilogo e quindi fare clic su **Connect** (Connetti) per connettersi all'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [Ottenere la stringa di connessione](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Finestra di dialogo per la connessione a Cosmos DB tramite un emulatore](./media/storage-explorer/emulator-dialog.png)



## <a name="azure-cosmos-db-resource-management"></a>Gestione risorse di Azure Cosmos DB

È possibile gestire un account Azure Cosmos DB tramite la procedura seguente:
* Aprire l'account nel portale di Azure
* Aggiungere la risorsa all'elenco di accesso rapido
* Cercare e aggiornare le risorse
* Creare ed eliminare un database
* Creare ed eliminare una raccolta
* Creare, modificare, eliminare e filtrare documenti
* Gestire stored procedure, trigger e funzioni definite dall'utente

### <a name="quick-access-tasks"></a>Attività di accesso rapido

Facendo clic con il tasto destro su una sottoscrizione nel riquadro di Explorer, è possibile eseguire numerose attività di azione rapida:

* Facendo clic con il tasto destro su un account di Azure Cosmos DB o su un database è possibile scegliere **Apri nel portale** e gestire le risorse nel browser sul portale di Azure.

     ![Apri nel portale](./media/storage-explorer/open-in-portal.png)

* È possibile anche aggiungere l'account, il database o la raccolta di Azure Cosmos DB all'**accesso rapido**.
* **Search from Here** (Esegui ricerca da qui) consente la ricerca di parole chiave nel percorso selezionato.

    ![search from here (esegui ricerca da qui)](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gestione di un database e di una raccolta
#### <a name="create-a-database"></a>Creare un database 
-   Fare clic con il tasto destro sull'account di Azure Cosmos DB, scegliere **Crea database**, immettere il nome del database e premere **Invio** per completare la procedura.
       
    ![Creazione del database](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminare un database
- Fare clic con il tasto destro sul database, fare clic su **Elimina Database**, quindi su **Sì** nella finestra popup. Il nodo del database viene eliminato e l'account di Azure Cosmos DB viene aggiornato automaticamente.

    ![Elimina database1](./media/storage-explorer/delete-database1.png)  

    ![Elimina database2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Creare una raccolta
1. Fare clic con il tasto destro sul database, scegliere **Crea raccolta**, quindi fornire le informazioni seguenti come **ID raccolta**, **Capacità di archiviazione**e così via. Fare clic su **OK** per terminare. 

    ![Crea collection1](./media/storage-explorer/create-collection.png)

    ![Crea collection2](./media/storage-explorer/create-collection2.png) 

2. Selezionare **Unlimited** (Illimitata) per essere in grado di specificare la chiave di partizione e quindi fare clic su **OK** per terminare.

    Se durante la creazione di una raccolta viene usata una chiave di partizione, una volta completata la creazione il valore della chiave di partizione non è modificabile dalla raccolta. Per informazioni sulle impostazioni della chiave di partizione, vedere [Progettazione del partizionamento](partition-data.md#designing-for-partitioning).

    ![Chiave di partizione](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Eliminare una raccolta
- Fare clic con il tasto destro sulla raccolta, fare clic su **Elimina raccolta**, quindi su **Sì** nella finestra popup. 

    Il nodo della raccolta viene eliminato e il database viene aggiornato automaticamente.

    ![Elimina raccolta](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gestione dei documenti

#### <a name="create-and-modify-documents"></a>Creare e modificare un documento
- Per creare un nuovo documento, aprire **Documenti** nella finestra di sinistra, fare clic su **Nuovo documento**, modificare il contenuto nel riquadro a destra, quindi fare clic su **Salva**. È possibile anche aggiornare un documento esistente e quindi fare clic su **Salva**. Le modifiche possono essere ignorate facendo clic su **Ignora**.

    ![Documento](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminare un documento
- Fare clic sul pulsante **Elimina** per eliminare il documento esistente.

#### <a name="query-for-documents"></a>Eseguire query per documenti
- Modificare il filtro documento immettendo una [query SQL](sql-api-sql-query.md), quindi fare clic su **Applica**.

    ![Filtro del documento](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Gestione dei grafi

#### <a name="create-and-modify-vertex"></a>Creare e modificare un vertice
1. Per creare un nuovo vertice, aprire **Graph** dalla finestra a sinistra, fare clic su **New Vertex** (Nuovo vertice), modificare il contenuto e quindi fare clic su **OK**.    
2. Per modificare un vertice esistente, fare clic sull'icona della penna nel riquadro a destra.   

    ![Grafico](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Eliminare un grafo
- Per eliminare un vertice, fare clic sull'icona del Cestino accanto al nome del vertice.

#### <a name="filter-for-graph"></a>Filtrare un grafo
- Modificare il filtro del grafo immettendo una [query gremlin](gremlin-support.md) e quindi fare clic su **Apply Filter** (Applica filtro).

    ![Filtro del grafo](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Gestione delle tabelle

#### <a name="create-and-modify-table"></a>Creare e modificare una tabella
1. Per creare una nuova tabella, aprire **Entities** (Entità) dalla finestra a sinistra, fare clic su **Add** (Aggiungi), modificare il contenuto nella finestra di dialogo **Add Entity** (Aggiungi entità), aggiungere una proprietà facendo clic sul pulsante **Add Property** (Aggiungi proprietà) e quindi fare clic su **Insert** (Inserisci).
2. Per modificare una tabella, fare clic su **Edit** (Modifica), modificare il contenuto e quindi fare clic su **Update** (Aggiorna).

    ![Tabella](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importare ed esportare una tabella
1. Per importare, fare clic sul pulsante **Import** (Importa) e scegliere una tabella esistente.
2. Per esportare, fare clic sul pulsante **Export** (Esporta) e scegliere una destinazione.

    ![Importazione ed esportazione di tabelle](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Eliminare entità
- Selezionare le entità e fare clic sul pulsante **Delete** (Elimina).

    ![Eliminazione di tabelle](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Eseguire query su tabelle
- Fare clic sul pulsante **Query**, specificare la condizione di query e quindi fare clic sul pulsante **Esegui query**. Chiudere il riquadro Query facendo clic sul pulsante **Close Query** (Chiudi query).

    ![Query di tabella](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gestire stored procedure, trigger e UDF
* Per creare una stored procedure, nella struttura ad albero di sinistra fare doppio clic su **Stored Procedure**, scegliere **Crea Stored Procedure**, immettere un nome nella finestra di sinistra, digitare gli script della stored procedure nella finestra di destra, quindi fare clic su **Crea**. 
* È anche possibile modificare le stored procedure esistenti facendo doppio clic, eseguendo l'aggiornamento e quindi facendo clic su **Aggiorna** per salvare oppure facendo clic su **Annulla** per annullare la modifica.

    ![Stored procedure](./media/storage-explorer/stored-procedure.png)
* Le operazioni per **trigger** e **UDF** sono simili a quelle per le **stored procedure**.

## <a name="next-steps"></a>Passaggi successivi

* Guardare il video seguente per informazioni su come usare Azure Cosmos DB in Azure Storage Explorer: [Usare Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Per altre informazioni su Storage Explorer e per connettersi ad altri servizi, vedere [Guida introduttiva a Storage Explorer (anteprima)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

