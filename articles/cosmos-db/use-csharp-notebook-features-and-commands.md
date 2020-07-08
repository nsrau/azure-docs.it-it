---
title: Usare i comandi e le funzionalità predefiniti dei notebook C# in Azure Cosmos DB (anteprima)
description: Informazioni su come usare i comandi e le funzionalità predefiniti per eseguire operazioni comuni con i notebook C# predefiniti di Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: tracking-python
ms.openlocfilehash: d9d48e825adeecd54375ce13c612d4a0c6eaaa18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263416"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Usare i comandi e le funzionalità predefiniti dei notebook C# in Azure Cosmos DB (anteprima)

I notebook Jupyter predefiniti in Azure Cosmos DB consentono di analizzare e visualizzare i dati dal portale di Azure. Questo articolo descrive come usare le funzionalità e i comandi predefiniti per eseguire operazioni comuni nei notebook C#.

## <a name="install-a-new-nuget-package"></a>Installare un nuovo pacchetto NuGet
Dopo aver abilitato il supporto per notebook per gli account Azure Cosmos, è possibile aprire un nuovo notebook e installare un pacchetto.

In una nuova cella di codice inserire ed eseguire il codice seguente, sostituendo ``PackageToBeInstalled`` con il pacchetto NuGet desiderato e ``optionalVersion`` con una versione specifica del pacchetto, se necessario. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

È possibile installare più pacchetti NuGet nella stessa cella. I pacchetti saranno disponibili per l'uso da qualsiasi notebook nell'area di lavoro dell'account Azure Cosmos. 

Al momento, l'area di lavoro dei notebook C# non supporta la risoluzione ricorsiva dei pacchetti NuGet. Se un pacchetto NuGet presenta dipendenze da altri pacchetti NuGet attualmente non installati, è necessario farvi riferimento in modo esplicito insieme al pacchetto padre.

