---
title: Gestire le connessioni in funzioni di Azure
description: Informazioni su come evitare i problemi di prestazioni in Funzioni di Azure tramite i client con connessione statica.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784926"
---
# <a name="manage-connections-in-azure-functions"></a>Gestire le connessioni in funzioni di Azure

Le funzioni in un'app per le funzioni condividono le risorse. Tra le risorse condivise sono connessioni: Le connessioni HTTP, le connessioni al database e le connessioni ai servizi, ad esempio archiviazione di Azure. Quando si eseguono molte funzioni contemporaneamente, è possibile esaurire le connessioni disponibili. Questo articolo illustra come codificare le funzioni per evitare di usare più connessioni necessarie.

## <a name="connection-limit"></a>Limite connessione

Il numero di connessioni disponibili è limitato in parte perché un'app per le funzioni in esecuzione in un [ambiente sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Una delle restrizioni della sandbox impone nel codice dell'oggetto è un [limite sul numero di connessioni (attualmente di 600 connessioni attive e connessioni totali 1.200)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) per ogni istanza. Quando si raggiunge questo limite, il runtime di Funzioni crea un log con il messaggio seguente: `Host thresholds exceeded: Connections`.

Questo limite è per ogni istanza.  Quando la [controller di scalabilità aggiunge le istanze delle app di funzione](functions-scale.md#how-the-consumption-and-premium-plans-work) per gestire più richieste, ogni istanza ha un limite di connessione indipendente. Pertanto, non esiste un limite di connessione globali e si possono avere molto più di 600 connessioni attive tra tutte le istanze attive.

Per risolvere il problema, assicurarsi che sia stato abilitato Application Insights per app per le funzioni. Application Insights consente di visualizzare le metriche per le App per le funzioni, ad esempio le esecuzioni. Per altre informazioni, vedere [visualizzare i dati di telemetria in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights).  

## <a name="static-clients"></a>Client statici

Per evitare di mantenere più connessioni del necessario, riutilizzare le istanze di client, anziché crearne di nuove con ogni chiamata di funzione. Si consiglia di riutilizzare le connessioni client per qualsiasi linguaggio che si potrebbe scrivere una funzione. Ad esempio, i client .NET, ad esempio la [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), e i client di archiviazione di Azure possono gestire le connessioni se si usa un client singolo e statico.

Di seguito sono riportate alcune linee guida da seguire quando si usa un client specifico del servizio in un'applicazione di funzioni di Azure:

- *No* creare un nuovo client con ogni chiamata alla funzione.
- *Eseguire operazioni* creare un client singolo, statico che ogni chiamata di funzione può utilizzare.
- *Prendere in considerazione* creazione di un client singolo, statico in una classe helper condiviso se diverse funzioni utilizzano lo stesso servizio.

## <a name="client-code-examples"></a>Esempi di codice client

Questa sezione illustra le procedure consigliate per la creazione e l'utilizzo di client dal codice funzione.

### <a name="httpclient-example-c"></a>Esempio di HttpClient (C#)

Di seguito è riportato un esempio di C# funzione di codice che crea un valore statico [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) istanza:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

Una domanda comune sul [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) in .NET è "Dovrei dispose del mio cliente?" In generale, eliminare gli oggetti che implementano `IDisposable` termine usarle. Ma non eliminare un client statico perché non si è fatto utilizzarlo quando la funzione termina. La durata del client statico deve coincidere con quella dell'applicazione.

### <a name="http-agent-examples-javascript"></a>Esempi HTTP dell'agente (JavaScript)

È consigliabile usare la classe [`http.agent` ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) che fornisce opzioni di gestione della connessione migliori, anziché i metodi non nativi come il modulo `node-fetch`. I parametri di connessione configurati tramite le opzioni nel `http.agent` classe. Per opzioni dettagliate disponibili con l'agente HTTP, vedere [nuovo agente (\[opzioni\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options).

Globale `http.globalAgent` classe utilizzata da `http.request()` dispone di tutti di questi valori impostati per le rispettive impostazioni predefinite. Il modo consigliato per configurare i limiti di connessione in Funzioni è impostare un numero massimo globale. L'esempio seguente imposta il numero massimo di socket per l'app per la funzione:

```js
http.globalAgent.maxSockets = 200;
```

 L'esempio seguente crea una nuova richiesta HTTP con un agente HTTP personalizzato solo per tale richiesta:

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
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>Connessioni SqlClient

Il codice della funzione è possibile usare il Provider di dati .NET Framework per SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) per stabilire connessioni a un database relazionale di SQL. È anche il provider sottostante per i framework di dati che si basano su ADO.NET, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx). A differenza delle connessioni [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implementa pool di connessioni per impostazione predefinita. Ma poiché è ancora possibile esaurire le connessioni, è consigliabile ottimizzare le connessioni al database. Per altre informazioni, vedere [Pool di connessioni SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Alcuni framework di dati, ad esempio Entity Framework, in genere ottengono le stringhe di connessione dal **ConnectionStrings** sezione di un file di configurazione. In questo caso, è necessario aggiungere esplicitamente le stringhe di connessione di database SQL alla raccolta delle **stringhe di connessione** delle impostazioni dell'app per le funzioni e al file [local.settings.json](functions-run-local.md#local-settings-file) nel progetto locale. Se si crea un'istanza di [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice della funzione, è consigliabile archiviare il valore di stringa di connessione nel **le impostazioni dell'applicazione** con le altre connessioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui motivi per cui è consigliabile client statici, vedere [antipattern di creazione di istanze non corretta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Per altri suggerimenti per incrementare le prestazioni di Funzioni di Azure, vedere [Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure](functions-best-practices.md).
