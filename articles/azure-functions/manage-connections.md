---
title: Come gestire le connessioni in Funzioni di Azure
description: Informazioni su come evitare i problemi di prestazioni in Funzioni di Azure tramite i client con connessione statica.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: glenga
ms.openlocfilehash: 6a877bb7f21b129522b9ffeab22eb77d7a556d53
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44094800"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Come gestire le connessioni in Funzioni di Azure

Le funzioni in un'app per le funzioni condividono le risorse e tra tali risorse condivise sono presenti connessioni: connessioni HTTP, connessioni di database e connessioni a servizi di Azure come l'archiviazione. Quando si eseguono molte funzioni contemporaneamente, è possibile esaurire le connessioni disponibili. Questo articolo descrive come scrivere le funzioni in modo da evitare di usare più connessioni di quelle effettivamente necessarie.

## <a name="connections-limit"></a>Limite di connessioni

Il numero di connessioni disponibili è parzialmente limitato, perché un'app per le funzioni viene eseguita nell'[ambiente sandbox di Servizio app di Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Una delle restrizioni che l'ambiente sandbox impone al codice è un [limite per il numero di connessioni, attualmente 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Quando si raggiunge questo limite, il runtime di Funzioni crea un log con il messaggio seguente: `Host thresholds exceeded: Connections`.

La probabilità di superare il limite aumenta quando il [controller di scalabilità aggiunge istanze di app per le funzioni](functions-scale.md#how-the-consumption-plan-works) per gestire più richieste. Ogni istanza di app per le funzioni può eseguire più funzioni contemporaneamente e tutte queste usano connessioni che vengono conteggiate nel totale, fino a un massimo di 300.

## <a name="use-static-clients"></a>Usare client statici

Per evitare di mantenere più connessioni del necessario, riutilizzare le istanze di client, anziché crearne di nuove con ogni chiamata di funzione. I client .NET come [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) e i client di Archiviazione di Azure possono gestire le connessioni se si usa un singolo client statico.

Di seguito sono riportate alcune linee guida da seguire quando si usa un client specifico del servizio in un'applicazione di Funzioni di Azure:

- **NON** creare un nuovo client con ogni chiamata di funzione.
- **CREARE** un singolo client statico che può essere usato da ogni chiamata di funzione.
- **VALUTARE** se creare un singolo client statico in una classe helper condivisa, se diverse funzioni usano lo stesso servizio.

## <a name="httpclient-code-example"></a>Esempio di codice HttpClient

Di seguito è riportato un esempio di codice di funzione che crea un [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) statico:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Una domanda frequente in merito al client .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) riguarda l'opportunità di eliminare il client. In generale, gli oggetti che implementano `IDisposable` vengono eliminati quando non è più necessario usarli. Tuttavia, un client statico non deve essere eliminato, perché è ancora necessario al termine della funzione. La durata del client statico deve coincidere con quella dell'applicazione.

## <a name="documentclient-code-example"></a>Esempio di codice DocumentClient

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

## <a name="sqlclient-connections"></a>Connessioni SqlClient

Il codice di funzione può usare il provider di dati .NET Framework per SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) per stabilire connessioni a un database relazionale di SQL. Questo è anche il provider sottostante per i framework di dati che si basano su ADO.NET, ad esempio Entity Framework. A differenza delle connessioni [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) e [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), ADO.NET implementa pool di connessioni per impostazione predefinita. Tuttavia, poiché è ancora possibile esaurire le connessioni, è consigliabile ottimizzare le connessioni al database. Per altre informazioni, vedere [Pool di connessioni SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Alcuni framework di dati, ad esempio [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), in genere ottengono le stringhe di connessione dalla sezione **ConnectionStrings** di un file di configurazione. In questo caso, è necessario aggiungere esplicitamente le stringhe di connessione di database SQL alla raccolta delle **stringhe di connessione** delle impostazioni dell'app per le funzioni e al file [local.settings.json](functions-run-local.md#local-settings-file) nel progetto locale. Se si crea una connessione [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) nel codice di funzione, è consigliabile archiviare il valore della stringa di connessione nelle **impostazioni dell'applicazione** con le altre connessioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui motivi per cui i client statici sono consigliati, vedere [Antipattern di creazione di istanze non corretta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Per altri suggerimenti per incrementare le prestazioni di Funzioni di Azure, vedere [Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure](functions-best-practices.md).