> [!TIP]
> Se il notebook richiede un pacchetto personalizzato, è consigliabile aggiungervi una cella per installare il pacchetto e impostarlo come prima cella. In questo modo si riduce la possibilità di conflitti con altri pacchetti caricati da Azure Cosmos DB per impostazione predefinita. È anche facile reinstallare i pacchetti in caso di [reimpostazione dell'area di lavoro](#reset-notebooks-workspace), operazione che prevede la rimozione di tutti i pacchetti. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Usare l'SDK .NET di Azure Cosmos DB predefinito
La versione 3 dell'[SDK .NET di Azure Cosmos DB per l'API SQL](https://github.com/Azure/azure-cosmos-dotnet-v3) è installata e inclusa nell'ambiente di notebook per l'account Azure Cosmos.

Creare un'istanza di ``CosmosClient`` per eseguire le operazioni dell'SDK. 

Ad esempio:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Per altre informazioni, vedere gli esempi dell'[SDK .NET V3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> L'SDK .NET di Azure Cosmos DB predefinito è supportato solo per gli account API SQL (Core). Per le altre API, sarà necessario [installare il driver di .NET pertinente](#install-a-new-nuget-package) corrispondente all'API. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Impostare le opzioni personalizzate utilizzando ``CosmosClientOptions``
Per maggiore flessibilità, è possibile impostare la proprietà ``CosmosClientOptions`` personalizzata e passarla nell'istanza ``CosmosClient``. È possibile usare questa proprietà per:

- Impostare un nome di applicazione nel suffisso utente-agente per includerlo in ogni richiesta.
- Impostare l'area preferita da usare durante l'esecuzione di operazioni sul servizio.
- Impostare un criterio di ripetizione personalizzato per gestire le richieste con limitazione della frequenza.

Per tutte le impostazioni supportate, vedere l'articolo [di riferimento dell'API CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions). Di seguito è riportato un esempio che illustra come impostare la proprietà `cosmosClientOptions`:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Accedere alle variabili della chiave primaria e dell'endpoint dell'account
È possibile accedere alla chiave e all'endpoint predefiniti dell'account Azure Cosmos in cui è presente il notebook.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> Le variabili ``Cosmos.Key`` e ``Cosmos.Endpoint`` sono valide solo per l'API SQL. Per le altre API, trovare l'endpoint e la chiave nel pannello **Stringhe di connessione** o **Chiavi** nell'account Azure Cosmos.  

## <a name="print-console-output-in-c-code"></a>Stampare l'output della console nel codice C#
Nel codice C#, è possibile usare la sintassi Display.AsMarkdown() con [interpolazione di stringhe](/dotnet/csharp/language-reference/tokens/interpolated) per stampare l'output della console che verrà visualizzato quando si esegue la cella. 

Ad esempio: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> La sintassi Console.WriteLine() non è attualmente supportata nei notebook C#. Usare Display.AsMarkdown per stampare l'output della console dal programma. 

## <a name="use-built-in-nteract-data-explorer"></a>Usare lo strumento nteract data explorer predefinito
Per filtrare e visualizzare una raccolta di elementi, è possibile usare lo strumento [nteract data explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) predefinito. In una cella, inserire la variabile che si desidera visualizzare nell'ultima riga e che verrà visualizzata automaticamente in nteract quando si esegue la cella.

Nell'esempio *GetingStarted_Csharp.ipynb* è possibile stampare la variabile con il risultato, ovvero ``telemetryEvents``. Per la versione integrale dell'esempio, vedere il [notebook GettingStarted_Csharp.ipynb](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb). 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Cella query CSharp":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract data explorer":::

## <a name="use-built-in-dictionary-viewer"></a>Usare il visualizzatore del dizionario predefinito
È possibile usare il visualizzatore del dizionario predefinito per visualizzare una variabile. In una cella, inserire la variabile che si desidera visualizzare nell'ultima riga, e che verrà automaticamente visualizzata quando si esegue la cella.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Visualizzatore del dizionario predefinito":::

## <a name="upload-json-items-to-a-container"></a>Caricare gli elementi JSON in un contenitore
È possibile usare il comando magic ``%%upload`` per caricare dati da un file JSON in un contenitore Azure Cosmos specificato. Eseguire il comando seguente per caricare gli elementi:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Sostituire ``{database_id}`` e ``{container_id}`` con il nome del database e del contenitore nell'account Azure Cosmos. 
- Sostituire ``{url_location_of_file}`` con il percorso del file JSON. Il file deve essere una matrice di oggetti JSON validi e deve essere accessibile attraverso la rete Internet pubblica.

Ad esempio:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

Con le statistiche di output, è possibile calcolare il numero effettivo di UR/sec usate per caricare gli elementi. Se, ad esempio, sono state usate 25.000 UR in 38 secondi, il numero effettivo di UR/sec è 25.000 UR/38 secondi = 658 UR/sec.

## <a name="run-another-notebook-in-current-notebook"></a>Eseguire un altro notebook nel notebook corrente 
È possibile usare il comando magic ``%%run`` per eseguire un altro notebook nell'area di lavoro dal notebook corrente. Ecco la sintassi:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Sostituire ``{notebookName}`` con il nome del notebook da eseguire. Il notebook deve trovarsi nell'area di lavoro My Notebooks (Notebook personali) corrente. 

## <a name="reset-notebooks-workspace"></a>Reimpostare l'area di lavoro dei notebook
Per ripristinare le impostazioni predefinite dell'area di lavoro dei notebook, selezionare **Reimposta area di lavoro** sulla barra dei comandi. I pacchetti installati personalizzati verranno rimossi e il server Jupyter verrà riavviato. I notebook, i file e le risorse di Azure Cosmos non saranno interessati.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Reimpostare l'area di lavoro dei notebook":::

## <a name="next-steps"></a>Passaggi successivi

- Vedere le informazioni sui vantaggi dei [notebook Jupyter di Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
- Vedere le informazioni sull'[SDK .NET di Azure Cosmos DB per l'API SQL](https://github.com/Azure/azure-cosmos-dotnet-v3)
