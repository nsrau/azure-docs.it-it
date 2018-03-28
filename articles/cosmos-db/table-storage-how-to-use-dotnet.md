---
title: Introduzione all'archiviazione tabelle di Azure con .NET | Documentazione Microsoft
description: Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL.
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2018
ms.author: mimig
ms.openlocfilehash: d31dd1c267bcc63106616b191b6b79d5800e31e9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Introduzione all'archiviazione tabelle di Azure con .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

L'archiviazione tabelle di Azure è un servizio che archivia dati NoSQL strutturati nel cloud, mettendo a disposizione un archivio di chiavi/attributi senza schema. Poiché l'archiviazione tabelle è senza schema, è facile adattare i dati con il variare delle esigenze dell'applicazione. L'accesso ai dati dell'archiviazione tabelle è rapido ed economico per molti tipi di applicazioni e presenta costi generalmente più bassi rispetto alle soluzioni SQL tradizionali per volumi di dati simili.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi o altri tipi di metadati richiesti dal servizio. In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

### <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
Questa esercitazione illustra come usare la [libreria di tabelle Microsoft Azure CosmosDB per .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) all'interno di scenari comuni di archiviazione tabelle di Azure. Il nome del pacchetto indica che il pacchetto è destinato all'uso con Azure Cosmos DB. Il pacchetto tuttavia funziona sia con Azure Cosmos DB che con l'archiviazione tabelle di Azure, ma ogni servizio ha un endpoint univoco. Questi scenari vengono esaminati tramite esempi C# che illustrano come:
* Creare ed eliminare tabelle
* Inserire, aggiornare ed eliminare righe
* Eseguire query su tabelle

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Libreria comune di Archiviazione di Azure per .NET (anteprima)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). Questo è un pacchetto di anteprima obbligatorio supportato in ambienti di produzione. 
* [Libreria di tabelle Microsoft Azure CosmosDB per .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Account di archiviazione di Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Altri esempi
Per altri esempi di uso dell'archivio tabelle, vedere [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)(Introduzione all'archivio tabelle di Azure in .NET). È possibile scaricare l'applicazione di esempio ed eseguirla oppure esaminare il codice in GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Creare un account del servizio di Azure

È possibile usare tabelle con l'archiviazione tabelle di Azure o con Azure Cosmos DB. Per altre informazioni sulle differenze tra i servizi, vedere [Offerte per tabelle](table-introduction.md#table-offerings). Sarà necessario creare un account per il servizio che si intende usare. 

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure
Il modo più semplice per creare il primo account di archiviazione di Azure consiste nell'usare il [portale di Azure](https://portal.azure.com). Per altre informazioni, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account).

È anche possibile creare un account di archiviazione di Azure usando [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), l'[interfaccia della riga di comando di Azure](../storage/common/storage-azure-cli.md) o la [libreria client del provider di risorse di archiviazione per .NET](/dotnet/api/microsoft.azure.management.storage).

Se si preferisce non creare un account di archiviazione in questa fase, è anche possibile usare l'emulatore di archiviazione di Azure per eseguire e testare il codice in un ambiente locale. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Creare un account per l'API di tabella di Azure Cosmos DB

