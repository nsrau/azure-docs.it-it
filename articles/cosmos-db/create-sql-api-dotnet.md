---
title: Creare un'app Web .NET con Azure Cosmos DB usando l'API SQL
description: In questa guida introduttiva si useranno l'API SQL di Azure Cosmos DB e il portale di Azure per creare un'app Web .NET
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/15/2019
ms.openlocfilehash: 1ef414b2de2acbf5b92661c8b5f1e249549b14df
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259143"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-db-sql-api-account"></a>Guida introduttiva: Compilare un'app Web .NET usando un account API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (Preview)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questo argomento di avvio rapido illustra come creare un account dell'[API SQL](sql-api-introduction.md), un database di documenti e una raccolta di Azure Cosmos DB, nonché come aggiungere dati di esempio alla raccolta usando il portale di Azure. Verrà quindi compilata e distribuita un'app Web di elenco attività basata su [SQL .NET SDK](sql-api-sdk-dotnet.md) per aggiungere altri dati da gestire all'interno della raccolta. 

## <a name="prerequisites"></a>Prerequisiti

Se Visual Studio 2017 non è ancora installato, è possibile scaricare e usare la versione **gratuita** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-account"></a>Creare un account

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<a id="create-collection-database"></a>
## <a name="add-a-database-and-a-collection"></a>Aggiungere un database e una raccolta

È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database e una raccolta. 

1. Fare clic su **Esplora dati** > **Nuova raccolta**. 
    
    A destra verrà visualizzata l'area **Aggiungi raccolta**. Per vederla potrebbe essere necessario scorrere verso destra.

    ![Esplora dati nel portale di Azure, riquadro Aggiungi raccolta](./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png)

2. Nella pagina **Aggiungi raccolta** immettere le impostazioni per la nuova raccolta.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    **ID database**|ToDoList|Immettere *ToDoList* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/, \\, #, ?` o spazi finali.
    **ID raccolta**|Items|Immettere *Items* come nome della nuova raccolta. Gli ID delle raccolte prevedono gli stessi requisiti relativi ai caratteri dei nomi di database.
    **Chiave di partizione**| `<your_partition_key>`| Immettere una chiave di partizione. L'esempio descritto in questo articolo usa */category* come chiave di partizione.
    **Velocità effettiva**|400 UR|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento. 
    
    Oltre alle impostazioni precedenti, è possibile aggiungere **Chiavi univoche** per la raccolta. In questo esempio il campo viene lasciato vuoto. Le chiavi univoche consentono agli sviluppatori di aggiungere un livello di integrità dei dati nel database. Se si definiscono criteri di chiave univoca durante la creazione di una raccolta, si ha la sicurezza che uno o più valori siano univoci per ogni chiave di partizione. Per altre informazioni, vedere l'articolo [Chiavi univoche in Azure Cosmos DB](unique-keys.md).
    
    Fare clic su **OK**.

    In Esplora dati verranno visualizzati il nuovo database e la nuova raccolta.

    ![Esplora dati nel portale di Azure, con il nuovo database e la nuova raccolta](./media/create-sql-api-dotnet/azure-cosmos-db-new-collection.png)

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Aggiungere dati di esempio

È ora possibile aggiungere dati alla nuova raccolta usando Esplora dati.

1. In Esplora dati il nuovo database viene visualizzato nel riquadro Raccolte. Espandere il database **Tasks**, espandere la raccolta **Items**, fare clic su **Documenti** e quindi su **Nuovo documento**. 

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png)
  
2. Aggiungere ora un documento alla raccolta con la struttura seguente.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Dopo avere aggiunto il codice JSON alla scheda **Documenti**, fare clic su **Salva**.

    ![Copiare i dati JSON e fare clic su Salva in Esplora dati nel portale di Azure](./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png)

4. Creare e salvare un altro documento inserendo un valore univoco per la proprietà `id` e modificando le altre proprietà come si preferisce. I nuovi documenti possono avere la struttura desiderata, perché Azure Cosmos DB non impone alcuno schema per i dati.

## <a name="query-your-data"></a>Eseguire query sui dati

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Si può ora passare a usare il codice. Clonare un'[app per le API SQL da GitHub](https://github.com/Azure-Samples/documentdb-dotnet-todo-app), impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/documentdb-dotnet-todo-app.git
    ```

4. Aprire quindi il file della soluzione todo in Visual Studio. 

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). In questo argomento di avvio rapido vengono creati un database e una raccolta usando il portale di Azure e vengono aggiunti dati di esempio usando l'esempio di .NET. Tuttavia, è anche possibile creare il database e la raccolta usando l'esempio di .NET. 

Tutti i frammenti di codice seguenti sono tratti dal file DocumentDBRepository.cs.

* L'oggetto DocumentClient viene inizializzato come illustrato nel codice seguente:

    ```csharp
    client = new DocumentClient(new Uri(ConfigurationManager.AppSettings["endpoint"]), ConfigurationManager.AppSettings["authKey"]);
    ```

* Il nuovo database viene creato usando il metodo `CreateDatabaseAsync` come illustrato nel codice seguente:

    ```csharp
    await client.CreateDatabaseAsync(new Database { Id = DatabaseId });
    ```

* La nuova raccolta viene creata usando il metodo `CreateDocumentCollectionAsync` come illustrato nel codice seguente:

    ```csharp
    private static async Task CreateCollectionIfNotExistsAsync()
    {
        try
        {
           await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseId, CollectionId));
        }
        catch (DocumentClientException e)
        {
           if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
           {
              await client.CreateDocumentCollectionAsync(
              UriFactory.CreateDatabaseUri(DatabaseId),
              new DocumentCollection
              {
                  Id = CollectionId
              },
              new RequestOptions { OfferThroughput = 400 });
           }
           else
           {
             throw;
           }
        }
    }
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Chiavi** nel riquadro di spostamento a sinistra e quindi selezionare **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare l'URI e la chiave primaria nel file web.config nel passaggio seguente.

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-sql-api-dotnet/keys.png)

2. In Visual Studio 2017 aprire il file web.config. 

3. Copiare il valore di URI dal portale (usando il pulsante di copia) e impostarlo come valore della chiave di endpoint in web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Copiare quindi il valore di CHIAVE PRIMARIA dal portale e impostarlo come valore di authKey in web.config. 

    `<add key="authKey" value="FILLME" />`
    
5. Aggiornare quindi i valori di database e raccolta in base al nome del database creato in precedenza. L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

   ```csharp
   <add key="database" value="ToDoList"/>
   <add key="collection" value="Items"/>
   ```
 
## <a name="run-the-web-app"></a>Eseguire l'app Web
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliere **Gestisci pacchetti NuGet**. 

2. Nella casella **Sfoglia** di NuGet digitare *DocumentDB*.

3. Dai risultati installare la libreria **Microsoft.Azure.DocumentDB**. Viene installato il pacchetto Microsoft.Azure.DocumentDB, insieme a tutte le dipendenze.

4. Premere CTRL + F5 per eseguire l'applicazione. L'app viene visualizzata nel browser. 

5. Selezionare su **Crea nuovo** nel browser e creare alcune nuove attività nell'app elenco attività.

   ![App elenco attività con dati di esempio](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list.png)

È ora possibile tornare a Esplora dati e visualizzare, modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare una raccolta con Esplora dati e come eseguire un'app Web. È ora possibile importare dati aggiuntivi nell'account Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)


