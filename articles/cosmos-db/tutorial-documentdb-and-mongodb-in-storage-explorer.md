---
title: Gestire Azure Cosmos DB in Azure Storage Explorer
description: Informazioni su come gestire Azure Cosmos DB in Azure Storage Explorer.
Keywords: Azure Cosmos DB, Azure Storage Explorer, DocumentDB, MongoDB, DocumentDB
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.openlocfilehash: e695cdd7c51e18a386764ab8444d3336366ae265
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Gestire Azure Cosmos DB in Azure Storage Explorer (anteprima)

L'uso di Azure Cosmos DB in Azure Storage Explorer consente agli utenti di gestire le entità di Azure Cosmos DB, modificare i dati, aggiornare le stored procedure e attivarle assieme ad altre entità come code e BLOB di archiviazione di Azure. È ora possibile usare lo stesso strumento per gestire le diverse entità di Azure in un'unica posizione. A questo punto, Azure Storage Explorer supporta account SQL (DocumentDB) e MongoDB.

In questo articolo viene illustrato come usare Azure Storage Explorer per la gestione di Azure Cosmos DB.


## <a name="prerequisites"></a>Prerequisiti

Un account Azure Cosmos DB per un database di MongoDB o di SQL (DocumentDB). Se non si dispone di un account, è possibile crearne uno sul portale di Azure, come descritto in [Azure Cosmos DB: Creare un'app Web per le API DocumentDB con .NET e il portale di Azure](create-documentdb-dotnet.md).

## <a name="installation"></a>Installazione

Installare i bit più recenti di Azure Storage Explorer qui: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). È ora supportata la versione per Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

1. Dopo l'installazione di **Azure Storage Explorer**, fare clic sull'icona **plug-in** a sinistra, come illustrato nell'immagine seguente.
       
   ![Icona plug-in](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. Selezionare **Add an Azure Account** (Aggiungi un account Azure), quindi fare clic su **Accedi**.

   ![Connettersi a una sottoscrizione di Azure](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. Nella finestra di dialogo **Azure Sign In** (Accesso ad Azure), fare clic su **Accedi** e immettere le credenziali di Azure.

    ![pagina di accesso](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. Selezionare la sottoscrizione dall'elenco e quindi fare clic su **Applica**.

    ![Applica](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    Il riquadro di Explorer si aggiorna e consente di visualizzare gli account nella sottoscrizione selezionata.

    ![Elenco degli account](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    La connessione all'**account Azure Cosmos DB** nella sottoscrizione di Azure è stata eseguita correttamente.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Connettersi ad Azure Cosmos DB usando una stringa di connessione

Un modo alternativo per connettersi a un Azure Cosmos DB consiste nell'usare una stringa di connessione. Usare la procedura seguente per connettersi mediante una stringa di connessione.

1. Individuare **Local and Attached** (Locali e connesse) nella struttura ad albero a sinistra, fare doppio clic su **Account Azure Cosmos DB**, quindi scegliere **Connect to Azure Cosmos DB...** (Connetti ad Azure Cosmos DB...)

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. Scegliere l'**esperienza predefinita** per il tipo di account tra **DocumentDB** o **MongoDB**, incollare la **stringa di connessione**, quindi fare clic su **OK** per connettersi all'account di Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [Ottenere la stringa di connessione](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![Connection-string](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

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

     ![Apri nel portale](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* È possibile anche aggiungere l'account, il database o la raccolta di Azure Cosmos DB all'**accesso rapido**.
* **Search from Here** (Esegui ricerca da qui) consente la ricerca di parole chiave nel percorso selezionato.

    ![search from here (esegui ricerca da qui)](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gestione di un database e di una raccolta
#### <a name="create-a-database"></a>Creare un database 
Fare clic con il tasto destro sull'account di Azure Cosmos DB, scegliere **Crea database**, immettere il nome del database e premere **Invio** per completare la procedura.

![Creazione del database](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminare un database
Fare clic con il tasto destro sul database, fare clic su **Elimina Database**, quindi su **Sì** nella finestra popup. Il nodo del database viene eliminato e l'account di Azure Cosmos DB viene aggiornato automaticamente.

![Elimina database1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![Elimina database2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Creare una raccolta
Fare clic con il tasto destro sul database, scegliere **Crea raccolta**, quindi fornire le informazioni seguenti come **ID raccolta**, **Capacità di archiviazione**e così via. Fare clic su **OK** per terminare. Per informazioni sulle impostazioni della chiave di partizione, vedere [Progettazione del partizionamento](partition-data.md#designing-for-partitioning).

Se durante la creazione di una raccolta viene usata una chiave di partizione, una volta completata la creazione il valore della chiave di partizione non è modificabile dalla raccolta.

![Crea collection1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![Crea collection2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Eliminare una raccolta
Fare clic con il tasto destro sulla raccolta, fare clic su **Elimina raccolta**, quindi su **Sì** nella finestra popup. 

Il nodo della raccolta viene eliminato e il database viene aggiornato automaticamente.  

![Elimina raccolta](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gestione dei documenti

#### <a name="create-and-modify-documents"></a>Creare e modificare un documento
Per creare un nuovo documento, aprire **Documenti** nella finestra di sinistra, fare clic su **Nuovo documento**, modificare il contenuto nel riquadro a destra, quindi fare clic su **Salva**. È possibile anche aggiornare un documento esistente e quindi fare clic su **Salva**. Le modifiche possono essere ignorate facendo clic su **Ignora**.

![Documento](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminare un documento
Fare clic sul pulsante **Elimina** per eliminare il documento esistente.
#### <a name="query-for-documents"></a>Eseguire query per documenti
Modificare il filtro documento immettendo una [query SQL](documentdb-sql-query.md), quindi fare clic su **Applica**.

![Filtro](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gestire stored procedure, trigger e UDF
* Per creare una stored procedure, nella struttura ad albero di sinistra fare doppio clic su **Stored Procedure**, scegliere **Crea Stored Procedure**, immettere un nome nella finestra di sinistra, digitare gli script della stored procedure nella finestra di destra, quindi fare clic su **Crea**. 
* È anche possibile modificare le stored procedure esistenti facendo doppio clic, eseguendo l'aggiornamento e quindi facendo clic su **Aggiorna** per salvare oppure facendo clic su **Annulla** per annullare la modifica.

![Stored procedure](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* Le operazioni per **trigger** e **UDF** sono simili a quelle per le **stored procedure**.

## <a name="next-steps"></a>Passaggi successivi

* Guardare il video seguente per informazioni su come usare Azure Cosmos DB in Azure Storage Explorer: [Usare Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Per altre informazioni su Storage Explorer e per connettersi ad altri servizi, vedere [Guida introduttiva a Storage Explorer (anteprima)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).

