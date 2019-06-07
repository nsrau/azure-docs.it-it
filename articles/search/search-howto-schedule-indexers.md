---
title: Come pianificare gli indicizzatori - ricerca di Azure
description: Pianificare gli indicizzatori di ricerca di Azure per indicizzare il contenuto in momenti specifici o periodicamente.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755381"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Come pianificare gli indicizzatori per ricerca di Azure
L'indicizzatore viene eseguito una volta, in genere subito dopo averlo creato. È possibile eseguirlo nuovamente su richiesta tramite il portale, l'API REST o .NET SDK. È anche possibile configurare un indicizzatore si esegua periodicamente in base a una pianificazione.

Alcune situazioni in cui la pianificazione dell'indicizzatore risulta utile:

* Modifiche ai dati di origine nel tempo e si desidera che gli indicizzatori di ricerca di Azure per elaborare automaticamente i dati modificati.
* L'indice viene popolato da più origini dati e si desidera accertarsi che gli indicizzatori eseguiti in momenti diversi per ridurre i conflitti.
* L'origine dati è molto elevati e si desidera distribuire l'indicizzatore nel corso del tempo di elaborazione. Per altre informazioni sull'indicizzazione di grandi volumi di dati, vedere [come indicizzare grandi set di dati in ricerca di Azure](search-howto-large-index.md).

L'utilità di pianificazione è una funzionalità incorporata di ricerca di Azure. È possibile utilizzare un'utilità di pianificazione esterna per controllare gli indicizzatori di ricerca.

## <a name="define-schedule-properties"></a>Definire le proprietà di pianificazione

Una pianificazione dell'indicizzatore ha due proprietà:
* **Intervallo**, che definisce la quantità di tempo compreso tra pianificata esecuzioni dell'indicizzatore. L'intervallo più piccolo consentito è 5 minuti e la più grande è 24 ore.
* **Start Time (UTC)** , che indica la prima volta in cui è necessario eseguire l'indicizzatore.