Per istruzioni per la creazione di un account per l'API di tabella di Azure Cosmos DB, vedere [Creare un account per un'API di tabella](create-table-dotnet.md#create-a-database-account).

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo
Configurare quindi l'ambiente di sviluppo in Visual Studio per poter provare gli esempi di codice di questa guida.

### <a name="create-a-windows-console-application-project"></a>Creare un progetto di applicazione console di Windows
In Visual Studio creare una nuova applicazione console di Windows. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2017. La procedura è simile per le altre versioni di Visual Studio.

1. Selezionare **File** > **Nuovo** > **Progetto**.
2. Selezionare **Installati** > **Visual C#** > **Desktop classico di Windows**.
3. Selezionare **App console (.NET Framework)**
4. Nel campo **Nome** immettere un nome per l'applicazione.
5. Selezionare **OK**.

Tutti gli esempi di codice in questa esercitazione possono essere aggiunti al metodo `Main()` del file `Program.cs` dell'applicazione console.

È possibile usare la libreria di tabelle di Azure CosmosDB in qualsiasi tipo di applicazione .NET, ad esempio un servizio cloud o un'app Web di Azure, nonché in applicazioni desktop e per dispositivi mobili. Per semplicità, in questa guida si usa un'applicazione console.

### <a name="use-nuget-to-install-the-required-packages"></a>Usare NuGet per installare i pacchetti necessari
Per completare questa esercitazione, all'interno del progetto è necessario fare riferimento a tre pacchetti consigliati:

* [Libreria comune di Archiviazione di Azure per .NET (anteprima)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common). 
* [Libreria di tabelle Microsoft Azure Cosmos DB per .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Questo pacchetto consente l'accesso a livello di codice alle risorse dati nell'account di archiviazione tabelle di Azure o nell'account dell'API di tabella di Azure Cosmos DB.
* [Libreria Gestione configurazione di Microsoft Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): questo pacchetto fornisce una classe per l'analisi di una stringa di connessione in un file di configurazione, indipendentemente dalla posizione in cui viene eseguita l'applicazione.

Per ottenere entrambi i pacchetti, è possibile usare NuGet. A tale scopo, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.
2. Cercare online "Microsoft.Azure.Storage.Common" e selezionare **Installa** per installare la libreria comune di Archiviazione di Azure per .NET (anteprima) e le relative dipendenze. Assicurarsi che la casella **Includi versione preliminare** sia selezionata, dato che si tratta di un pacchetto di anteprima.
3. Cercare online "Microsoft.Azure.CosmosDB.Table" e selezionare **Installa** per installare la libreria di tabelle Microsoft Azure CosmosDB.
4. Cercare online "WindowsAzure.ConfigurationManager" e selezionare **Installa** per installare la libreria di Microsoft Azure Configuration Manager.

> [!NOTE]
> Le dipendenze ODataLib nella libreria comune di archiviazione per .NET vengono risolte dai pacchetti ODataLib disponibili in NuGet, non da WCF Data Services. È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet. I pacchetti ODataLib specifici usati dalla libreria client di archiviazione sono [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) e [Spatial](http://nuget.org/packages/System.Spatial/). Queste librerie sono usate dalle classi di archiviazione tabelle di Azure, ma sono dipendenze necessarie per la programmazione con la libreria comune di archiviazione.
> 
> 

> [!TIP]
> Gli sviluppatori già esperti in archiviazione tabelle di Azure potrebbero avere già usato il pacchetto [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) in passato. È consigliabile che tutte le nuove applicazioni di tabella usino la [libreria comune di archiviazione di Azure](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) e la [libreria di tabelle di Azure Cosmos DB](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table), ma il pacchetto WindowsAzure.Storage è ancora supportato. Se si usa la libreria WindowsAzure.Storage, includere Microsoft.WindowsAzure.Storage.Table nelle istruzioni using.
>
>

### <a name="determine-your-target-environment"></a>Determinare l'ambiente di destinazione
Sono disponibili due opzioni relative all'ambiente per l'esecuzione degli esempi di questa guida:

* È possibile eseguire il codice con un account di archiviazione di Azure nel cloud. 
* È possibile eseguire il codice con un account di Azure Cosmos DB nel cloud.
* È possibile eseguire il codice nell'emulatore di archiviazione di Azure. L'emulatore di archiviazione è un ambiente locale che emula un account di archiviazione di Azure nel cloud. L'emulatore è un'opzione gratuita per il test e il debug del codice durante lo sviluppo dell'applicazione. L'emulatore usa un account e una chiave noti. Per altre informazioni, vedere [Usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

Se si specifica come destinazione un account di archiviazione nel cloud, immettere la chiave di accesso primaria per tale account tramite il portale di Azure. Per altre informazioni, vedere [Gestire le chiavi di accesso alle risorse di archiviazione](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> È possibile impostare come destinazione l'emulatore di archiviazione per evitare di incorrere negli eventuali costi associati al servizio Archiviazione di Azure. Se però si sceglie di impostare come destinazione un account di archiviazione di Azure nel cloud, i costi per eseguire questa esercitazione saranno minimi.
> 
> 

Se si specifica come destinazione un account di Azure Cosmos DB, copiare la chiave di accesso primaria per il proprio account dell'API di tabella dal portale di Azure. Per altre informazioni, vedere [Aggiornare la stringa di connessione](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Nella libreria comune di Archiviazione di Azure per .NET è possibile configurare gli endpoint e le credenziali per l'accesso ai servizi di archiviazione tramite una stringa di connessione di archiviazione. Per gestire nel modo migliore la stringa di connessione di archiviazione, usare un file di configurazione. 

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di archiviazione di Azure](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> La chiave dell'account è simile alla password radice per l'account di archiviazione. È consigliabile proteggere sempre la chiave dell'account di archiviazione. Evitare di distribuirla ad altri utenti, impostarla come hardcoded o salvarla in un file di testo normale accessibile ad altri. Rigenerare la chiave tramite il portale di Azure se si ritiene che possa essere stata compromessa.
> 
> 

Per configurare la stringa di connessione, aprire il file `app.config` da Esplora soluzioni in Visual Studio. Aggiungere il contenuto dell'elemento `<appSettings>` illustrato di seguito. Sostituire `account-name` con il nome dell'account e `account-key` con la chiave di accesso dell'account:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Se ad esempio si usa un account di Archiviazione di Azure, le impostazioni di configurazione sono simili a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Se si usa un account di Azure Cosmos DB, le impostazioni di configurazione sono simili a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Per impostare come destinazione l'emulatore di archiviazione, è possibile usare un collegamento con mapping al nome dell'account e alla chiave noti. In questo caso, l'impostazione della stringa di connessione è:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Aggiungere le direttive using
Aggiungere le direttive **using** seguenti all'inizio del file `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Creare il client del servizio tabelle
La classe [CloudTableClient][dotnet_CloudTableClient] consente di recuperare le tabelle e le entità archiviate nell'archiviazione tabelle. Di seguito è illustrato un modo per creare il client del servizio tabelle:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

A questo punto si è pronti a scrivere codice che legge e scrive i dati nell'archivio tabelle.

## <a name="create-a-table"></a>Creare una tabella
Questo esempio illustra come creare una tabella, se non esiste già:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per eseguire il mapping di entità a oggetti C# viene usata una classe personalizzata derivata da [TableEntity][dotnet_TableEntity]. Per aggiungere un'entità a una classe, creare una classe che definisca le proprietà dell'entità. Il codice seguente consente di definire una classe di entità che utilizza il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La partizione e la chiave di riga di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse, tuttavia l'uso di chiavi di partizione diverse assicura una maggiore scalabilità delle operazioni parallele. Le entità da archiviare nelle tabelle devono essere di un tipo supportato, ad esempio derivato dalla classe [TableEntity][dotnet_TableEntity]. Le proprietà dell'entità da archiviare in una tabella devono essere proprietà pubbliche del tipo e supportare sia l'ottenimento che l'impostazione di valori. Il tipo di entità *deve* inoltre esporre un costruttore senza parametri.

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

    public string PhoneNumber { get; set; }
}
```

Le operazioni su tabella che interessano le entità vengono eseguite tramite l'oggetto [CloudTable][dotnet_CloudTable] creato precedentemente nella sezione "Creare una tabella". L'operazione da eseguire è rappresentata da un oggetto [TableOperation][dotnet_TableOperation]. L'esempio di codice seguente illustra la creazione dell'oggetto [CloudTable][dotnet_CloudTable] e quindi di un oggetto **CustomerEntity**. Per preparare l'operazione, viene creato un oggetto [TableOperation][dotnet_TableOperation] per inserire l'entità customer nella tabella. L'operazione viene infine eseguita chiamando [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità
Per inserire un batch di entità in una tabella, è possibile usare un'unica operazione di scrittura. Di seguito sono riportate altre informazioni sulle operazioni batch:

* È possibile utilizzare una singola operazione batch per eseguire operazioni di aggiornamento, eliminazione e inserimento.
* Una singola operazione batch può includere fino a 100 entità.
* A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.
* È possibile eseguire una query come operazione batch, ma deve essere l'unica operazione del batch.

L'esempio di codice seguente consente di creare due oggetti entità e di aggiungere ciascuno a un oggetto [TableBatchOperation][dotnet_TableBatchOperation] usando il metodo [Insert][dotnet_TableBatchOperation_Insert]. Per eseguire l'operazione viene quindi chiamato [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch].

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the batch operation.
TableBatchOperation batchOperation = new TableBatchOperation();

// Create a customer entity and add it to the table.
CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
customer1.Email = "Jeff@contoso.com";
customer1.PhoneNumber = "425-555-0104";

// Create another customer entity and add it to the table.
CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
customer2.Email = "Ben@contoso.com";
customer2.PhoneNumber = "425-555-0102";

// Add both customer entities to the batch insert operation.
batchOperation.Insert(customer1);
batchOperation.Insert(customer2);

// Execute the batch operation.
table.ExecuteBatch(batchOperation);
```

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione
Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, usare un oggetto [TableQuery][dotnet_TableQuery]. Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

// Print the fields for each customer.
foreach (CustomerEntity entity in table.ExecuteQuery(query))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperare un intervallo di entità in una partizione
Se non si vuole eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo combinando il filtro della chiave di partizione con quello della chiave di riga. L'esempio di codice seguente usa due filtri per recuperare tutte le entità della partizione 'Smith' in cui la chiave di riga (nome) inizia con una lettera che precede la 'E' nell'alfabeto. Stampa quindi i risultati della query.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table query.
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

// Loop through the results, displaying information about the entity.
foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità
Per recuperare una singola entità specifica, è possibile scrivere una query. Il codice seguente usa [TableOperation][dotnet_TableOperation] per specificare il cliente 'Ben Smith'. Questo metodo restituisce una sola entità anziché una raccolta e il valore restituito in [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] è un oggetto **CustomerEntity**. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Sostituire un'entità
Per aggiornare un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nel servizio tabelle. Il codice seguente consente di modificare il numero di telefono di un cliente esistente. Invece di una chiamata a [Insert][dotnet_TableOperation_Insert], nel codice viene usato [Replace][dotnet_TableOperation_Replace]. Con [Replace][dotnet_TableOperation_Replace], l'entità viene completamente sostituita nel server, a meno che non sia stata modificata dopo essere stata recuperata. In questo caso, infatti, l'operazione non viene eseguita per impedire all'applicazione di sovrascrivere inavvertitamente una modifica effettuata tra il recupero e l'aggiornamento da parte di un altro componente dell'applicazione. Per risolvere questo errore, recuperare di nuovo l'entità, apportare le modifiche, se ancora valide, quindi eseguire un'altra operazione [Replace][dotnet_TableOperation_Replace]. La sezione successiva illustra come ovviare a questo comportamento.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity object.
CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

if (updateEntity != null)
{
    // Change the phone number.
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Inserire o sostituire un'entità
Le operazioni [Replace][dotnet_TableOperation_Replace] non vengono eseguite se l'entità è stata modificata dopo essere stata recuperata dal server. Per la corretta esecuzione dell'operazione [Replace][dotnet_TableOperation_Replace] è anche necessario recuperare prima l'entità dal server. In alcuni casi, tuttavia, non è noto se l'entità è già esistente nel server e i valori in essa archiviati sono irrilevanti, pertanto devono essere sovrascritti completamente dall'aggiornamento. A tale scopo è necessario usare un'operazione [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Questa operazione inserisce l'entità se non è già esistente oppure la sostituisce se esiste già, indipendentemente dalla data dell'ultimo aggiornamento.

Nell'esempio di codice seguente, viene creata e inserita nella tabella 'people' un'entità customer per 'Fred Jones'. Viene quindi usata l'operazione [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] per salvare nel server un'entità con la stessa chiave di partizione (Jones) e chiave di riga (Fred), questa volta con un valore diverso per la proprietà PhoneNumber. Dato che si usa [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], tutti i valori delle proprietà vengono sostituiti. Se tuttavia un'entità 'Fred Jones' non fosse già esistita nella tabella, sarebbe stata inserita.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità
Una query tabella consente di recuperare alcune proprietà da un'entità, ma non tutte. Questa tecnica, denominata proiezione, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. La query nel codice seguente restituisce solo gli indirizzi di posta elettronica di entità nella tabella. A tale scopo viene usata una query di [DynamicTableEntity][dotnet_DynamicTableEntity] e anche [EntityResolver][dotnet_EntityResolver]. Per altre informazioni sulla proiezione, vedere il post di blog [Introducing Upsert and Query Projection][blog_post_upsert] (Introduzione di Upsert e proiezione di query). La proiezione non è supportata dall'emulatore di archiviazione, quindi questo codice viene eseguito solo se si usa un account nel servizio tabelle.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Eliminare un'entità
Per eliminare facilmente un'entità dopo averla recuperata, è possibile usare lo stesso modello illustrato per aggiornare un'entità. Il codice seguente consente di recuperare ed eliminare un'entità customer.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a retrieve operation that expects a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the operation.
TableResult retrievedResult = table.Execute(retrieveOperation);

// Assign the result to a CustomerEntity.
CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

// Create the Delete TableOperation.
if (deleteEntity != null)
{
    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

    // Execute the operation.
    table.Execute(deleteOperation);

    Console.WriteLine("Entity deleted.");
}
else
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Eliminare una tabella
L'esempio di codice seguente consente infine di eliminare una tabella dall'account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperare entità nelle pagine in modo asincrono
Se si legge un numero elevato di entità e si desidera elaborare/visualizzare le entità man mano che vengono recuperate anziché attendere che vengano tutte restituite, è possibile recuperare le entità utilizzando una query segmentata. In questo esempio viene spiegato come restituire i risultati nelle pagine utilizzando il modello Async-Await, in modo che l'esecuzione non venga bloccata durante l'attesa della restituzione di un set di risultati di grandi dimensioni. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Programmazione asincrona con Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse:

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
* Per altri esempi di archivio tabelle, vedere [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio tabelle:
* [Informazioni di riferimento sulla libreria client di archiviazione per .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Informazioni di riferimento sulle API REST](http://msdn.microsoft.com/library/azure/dd179355)
* Per altre informazioni su come semplificare il codice scritto da usare con Archiviazione di Azure, vedere [Informazioni su Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
* [Introduzione all'archivio BLOB di Azure con .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) .
* Per archiviare i dati relazionali, vedere [Connettersi al database SQL tramite .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
