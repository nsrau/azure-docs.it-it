---
title: Gestire le connessioni in funzioni di Azure
description: Informazioni su come evitare i problemi di prestazioni in Funzioni di Azure tramite i client con connessione statica.
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226489"
---
# <a name="manage-connections-in-azure-functions"></a>Gestire le connessioni in funzioni di Azure

Le funzioni in un'app per le funzioni condividono le risorse. Tra queste risorse condivise si trovano le connessioni, ovvero le connessioni HTTP, le connessioni di database e le connessioni ai servizi, ad esempio archiviazione di Azure. Quando si eseguono molte funzioni contemporaneamente, è possibile esaurire le connessioni disponibili. Questo articolo illustra come codificare le funzioni per evitare di usare più connessioni del necessario.

## <a name="connection-limit"></a>Limite di connessione

Il numero di connessioni disponibili è limitato in parte perché un'app per le funzioni viene eseguita in un [ambiente sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Una delle restrizioni imposte dal sandbox sul codice è un limite al numero di connessioni in uscita, che è attualmente 600 connessioni attive (1.200 totali) per ogni istanza. Quando si raggiunge questo limite, il runtime di funzioni scrive il messaggio seguente nei log: `Host thresholds exceeded: Connections`. Per ulteriori informazioni, vedere la pagina relativa ai [limiti del servizio Functions](functions-scale.md#service-limits).

Questo limite è per ogni istanza. Quando il [controller di scalabilità aggiunge le istanze dell'app](functions-scale.md#how-the-consumption-and-premium-plans-work) per le funzioni per gestire più richieste, ogni istanza ha un limite di connessione indipendente. Ciò significa che non esiste alcun limite di connessione globale ed è possibile avere più di 600 connessioni attive tra tutte le istanze attive.

Per la risoluzione dei problemi, verificare di aver abilitato Application Insights per l'app per le funzioni. Application Insights consente di visualizzare le metriche per le app per le funzioni, ad esempio le esecuzioni. Per altre informazioni, vedere [visualizzare i dati di telemetria in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Client statici

Per evitare di mantenere più connessioni del necessario, riutilizzare le istanze di client, anziché crearne di nuove con ogni chiamata di funzione. Si consiglia di riutilizzare le connessioni client per qualsiasi lingua in cui è possibile scrivere la funzione. Ad esempio, i client .NET come [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)e i client di archiviazione di Azure possono gestire le connessioni se si usa un singolo client statico.

Di seguito sono riportate alcune linee guida da seguire quando si usa un client specifico del servizio in un'applicazione di funzioni di Azure:

- *Non* creare un nuovo client con ogni chiamata di funzione.
- *Creare un* singolo client statico che può essere utilizzato da ogni chiamata di funzione.
- Si *consiglia* di creare un singolo client statico in una classe helper condivisa se diverse funzioni usano lo stesso servizio.

## <a name="client-code-examples"></a>Esempi di codice client

Questa sezione illustra le procedure consigliate per la creazione e l'utilizzo di client dal codice funzione.

### <a name="httpclient-example-c"></a>Esempio di HttpClient (C#)

Di seguito è riportato un C# esempio di codice di funzione che consente di creare un'istanza [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) statica:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Una domanda comune su [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET è "è necessario eliminare il client?" In generale, si eliminano gli oggetti che implementano `IDisposable` al termine dell'utilizzo. Ma non si elimina un client statico perché l'operazione non viene eseguita al termine della funzione. La durata del client statico deve coincidere con quella dell'applicazione.

### <a name="http-agent-examples-javascript"></a>Esempi di agenti HTTP (JavaScript)

È consigliabile usare la classe [`http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) che fornisce opzioni di gestione della connessione migliori, anziché i metodi non nativi come il modulo `node-fetch`. I parametri di connessione vengono configurati tramite le opzioni della classe `http.agent`. Per le opzioni dettagliate disponibili con l'agente HTTP, vedere [New Agent (opzioni\[\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

La classe `http.globalAgent` globale utilizzata da `http.request()` dispone di tutti questi valori impostati sulle rispettive impostazioni predefinite. Il modo consigliato per configurare i limiti di connessione in Funzioni è impostare un numero massimo globale. L'esempio seguente imposta il numero massimo di socket per l'app per la funzione:

```js
http.globalAgent.maxSockets = 200;
```

 Nell'esempio seguente viene creata una nuova richiesta HTTP con un agente HTTP personalizzato solo per la richiesta:

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

Il codice di funzione può usare il provider di dati .NET Framework per SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) per stabilire connessioni a un database relazionale SQL. Si tratta anche del provider sottostante per i Framework di dati che si basano su ADO.NET, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). A differenza delle connessioni [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implementa pool di connessioni per impostazione predefinita. Tuttavia, poiché è comunque possibile esaurire le connessioni, è consigliabile ottimizzare le connessioni al database. Per altre informazioni, vedere [Pool di connessioni SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Alcuni framework di dati, ad esempio Entity Framework, ottengono in genere stringhe di connessione dalla sezione **connectionStrings** di un file di configurazione. In questo caso, è necessario aggiungere esplicitamente le stringhe di connessione di database SQL alla raccolta delle **stringhe di connessione** delle impostazioni dell'app per le funzioni e al file [local.settings.json](functions-run-local.md#local-settings-file) nel progetto locale. Se si sta creando un'istanza di [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice della funzione, è necessario archiviare il valore della stringa di connessione nelle **impostazioni dell'applicazione** con le altre connessioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui motivi per cui è consigliabile usare i client statici, vedere [antipattern di creazione di istanze non corretta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Per altri suggerimenti per incrementare le prestazioni di Funzioni di Azure, vedere [Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure](functions-best-practices.md).
