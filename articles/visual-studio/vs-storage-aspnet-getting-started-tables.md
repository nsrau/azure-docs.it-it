---
title: Introduzione all'archiviazione tabelle di Azure e a Servizi connessi di Visual Studio (ASP.NET) | Documentazione Microsoft
description: Informazioni su come iniziare a usare il servizio di archiviazione tabelle in un progetto ASP.NET in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con Servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: kraigb
ms.openlocfilehash: 32a57e77bf6fe3cff88b9d6772ede9e6669ec75f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introduzione all'archiviazione tabelle di Azure e a Servizi connessi di Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica

L’archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.

Questa esercitazione illustra come scrivere codice ASP.NET per alcuni scenari comuni usando le entità di archiviazione tabelle di Azure. Questi scenari includono la creazione di una tabella, l'aggiunta, la creazione di query ed eliminazione di entità di tabella. 

##<a name="prerequisites"></a>prerequisiti

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Creare un controller MVC 

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Controller** e selezionare **Aggiungi > Controller** dal menu di scelta rapida.

    ![Aggiungere un controller a un'app MVC ASP.NET](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Nella finestra di dialogo **Aggiungi scaffolding** fare clic su **Controller MVC 5 - Vuoto** e selezionare **Aggiungi**.

    ![Specificare il tipo di controller MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Nella finestra di dialogo **Aggiungi controller** assegnare un nome al controller *TablesController* e selezionare **Aggiungi**.

    ![Assegnare un nome al controller MVC](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Aggiungere le direttive *using* seguenti al file `TablesController.cs`:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Creare una classe di modello

Molti esempi in questo articolo usano una classe derivata da **TableEntity**, denominata **CustomerEntity**. La procedura seguente consente di dichiarare questa classe come classe di modello:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Modelli** e selezionare **Aggiungi > Classe** dal menu di scelta rapida.

1. Nella finestra di dialogo **Aggiungi nuovo elemento** assegnare un nome alla classe, **CustomerEntity**.

1. Aprire il file `CustomerEntity.cs` e aggiungere le direttive **using** seguenti:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Modificare la classe in modo che, al termine, la classe venga dichiarata come illustrato nel codice seguente. La classe dichiara una classe di entità denominata **CustomerEntity** che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Creare una tabella

I passaggi seguenti illustrano come creare una tabella:

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment). 

1. Aprire il file `TablesController.cs` .

1. Aggiungere un metodo denominato **CreateTable** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **CreateTable** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;nome account di archiviazione>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto **CloudTable** che rappresenta un riferimento al nome della tabella desiderata. Il metodo **CloudTableClient.GetTableReference** non esegue una richiesta all'archiviazione tabelle. Il riferimento viene restituito indipendentemente dall'esistenza della tabella. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Chiamare il metodo **CloudTable.CreateIfNotExists** per creare la tabella se non esiste. Il metodo **CloudTable.CreateIfNotExists** restituisce **true** se la tabella non esiste e viene creata correttamente. In caso contrario, viene restituito **false**.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aggiornare **ViewBag** con il nome della tabella.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Tabelle** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **CreateTable** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `CreateTable.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Eseguire l'applicazione e selezionare **Create table** per visualizzare risultati simili allo screenshot seguente:
  
    ![Crea tabella](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Come accennato in precedenza, il metodo **CloudTable.CreateIfNotExists** restituisce **true** solo quando la tabella non esiste e viene creata. Se quindi si esegue l'app quando la tabella esiste, il metodo restituisce **false**. Per eseguire l'app più volte, è necessario eliminare la tabella prima di eseguire di nuovo l'app. L'eliminazione della tabella può essere eseguita tramite il metodo **CloudTable.Delete**. È possibile anche eliminare la tabella usando il [portale Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) o [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per eseguire il mapping di *entità* a oggetti C\#, viene usata una classe personalizzata derivata da **TableEntity**. Per aggiungere un'entità a una classe, creare una classe che definisca le proprietà dell'entità. Questa sezione illustra come definire una classe di entità che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La combinazione della chiave di riga e della chiave di partizione di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse, tuttavia l'uso di chiavi di partizione diverse assicura una maggiore scalabilità delle operazioni parallele. Tutte le proprietà da archiviare nel servizio tabelle devono essere proprietà pubbliche di un tipo supportato che espone entrambi i valori di impostazione e recupero.
La classe di entità *deve* dichiarare un costruttore pubblico senza parametri.

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment).

1. Aprire il file `TablesController.cs` .

1. Aggiungere la seguente direttiva in modo che il codice nel file `TablesController.cs` possa accedere alla classe **CustomerEntity**:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Aggiungere un metodo denominato **AddEntity** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **AddEntity** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;nome account di archiviazione>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto **CloudTable** che rappresenta un riferimento alla tabella in cui si sta aggiungendo la nuova entità. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Creare un'istanza e inizializzare la classe **CustomerEntity**.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Creare un oggetto **TableOperation** che inserisce l'entità cliente.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Eseguire l'operazione di inserimento chiamando il metodo **CloudTable.Execute**. È possibile verificare il risultato dell'operazione controllando la proprietà **TableResult.HttpStatusCode**. Il codice di stato 2xx indica che l'azione richiesta dal client è stata elaborata correttamente. Ad esempio, l'inserimento di nuove entità genera il codice di stato HTTP 204 indicante che l'operazione è stata elaborata correttamente e il server non ha restituito alcun contenuto.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aggiornare **ViewBag** con il nome della tabella e i risultati dell'operazione di inserimento.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Tabelle** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **AddEntity** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `AddEntity.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Eseguire l'applicazione e selezionare **Aggiungi entità** per visualizzare risultati simili allo screenshot seguente:
  
    ![Aggiungi entità](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    È possibile verificare che l'entità sia stata aggiunta seguendo i passaggi nella sezione [Ottenere una singola entità](#get-a-single-entity). È possibile anche usare [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) per visualizzare tutte le entità per le tabelle.

## <a name="add-a-batch-of-entities-to-a-table"></a>Aggiungere un batch di entità a una tabella

Oltre ad [aggiungere entità a una tabella una alla volta](#add-an-entity-to-a-table), è possibile aggiungere entità anche in batch. L'aggiunta di entità in batch riduce il numero di round trip tra il codice e il servizio tabelle di Azure. I passaggi seguenti illustrano come aggiungere più entità a una tabella con una singola operazione di inserimento:

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment).

1. Aprire il file `TablesController.cs` .

1. Aggiungere un metodo denominato **AddEntities** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **AddEntities** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;nome account di archiviazione>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto **CloudTable** che rappresenta un riferimento alla tabella in cui si sta aggiungendo le nuove entità. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Creare un'istanza di alcuni oggetti cliente basati sulla classe di modello **CustomerEntity** presentati nella sezione [Aggiungere un'entità a una tabella](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Ottenere un oggetto **TableBatchOperation**.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Aggiungere le entità all'oggetto di operazione di inserimento in batch.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Eseguire l'operazione di inserimento in batch chiamando il metodo **CloudTable.ExecuteBatch**.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Il metodo **CloudTable.ExecuteBatch** restituisce un elenco di oggetti **TableResult** in cui ogni oggetto **TableResult** può essere esaminato per determinare l'esito positivo o negativo di ogni singola operazione. Per questo esempio, passare l'elenco a una visualizzazione e mostrare i risultati di ogni operazione. 
 
    ```csharp
    return View(results);
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Tabelle** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **AddEntities** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `AddEntities.cshtml` e modificarlo in modo che l'aspetto sia simile al seguente.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Eseguire l'applicazione e selezionare **Aggiungi entità** per visualizzare risultati simili allo screenshot seguente:
  
    ![Aggiungi entità](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    È possibile verificare che l'entità sia stata aggiunta seguendo i passaggi nella sezione [Ottenere una singola entità](#get-a-single-entity). È possibile anche usare [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) per visualizzare tutte le entità per le tabelle.

## <a name="get-a-single-entity"></a>Ottenere una singola entità

Questa sezione illustra come ottenere una singola entità da una tabella usando la chiave di riga e la chiave di partizione dell'entità. 

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment) e che siano stati usati i dati in [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table). 

1. Aprire il file `TablesController.cs` .

1. Aggiungere un metodo denominato **GetSingle** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **ListBlobs** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;nome account di archiviazione>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto **CloudTable** che rappresenta un riferimento alla tabella da cui si stanno recuperando l'entità. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Creare un oggetto operazione di recupero che accetta un oggetto entità derivato da **TableEntity**. Il primo parametro è il *partitionKey* e il secondo è il *rowKey*. Usando la classe **CustomerEntity** e i dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table), il frammento di codice seguente esegue una query sulla tabella per un **CustomerEntity** il cui valore *partitionKey* sia "Smith" e il cui valore *rowKey* sia "Ben":

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Eseguire l'operazione di recupero.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Passare il risultato alla visualizzazione per mostrare i dati ottenuti.

    ```csharp
    return View(result);
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Tabelle** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **GetSingle** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `GetSingle.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Eseguire l'applicazione e selezionare **Get Single** per visualizzare risultati simili allo screenshot seguente:
  
    ![Get single](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione

Come accennato nella sezione [Aggiungere un'entità a una tabella](#add-an-entity-to-a-table), la combinazione di una partizione e una chiave di riga identifica in modo univoco un'entità in una tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse. Questa sezione illustra come eseguire query su una tabella per tutte le entità da una partizione specificata.  

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment) e che siano stati usati i dati in [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table). 

1. Aprire il file `TablesController.cs` .

1. Aggiungere un metodo denominato **GetPartition** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **GetPartition** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;nome account di archiviazione>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto **CloudTable** che rappresenta un riferimento alla tabella da cui si stanno recuperando le entità. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Creare un'istanza di un oggetto **TableQuery** specificando la query nella clausola **Where**. Usando la classe **CustomerEntity** e i dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table), il frammento di codice seguente esegue una query sulla tabella per tutte le entità in cui il valore **PartitionKey** (cognome del cliente) sia "Smith":

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. All'interno di un ciclo chiamare il metodo **CloudTable.ExecuteQuerySegmented** passando l'oggetto query istanziato nel passaggio precedente.  Il metodo **CloudTable.ExecuteQuerySegmented** restituisce un oggetto **TableContinuationToken** che, quando è **null**, indica che non ci sono più entità da recuperare. All'interno del ciclo usare un altro ciclo per eseguire un'iterazione sulle entità restituite. Nell'esempio di codice seguente ogni entità restituita viene aggiunta a un elenco. Al termine del ciclo, l'elenco viene passato a una visualizzazione per mostrare i dati ottenuti: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Tabelle** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **GetPartition** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `GetPartition.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Eseguire l'applicazione e selezionare **Get Partition** per visualizzare risultati simili allo screenshot seguente:
  
    ![Get Partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Eliminare un'entità

Questa sezione illustra come eliminare un'entità da una tabella.

> [!NOTE]
> 
> Questa sezione presuppone che siano stati completati i passaggi descritti in [Configurare l'ambiente di sviluppo](#set-up-the-development-environment) e che siano stati usati i dati in [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table). 

1. Aprire il file `TablesController.cs` .

1. Aggiungere un metodo denominato **DeleteEntity** che restituisce un **ActionResult**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Nel metodo **DeleteEntity** recuperare un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure: sostituire *&lt;nome account di archiviazione>* con il nome dell'account di archiviazione Azure a cui si sta accedendo.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto **CloudTableClient** che rappresenta un client del servizio tabelle.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto **CloudTable** che rappresenta un riferimento alla tabella da cui si sta eliminando l'entità. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Creare un oggetto operazione di eliminazione che accetta un oggetto entità derivato da **TableEntity**. In questo caso si usa la classe **CustomerEntity** e ai dati presentati nella sezione [Aggiungere un batch di entità a una tabella](#add-a-batch-of-entities-to-a-table). Il valore **ETag** dell'entità deve essere impostato su un valore valido.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Eseguire l'operazione di eliminazione.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Passare il risultato alla visualizzazione per mostrare i dati ottenuti.

    ```csharp
    return View(result);
    ```

1. In **Esplora soluzioni** espandere la cartella **Views**, fare clic con il pulsante destro del mouse su **Tabelle** e dal menu di scelta rapida selezionare **Aggiungi->Visualizzazione**.

1. Nella finestra di dialogo **Aggiungi visualizzazione** immettere **DeleteEntity** per il nome della visualizzazione e selezionare **Aggiungi**.

1. Aprire `DeleteEntity.cshtml` e modificarlo in modo che l'aspetto sia simile al frammento di codice seguente:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. In **Esplora soluzioni** espandere la cartella **Views->Shared** e aprire `_Layout.cshtml`.

1. Dopo l'ultimo **Html.ActionLink** aggiungere il seguente **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Eseguire l'applicazione e selezionare **Delete entity** per visualizzare risultati simili allo screenshot seguente:
  
    ![Get single](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.

  * [Introduzione all'Archiviazione BLOB di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Introduzione all'archiviazione code di Azure e ai Servizi connessi di Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)