Quando si crea innanzitutto l'indicizzatore o aggiornando le proprietà dell'indicizzatore in un secondo momento, è possibile specificare una pianificazione. Le pianificazioni di indicizzatore possono essere impostate utilizzando il [portale](#portal), il [API REST](#restApi), o la [.NET SDK](#dotNetSdk).

È possibile effettuare solo l'esecuzione di un indicizzatore specificato per volta. Se un indicizzatore è già in esecuzione quando viene pianificata l'esecuzione successiva, tale esecuzione viene rimandata fino al successivo orario pianificato.

Ecco un esempio per rendere il discorso più concreto. Si supponga che si configura una pianificazione dell'indicizzatore con un **Interval** di ogni ora e una **ora di inizio** del 1 giugno 2019 alle 8:00:00 AM UTC. Ecco cosa può accadere quando l'esecuzione dell'indicizzatore richiede più tempo rispetto a un'ora:

* La prima esecuzione dell'indicizzatore comincia il 1 giugno 2019 alle ore 8:00 UTC. Si supponga che l'esecuzione richieda 20 minuti (o un tempo qualsiasi inferiore a 1 ora).
* La seconda esecuzione comincia il 1 giugno 2019 9:00 UTC. Si supponga che l'esecuzione richieda 70 minuti - più di un'ora, e non verrà completata fino a 10:10 AM UTC.
* La terza esecuzione è pianificata per l'avvio al 10 12:00:00 AM UTC, ma in quel momento dell'esecuzione precedente è ancora in esecuzione. Ciò è pianificata l'esecuzione viene quindi ignorata. Alla successiva esecuzione dell'indicizzatore non verrà avviata fino a 11 12:00:00 AM UTC.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definire una pianificazione nel portale

L'importazione guidata dati nel portale è possibile definire la pianificazione per un indicizzatore in fase di creazione. L'impostazione di pianificazione predefinita è **oraria**, ovvero l'indicizzatore viene eseguito una volta dopo che viene creato e viene eseguito nuovamente ogni ora in un secondo momento.

È possibile modificare l'impostazione di pianificazione a **una volta** se non si desidera che l'indicizzatore per l'esecuzione anche in questo caso automatica, o a **giornaliero** per eseguire una volta al giorno. Impostarla su **Custom** se si desidera specificare un intervallo diverso o un'ora di avvio futuri specifica.

Quando si imposta la pianificazione **personalizzato**, i campi vengono visualizzati per consentire di specificare il **intervallo** e il **ora di inizio (UTC)** . Intervallo consentito è 5 minuti e da più tempo più breve tempo è 1440 minuti (24 ore).

   ![Pianificazione dell'indicizzatore di impostazione nella procedura guidata Importa dati](media/search-howto-schedule-indexers/schedule-import-data.png "pianificazione dell'indicizzatore di impostazione nella procedura guidata Importa dati")

Dopo aver creato un indicizzatore, è possibile modificare le impostazioni di pianificazione tramite Pannello di modifica dell'indicizzatore. I campi di pianificazione sono le stesse l'importazione guidata dati.

   ![Impostazione della pianificazione nel Pannello di modifica di un indicizzatore](media/search-howto-schedule-indexers/schedule-edit.png "impostazione della pianificazione nel pannello Modifica indicizzatore")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definire una pianificazione tramite l'API REST

È possibile definire la pianificazione per un indicizzatore usando l'API REST. A tale scopo, includere il **pianificazione** proprietà durante la creazione o aggiornamento dell'indicizzatore. L'esempio seguente mostra una richiesta PUT per aggiornare un indicizzatore esistente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

È richiesto il parametro **interval** . L'intervallo fa riferimento al tempo tra l'inizio di due esecuzioni consecutive dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore.

L'opzione facoltativa **startTime** indica quando devono iniziare esecuzioni pianificate. Se viene omesso, verrà usata l'ora UTC corrente. Può trattarsi di un'ora nel passato, caso in cui la prima esecuzione è pianificata come se l'indicizzatore fosse stato continuamente dall'originale **startTime**.

È anche possibile eseguire un indicizzatore su richiesta in qualsiasi momento usando la chiamata di esecuzione di un indicizzatore. Per altre informazioni sull'esecuzione di indicizzatori e l'impostazione di pianificazioni di indicizzatore, vedere [esecuzione di un indicizzatore](https://docs.microsoft.com/rest/api/searchservice/run-indexer), [Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer), e [aggiornamento dell'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/update-indexer) nel riferimento all'API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definire una pianificazione usando .NET SDK

È possibile definire la pianificazione per un indicizzatore tramite .NET SDK ricerca di Azure. A tale scopo, includere il **pianificazione** proprietà durante la creazione o l'aggiornamento di un indicizzatore.

Il seguente C# che viene creato un indicizzatore, con un'origine dati predefinita e l'indice e la pianificazione venga eseguita una volta al giorno 30 minuti a partire da ora e:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Se il **pianificazione** parametro viene omesso, l'indicizzatore verrà eseguito solo una volta immediatamente dopo averlo creato.

Il **startTime** parametro può essere impostato su un'ora nel passato. In tal caso, la prima esecuzione è pianificata come se l'indicizzatore fosse stato continuamente poiché il dato **startTime**.

La pianificazione viene definita mediante il [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) classe. Il **IndexingSchedule** costruttore richiede un' **intervallo** parametri specificato utilizzando una **TimeSpan** oggetto. Il valore di intervallo più piccolo consentito è 5 minuti e la più grande è 24 ore. La seconda **startTime** parametro, specificato come una **DateTimeOffset** oggetto, è facoltativo.

il SDK di .NET consente operazioni sull'indicizzatore controllo usando il [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) classe e i relativi [indicizzatori](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) proprietà, che implementa i metodi dal **IIndexersOperations**dell'interfaccia. 

È possibile eseguire un indicizzatore su richiesta in qualsiasi momento usando uno dei [eseguiti](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), o [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) metodi.

Per altre informazioni sulla creazione, aggiornamento e l'esecuzione di indicizzatori, vedere [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
