---
title: Creare un&quot;applicazione .NET Azure Cosmos DB tramite l&quot;API di tabella | Microsoft Docs
description: Come iniziare a usare l&quot;API di tabella di Azure Cosmos DB tramite .NET
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 66327041-4d5e-4ce6-a394-fee107c18e59
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 9bbf188b0080b8b1cf71423023645f54f1f823e5
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017


---
# <a name="azure-cosmos-db-build-a-net-application-using-the-table-api"></a>Azure Cosmos DB: Creare un'applicazione .NET tramite l'API di tabella

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida di avvio rapido mostra come creare un account Azure Cosmos DB e come creare una tabella nell'account tramite il portale di Azure. Si scriverà quindi il codice per inserire, aggiornare ed eliminare entità e per eseguire alcune query. È possibile scaricare da NuGet [Azure Storage Preview SDK](https://aka.ms/premiumtablenuget), che include le stesse classi e firme di metodi disponibili nella versione pubblica di [Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), ma che permette anche di connettersi agli account Azure Cosmos DB tramite l'[API di tabella](table-introduction.md) (anteprima). 

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmosdb-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Aggiungere una tabella

[!INCLUDE [cosmosdb-create-table](../../includes/cosmosdb-create-table.md)]

## <a name="add-sample-data"></a>Aggiungere dati di esempio

È ora possibile aggiungere dati alla nuova tabella usando Esplora dati.

1. In Esplora dati espandere **sample-database**, **sample-table**, fare clic su **Entità** e quindi su **Aggiungi entità**.
2. Aggiungere ora i dati alle caselle dei valori di PartitionKey e RowKey e quindi fare clic su **Aggiunti entità**.

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/create-table-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
    È ora possibile aggiungere altre entità alla tabella, modificare le entità o eseguire query sui dati in Esplora dati. Esplora dati è anche lo strumento in cui è possibile ridimensionare la velocità effettiva e aggiungere stored procedure, funzioni definite dall'utente e trigger alla tabella.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API DocumentDB da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una directory di lavoro.  

2. Eseguire il comando seguente per clonare l'archivio di esempio. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-getting-started.git
    ```

3. Aprire quindi il file della soluzione in Visual Studio. 

## <a name="review-the-code"></a>Esaminare il codice

Ecco una breve analisi di ciò che accade nell'app. Aprire il file Program.cs. Come si noterà, queste righe di codice creano le risorse di Azure Cosmos DB. 

* Viene inizializzato CloudTableClient.

    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); 
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

* Se non esiste, viene creata una nuova tabella.

    ```csharp
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();
    ```

* Viene creato un nuovo contenitore di tabelle. Si noterà che questo codice è molto simile al normale SDK di archiviazione tabelle di Azure 

    ```csharp
    CustomerEntity item = new CustomerEntity()
                {
                    PartitionKey = Guid.NewGuid().ToString(),
                    RowKey = Guid.NewGuid().ToString(),
                    Email = $"{GetRandomString(6)}@contoso.com",
                    PhoneNumber = "425-555-0102",
                    Bio = GetRandomString(1000)
                };
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Azure Cosmos DB nel [portale di Azure](http://portal.azure.com/) fare clic su **Chiavi** nel riquadro di spostamento a sinistra e quindi su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare l'URI e la chiave primaria nel file app.config nel passaggio seguente.

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-documentdb-dotnet-core/keys.png)

2. In Visual Studio aprire il file app.config. 

3. Copiare il nome dell'account Azure Cosmos DB dal portale e impostarlo come valore di AccountName nel valore della stringa PremiumStorageConnection in app.config. Nello screenshot precedente il nome dell'account è cosmos-db-quickstart. Il nome dell'account viene visualizzato nella parte superiore del portale.

    `<add key="PremiumStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://COSMOSDB.documents.azure.com" />`

4. Copiare quindi il valore di CHIAVE PRIMARIA dal portale e impostarlo come valore di AccountKey in PremiumStorageConnectionString. 

    `AccountKey=AUTHKEY`

5. Copiare infine il valore di URI dalla pagina Chiavi del portale (usando il pulsante di copia) e impostarlo come valore di TableEndpoint di PremiumStorageConnectionString.

    `TableEndpoint=https://COSMOSDB.documents.azure.com`

    È possibile lasciare inalterato StandardStorageConnectionString.

L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

## <a name="run-the-web-app"></a>Eseguire l'app Web

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliere **Gestisci pacchetti NuGet**. 

2. Nella casella **Sfoglia** di NuGet digitare *WindowsAzure.Storage* e selezionare **Includi versione preliminare**. 

3. Dai risultati installare la libreria **WindowsAzure.Storage**. Viene installato il pacchetto dell'API di tabella di Azure Cosmos DB di anteprima, insieme a tutte le dipendenze.

4. Premere CTRL+F5 per eseguire l'applicazione.

    La finestra della console visualizza i dati aggiunti alla tabella. Al termine dello script, chiudere la finestra della console. 

È ora possibile tornare a Esplora dati e visualizzare, modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi: 

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare una tabella con Esplora dati e come eseguire un'app.  È ora possibile eseguire query sui dati tramite l'API di tabella.  

> [!div class="nextstepaction"]
> [Eseguire query tramite l'API di tabella](tutorial-query-table.md)


