---
title: Come gestire le connessioni in Funzioni di Azure
description: Informazioni su come evitare i problemi di prestazioni in Funzioni di Azure tramite i client con connessione statica.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659326"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Come gestire le connessioni in Funzioni di Azure

Le funzioni in un'app per le funzioni condividono le risorse e tra tali risorse condivise sono presenti connessioni: connessioni HTTP, connessioni di database e connessioni a servizi di Azure come l'archiviazione. Quando si eseguono molte funzioni contemporaneamente, è possibile esaurire le connessioni disponibili. Questo articolo descrive come scrivere le funzioni in modo da evitare di usare più connessioni di quelle effettivamente necessarie.

## <a name="connections-limit"></a>Limite di connessioni

Il numero di connessioni disponibili è parzialmente limitato, perché un'app per le funzioni viene eseguita nell'[ambiente sandbox di Servizio app di Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Una delle restrizioni che l'ambiente sandbox impone al codice è un [limite per il numero di connessioni, attualmente 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). Quando si raggiunge questo limite, il runtime di Funzioni crea un log con il messaggio seguente: `Host thresholds exceeded: Connections`.

Le probabilità di superare il limite aumentano quando il [controller di scalabilità aggiunge istanze dell'app per le funzioni](functions-scale.md#how-the-consumption-plan-works). Ogni istanza dell'app per le funzioni può chiamare le funzioni più volte contemporaneamente e tutte queste funzioni usano connessioni incluse nel conteggio per il limite di 300.

## <a name="use-static-clients"></a>Usare client statici

Per evitare di mantenere più connessioni del necessario, riutilizzare le istanze di client, anziché crearne di nuove con ogni chiamata di funzione. Client .NET come `HttpClient`, `DocumentClient` e i client di Archiviazione di Azure possono gestire le connessioni se si usa un singolo client statico.

Di seguito sono riportate alcune linee guida da seguire quando si usa un client specifico del servizio in un'applicazione di Funzioni di Azure:

- **NON** creare un nuovo client con ogni chiamata di funzione.
- **CREARE** un singolo client statico che può essere usato da ogni chiamata di funzione.
- **VALUTARE** se creare un singolo client statico in una classe helper condivisa, se diverse funzioni usano lo stesso servizio.

## <a name="httpclient-code-example"></a>Esempio di codice HttpClient

Di seguito è riportato un esempio di codice di funzione che crea un valore `HttpClient` statico:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Una domanda comune in merito a `HttpClient` di .NET è: "devo eliminare il client?" In generale, gli oggetti che implementano `IDisposable` vengono eliminati quando non è più necessario usarli. Tuttavia, un client statico non deve essere eliminato, perché è ancora necessario al termine della funzione. La durata del client statico deve coincidere con quella dell'applicazione.

## <a name="documentclient-code-example"></a>Esempio di codice DocumentClient

`DocumentClient` si connette a un'istanza di Cosmos DB. La documentazione di Cosmos DB consiglia di [usare un client di Azure Cosmos DB singleton per la durata dell'applicazione](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage). Nell'esempio seguente viene illustrato un modello per questa operazione in una funzione.

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

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui motivi per cui i client statici sono consigliati, vedere [Antipattern di creazione di istanze non corretta](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Per altri suggerimenti per incrementare le prestazioni di Funzioni di Azure, vedere [Ottimizzare le prestazioni e l'affidabilità delle funzioni di Azure](functions-best-practices.md).