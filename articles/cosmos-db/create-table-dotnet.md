---
title: 'Guida introduttiva: API Tabelle con .NET - Azure Cosmos DB | Microsoft Docs'
description: Questa guida introduttiva illustra come usare l'API Tabelle di Azure Cosmos DB per creare un'applicazione con il portale di Azure e .NET
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmos-db
ms.custom: quickstart connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: 44637049dd5d6cfe353afe98427d843a0d4e403a
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Guida introduttiva: Creare un'app dell'API Tabelle con .NET e Azure Cosmos DB 

Questa guida introduttiva descrive come usare .NET e l'[API Tabelle](table-introduction.md) di Azure Cosmos DB per creare un'app clonando un esempio di GitHub. La guida introduttiva illustra anche come creare un account Azure Cosmos DB e come usare Esplora dati per creare tabelle e entità nel portale di Azure basato sul Web.

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

> [!IMPORTANT] 
> Per lavorare con gli SDK dell'API di tabella disponibili a livello generale, è necessario creare un nuovo account dell'API di tabella. Gli account dell'API di tabella creati durante l'anteprima non sono supportati dagli SDK disponibili a livello generale.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Aggiungere una tabella

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Aggiungere dati di esempio

È ora possibile aggiungere dati alla nuova tabella usando Esplora dati.

1. In Esplora dati espandere **sample-table**, fare clic su **Entità** e quindi su **Aggiungi entità**.

   ![Creare nuove entità in Esplora dati nel portale di Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
2. Aggiungere ora i dati alle caselle dei valori di PartitionKey e RowKey e quindi fare clic su **Aggiungi entità**.

   ![Configurare la chiave di partizione e la chiave di riga per una nuova entità](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-entity.png)
  
    È ora possibile aggiungere altre entità alla tabella, modificare le entità o eseguire query sui dati in Esplora dati. Esplora dati è anche lo strumento in cui è possibile ridimensionare la velocità effettiva e aggiungere stored procedure, funzioni definite dall'utente e trigger alla tabella.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

A questo punto è possibile clonare un'app Table da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio git bash, ed eseguire il comando `cd` per passare a una cartella in cui installare l'app di esempio. 

    ```bash
    cd "C:\git-samples"
    ```

2. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Aprire quindi il file della soluzione TableStorage in Visual Studio. 

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. Questo consente all'app di comunicare con il database ospitato. 

1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **Stringa di connessione**. 

    Usare i pulsanti di copia sul lato destro della schermata per copiare la STRINGA DI CONNESSIONE PRIMARIA.

    ![Visualizzare e copiare la STRINGA DI CONNESSIONE PRIMARIA nel riquadro Stringa di connessione](./media/create-table-dotnet/connection-string.png)

2. In Visual Studio aprire il file App.config. 

3. Rimuovere il commento di StorageConnectionString alla riga 8 e impostare come commento StorageConnectionString alla riga 7, in quanto questa esercitazione non usa l'emulatore di archiviazione. Ora le righe 7 e 8 dovrebbero essere simili a quanto segue:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Incollare la STRINGA DI CONNESSIONE PRIMARIA dal portale nel valore StorageConnectionString alla riga 8. Incollare la stringa tra virgolette. 

    > [!IMPORTANT]
    > Se l'endpoint usa documents.azure.com, ovvero si dispone di un account di anteprima, è necessario creare un [nuovo account dell'API di tabella](#create-a-database-account) per lavorare con l'SDK dell'API di tabella disponibile a livello generale. 
    > 

    Ora la riga 8 dovrebbe essere simile a quanto segue:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Salvare il file App.config.

L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Compilare e distribuire l'app

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **TableStorage** in **Esplora soluzioni** e quindi scegliere **Gestisci pacchetti NuGet**. 

2. Nella casella **Sfoglia** di NuGet digitare *Microsoft.Azure.CosmosDB.Table*.

3. Dai risultati installare la libreria **Microsoft.Azure.CosmosDB.Table**. Viene installato il pacchetto dell'API Tabelle di Azure Cosmos DB, insieme a tutte le dipendenze.

4. Aprire BasicSamples.cs e aggiungere un punto di interruzione alla riga 30 e alla riga 52.

5. Premere CTRL+F5 per eseguire l'applicazione.

    La finestra della console mostra i dati della tabella aggiunti al nuovo database della tabella in Azure Cosmos DB. 
    
    Se si ottiene un errore di dipendenze, vedere [Risoluzione dei problemi](table-sdk-dotnet.md#troubleshooting).

    Quando si raggiunge il primo punto di interruzione, tornare a Esplora dati nel portale di Azure, espandere la tabella demo* e fare clic su **Entità**. La scheda **Entità** a destra illustra la nuova entità che è stata aggiunta: si noti che il numero di telefono per l'utente è 425-555-0101.
    
6. Chiudere la scheda Entità in Esplora dati.
    
7. Continuare a eseguire l'app fino al punto di interruzione successivo.

    Quando si raggiunge il punto di interruzione, passare al portale, fare clic di nuovo su Entità per aprire la scheda Entità e notare che il numero di telefono è stato aggiornato a 425-555-0105.

8. Dopo essere tornati nella finestra della console, premere CTRL+C per terminare l'esecuzione dell'app. 

    È ora possibile tornare in Esplora dati per aggiungere o modificare entità ed eseguire query sui dati.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare una tabella con Esplora dati e come eseguire un'app.  È ora possibile eseguire query sui dati tramite l'API di tabella.  

> [!div class="nextstepaction"]
> [Importare i dati delle tabelle nell'API Tabelle](table-import.md)

