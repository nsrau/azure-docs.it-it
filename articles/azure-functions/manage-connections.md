---
title: Gestire le connessioni in Funzioni di AzureManage connections in Azure Functions
description: Informazioni su come evitare i problemi di prestazioni in Funzioni di Azure tramite i client con connessione statica.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276452"
---
# <a name="manage-connections-in-azure-functions"></a>Gestire le connessioni in Funzioni di AzureManage connections in Azure Functions

Le funzioni in un'app per le funzioni condividono risorse. Tra queste risorse condivise sono presenti le connessioni: connessioni HTTP, connessioni di database e connessioni a servizi come Archiviazione di Azure.Among those shared resources are connections: HTTP connections, database connections, and connections to services such as Azure Storage. Quando si eseguono molte funzioni contemporaneamente, è possibile esaurire le connessioni disponibili. In questo articolo viene illustrato come codificare le funzioni per evitare di utilizzare più connessioni di cui hanno bisogno.

## <a name="connection-limit"></a>Limite di connessione

Il numero di connessioni disponibili è limitato in parte perché un'app per le funzioni viene eseguita in un [ambiente sandbox.](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) Una delle restrizioni che la sandbox impone al codice è un limite al numero di connessioni in uscita, che è attualmente 600 connessioni attive (1.200 totali) per istanza. Quando si raggiunge questo limite, il runtime delle `Host thresholds exceeded: Connections`funzioni scrive il seguente messaggio nei registri: . Per ulteriori informazioni, vedere i limiti del [servizio Funzioni](functions-scale.md#service-limits).

Questo limite è per istanza. Quando il controller di [scalabilità aggiunge istanze dell'app](functions-scale.md#how-the-consumption-and-premium-plans-work) per le funzioni per gestire più richieste, ogni istanza ha un limite di connessione indipendente. Ciò significa che non esiste un limite di connessione globale e che è possibile avere molto più di 600 connessioni attive in tutte le istanze attive.

Durante la risoluzione dei problemi, assicurarsi di aver abilitato Application Insights per l'app per le funzioni. Application Insights consente di visualizzare le metriche per le app per le funzioni come le esecuzioni. Per altre informazioni, vedere [Visualizzare i dati di telemetria in Application Insights.](functions-monitoring.md#view-telemetry-in-application-insights)  

## <a name="static-clients"></a>Client statici

Per evitare di mantenere più connessioni del necessario, riutilizzare le istanze di client, anziché crearne di nuove con ogni chiamata di funzione. È consigliabile riutilizzare le connessioni client per qualsiasi linguaggio in cui è possibile scrivere la funzione. Ad esempio, i client .NET come i client [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)e Archiviazione di Azure possono gestire le connessioni se si utilizza un singolo client statico.

Ecco alcune linee guida da seguire quando si usa un client specifico del servizio in un'applicazione Funzioni di Azure:Here are some guidelines to follow when you're using a service-specific client in an Azure Functions application:

- *Non* creare un nuovo client con ogni chiamata di funzione.
- *Creare* un singolo client statico che può essere utilizzato da ogni chiamata di funzione.
- *Prendi in considerazione la* creazione di un singolo client statico in una classe helper condivisa se funzioni diverse usano lo stesso servizio.

## <a name="client-code-examples"></a>Esempi di codice client

Questa sezione illustra le procedure consigliate per la creazione e l'utilizzo di client dal codice funzione.

### <a name="httpclient-example-c"></a>Esempio di HttpClient (C#)

Di seguito è riportato un esempio di codice della funzione di C, che crea un'istanza Statica di HttpClient:Here's an example of C' function code that creates a static [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) instance:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Una domanda comune su [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET è "Devo eliminare il mio client?" In generale, si eliminano `IDisposable` gli oggetti che implementano al termine dell'utilizzo. Ma non si elimina un client statico perché non si è fatto di usarlo quando la funzione termina. La durata del client statico deve coincidere con quella dell'applicazione.

### <a name="http-agent-examples-javascript"></a>Esempi di agenti HTTP (JavaScript)HTTP agent examples (JavaScript)

Poiché fornisce migliori opzioni di gestione [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) delle connessioni, è consigliabile usare `node-fetch` la classe nativa anziché i metodi non nativi, ad esempio il modulo. I parametri di connessione `http.agent` vengono configurati tramite le opzioni della classe. Per le opzioni dettagliate disponibili con l'agente HTTP, vedere [new Agent(\[options\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

La `http.globalAgent` classe globale `http.request()` utilizzata da ha tutti questi valori impostati sui rispettivi valori predefiniti. Il modo consigliato per configurare i limiti di connessione in Funzioni è impostare un numero massimo globale. L'esempio seguente imposta il numero massimo di socket per l'app per la funzione:

```js
http.globalAgent.maxSockets = 200;
```

 Nell'esempio seguente viene creata una nuova richiesta HTTP con un agente HTTP personalizzato solo per tale richiesta:The following example creates a new HTTP request with a custom HTTP agent only for that request:

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>Esempio di codice DocumentClient (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) si connette a un'istanza di Azure Cosmos DB. La documentazione di Azure Cosmos DB consiglia di [usare un client di Azure Cosmos DB singleton per l'intera durata dell'applicazione](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). L'esempio seguente illustra un modello per eseguire questa operazione in una funzione:

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>Esempio di codice CosmosClient (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) si connette a un'istanza di Azure Cosmos DB. La documentazione di Azure Cosmos DB consiglia di [usare un client di Azure Cosmos DB singleton per l'intera durata dell'applicazione](../cosmos-db/performance-tips.md#sdk-usage). L'esempio seguente illustra un modello per eseguire questa operazione in una funzione:

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Connessioni SqlClient

Il codice della funzione può utilizzare il provider di dati .NET Framework per SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) per stabilire connessioni a un database relazionale SQL. Questo è anche il provider sottostante per i framework di dati che si basano su ADO.NET, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). A differenza delle connessioni [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implementa pool di connessioni per impostazione predefinita. Tuttavia, poiché è ancora possibile esaurire le connessioni, è consigliabile ottimizzare le connessioni al database. Per altre informazioni, vedere [Pool di connessioni SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Alcuni framework di dati, ad esempio Entity Framework, in genere ottengono stringhe di connessione dalla sezione **ConnectionStrings** di un file di configurazione. In questo caso, è necessario aggiungere esplicitamente le stringhe di connessione di database SQL alla raccolta delle **stringhe di connessione** delle impostazioni dell'app per le funzioni e al file [local.settings.json](functions-run-local.md#local-settings-file) nel progetto locale. Se si sta creando un'istanza di [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice della funzione, è necessario archiviare il valore della stringa di connessione in **Impostazioni applicazione** con le altre connessioni.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sui motivi per cui si consigliano client statici, vedere Antipattern di [creazione di istanze non corretto](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Per altri suggerimenti per incrementare le prestazioni di Funzioni di Azure, vedere [Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure](functions-best-practices.md).
