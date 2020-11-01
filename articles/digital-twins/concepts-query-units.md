---
title: Unità di query nei dispositivi gemelli digitali di Azure
titleSuffix: Azure Digital Twins
description: Informazioni sul concetto di fatturazione delle unità di query nei dispositivi gemelli digitali di Azure
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 33c572719d76a2add39aec37329679113fcddb76
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146331"
---
# <a name="query-units-in-azure-digital-twins"></a>Unità di query nei dispositivi gemelli digitali di Azure 

Un'unità di query di Azure Digital gemelli **(qu)** è un'unità di calcolo su richiesta usata per eseguire le [query dei dispositivi gemelli digitali di Azure](how-to-query-graph.md) usando l' [API di query](/rest/api/digital-twins/dataplane/query). 

Estrae le risorse di sistema, ad esempio CPU, IOPS e memoria, necessarie per eseguire operazioni di query supportate da dispositivi gemelli digitali di Azure, consentendo di tenere traccia dell'utilizzo nelle unità query.

La quantità di unità di query utilizzate per eseguire una query è interessata da...

* complessità della query
* dimensioni del set di risultati (pertanto una query che restituisce 10 risultati utilizzerà più QUs di una query con una complessità simile che restituisce solo un risultato)

Questo articolo illustra come comprendere le unità di query e tenere traccia dell'utilizzo delle unità query.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Trovare il consumo di unità di query nei dispositivi gemelli digitali di Azure

Quando si esegue una query con l' [API di query](/rest/api/digital-twins/dataplane/query)di Azure Digital gemelli, è possibile esaminare l'intestazione della risposta per tenere traccia del numero di QUs utilizzate dalla query. Cercare "query-charge" nella risposta restituita dai dispositivi gemelli digitali di Azure.

Gli [SDK](how-to-use-apis-sdks.md) di dispositivi gemelli digitali di Azure consentono di estrarre l'intestazione di caricamento query dalla risposta paginabile. In questa sezione viene illustrato come eseguire una query per i dispositivi gemelli digitali e come eseguire l'iterazione della risposta paginabile per estrarre l'intestazione di addebito query. 

Il frammento di codice seguente illustra come è possibile estrarre gli addebiti per le query che si verificano quando si chiama l'API di query. Scorre innanzitutto le pagine di risposta per accedere all'intestazione di addebito della query e quindi scorre i risultati dei dispositivi gemelli digitali all'interno di ogni pagina. 

```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins");
int pageNum = 0;

// The "await" keyword here is required, as a call is made when fetching a new page.

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages())
{
    Console.WriteLine($"Page {++pageNum} results:");

    // Extract the query-charge header from the page

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge))
    {
        Console.WriteLine($"Query charge was: {queryCharge}");
    }

    // Iterate over the twin instances.

    // The "await" keyword is not required here, as the paged response is local.

    foreach (string response in page.Values)
    {
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response);
        Console.WriteLine($"Found digital twin '{twin.Id}'");
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'esecuzione di query sui dispositivi gemelli digitali di Azure, vedere:

* [*Concetti: linguaggio di query*](concepts-query-language.md)
* [*Procedura: eseguire una query sul grafico gemello*](how-to-query-graph.md)
* [Documentazione di riferimento dell'API di query](/rest/api/digital-twins/dataplane/query/querytwins)

È possibile trovare i limiti correlati alle query di Azure Digital gemelli in [*riferimento: limiti del servizio*](reference-service-limits.md).