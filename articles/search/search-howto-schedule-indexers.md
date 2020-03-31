---
title: Pianificare l'esecuzione dell'indicizzatore
titleSuffix: Azure Cognitive Search
description: Pianificare gli indicizzatori di Ricerca cognitiva di Azure per indicizzare il contenuto periodicamente o in momenti specifici.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112955"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Come pianificare gli indicizzatori in Ricerca cognitiva di AzureHow to schedule indexers in Azure Cognitive Search

Un indicizzatore viene in genere eseguito una volta, immediatamente dopo la creazione. È possibile eseguirlo nuovamente su richiesta usando il portale, l'API REST o .NET SDK. È inoltre possibile configurare un indicizzatore per l'esecuzione periodica in base a una pianificazione.

Alcune situazioni in cui la pianificazione dell'indicizzatore è utile:Some situations where indexer scheduling is useful:

* I dati di origine cambieranno nel tempo e si vuole che gli indicizzatori di Ricerca cognitiva di Azure elaborino automaticamente i dati modificati.
* L'indice verrà popolato da più origini dati e si desidera assicurarsi che gli indicizzatori vengano eseguiti in momenti diversi per ridurre i conflitti.
* I dati di origine sono molto grandi e si desidera distribuire l'elaborazione dell'indicizzatore nel tempo. Per altre informazioni sull'indicizzazione di grandi volumi di dati, vedere [Come indicizzare set](search-howto-large-index.md)di dati di grandi dimensioni in Ricerca cognitiva di Azure.For more information about indexing large volumes of data, see How to index large data sets in Azure Cognitive Search .

L'utilità di pianificazione è una funzionalità incorporata di Ricerca cognitiva di Azure.The scheduler is a built-in feature of Azure Cognitive Search. Non è possibile utilizzare un'utilità di pianificazione esterna per controllare gli indicizzatori di ricerca.

## <a name="define-schedule-properties"></a>Definire le proprietà dell'abaco

Una pianificazione dell'indicizzatore ha due proprietà:An indexer schedule has two properties:
* **Intervallo**, che definisce la quantità di tempo tra le esecuzioni pianificate dell'indicizzatore. L'intervallo più piccolo consentito è 5 minuti e il più grande è 24 ore.
* **Ora di inizio (UTC),** che indica la prima volta in cui deve essere eseguito l'indicizzatore.

È possibile specificare una pianificazione quando si crea l'indicizzatore per la prima volta o aggiornando le proprietà dell'indicizzatore in un secondo momento. Le pianificazioni dell'indicizzatore possono essere impostate tramite il [portale](#portal), l'API [REST](#restApi)o [.NET SDK.](#dotNetSdk)

È possibile effettuare solo l'esecuzione di un indicizzatore specificato per volta. Se un indicizzatore è già in esecuzione quando viene pianificata la successiva esecuzione, tale esecuzione viene posticipata fino alla successiva ora pianificata.

Ecco un esempio per rendere il discorso più concreto. Si supponga di configurare una pianificazione dell'indicizzatore con un intervallo di **orario** e un'ora di **inizio** del 1 giugno 2019 alle 8:00:00 AM UTC. Ecco cosa potrebbe accadere quando l'esecuzione di un indicizzatore richiede più di un'ora:Here's what could happen when an indexer run takes longer than an hour:

* La prima esecuzione dell'indicizzatore inizia intorno al 1 giugno 2019 alle 08:00 UTC. Si supponga che l'esecuzione richieda 20 minuti (o un tempo qualsiasi inferiore a 1 ora).
* La seconda esecuzione inizia intorno al 1 giugno 2019 9:00 AM UTC. Si supponga che l'esecuzione richiede 70 minuti - più di un'ora - e non verrà completata fino alle 10:10 AM UTC.
* La terza esecuzione è pianificata per iniziare alle 10:00 AM UTC, ma in quel momento l'esecuzione precedente è ancora in esecuzione. L'esecuzione pianificata viene quindi ignorata. La successiva esecuzione dell'indicizzatore non inizierà prima delle 11:00 AM UTC.

> [!NOTE]
> Se un indicizzatore è impostato su una determinata pianificazione ma ripetutamente non riesce sullo stesso documento più e più volte ogni volta che viene eseguito, l'indicizzatore inizierà l'esecuzione su un intervallo meno frequente (fino a un massimo di almeno una volta ogni 24 ore) fino a quando non progredisce correttamente di nuovo.  Se si ritiene di aver risolto il problema che causava il blocco dell'indicizzatore in un determinato punto, è possibile eseguire un'esecuzione su richiesta dell'indicizzatore e, se l'operazione viene eseguita correttamente, l'indicizzatore tornerà nuovamente all'intervallo di pianificazione impostato.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>Pianificazione nel portale

L'Importazione guidata dati nel portale consente di definire la pianificazione per un indicizzatore al momento della creazione. L'impostazione predefinita Pianificazione è **Oraria**, ovvero l'indicizzatore viene eseguito una volta dopo la creazione ed eseguito nuovamente ogni ora in seguito.

È possibile modificare l'impostazione Pianificazione su **Una volta** se non si desidera che l'indicizzatore venga eseguito di nuovo automaticamente o **su Giornaliero** per l'esecuzione una volta al giorno. Impostarla su **Personalizzato** se si desidera specificare un intervallo diverso o un'ora di inizio futura specifica.

Quando si imposta la pianificazione su **Personalizzato**, vengono visualizzati campi che consentono di specificare **l'intervallo** e l'ora di **inizio (UTC).** L'intervallo di tempo più breve consentito è 5 minuti e il più lungo è 1440 minuti (24 ore).

   ![Impostazione della pianificazione dell'indicizzatore nell'Importazione guidata datiSetting indexer schedule in Import Data wizard](media/search-howto-schedule-indexers/schedule-import-data.png "Impostazione della pianificazione dell'indicizzatore nell'Importazione guidata datiSetting indexer schedule in Import Data wizard")

Dopo aver creato un indicizzatore, è possibile modificare le impostazioni di pianificazione utilizzando il pannello Modifica dell'indicizzatore. I campi Pianificazione sono gli stessi dell'Importazione guidata dati.

   ![Impostazione della pianificazione nel pannello Modifica indicizzatore](media/search-howto-schedule-indexers/schedule-edit.png "Impostazione della pianificazione nel pannello Modifica indicizzatore")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>Pianificazione tramite le API REST

È possibile definire la pianificazione per un indicizzatore usando l'API REST. A tale scopo, includere la proprietà **schedule** durante la creazione o l'aggiornamento dell'indicizzatore. L'esempio seguente mostra una richiesta PUT per aggiornare un indicizzatore esistente:The example below shows a PUT request to update an existing indexer:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

È richiesto il parametro **interval** . L'intervallo fa riferimento al tempo tra l'inizio di due esecuzioni consecutive dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore.

Il **startTime** facoltativo indica quando devono iniziare le esecuzioni pianificate. Se viene omesso, verrà usata l'ora UTC corrente. Questo tempo può essere nel passato, nel qual caso la prima esecuzione è pianificata come se l'indicizzatore è stato eseguito in modo continuo dal **startTime**originale .

È inoltre possibile eseguire un indicizzatore su richiesta in qualsiasi momento utilizzando la chiamata Esegui indicizzatore. Per ulteriori informazioni sull'esecuzione di indicizzatori e sull'impostazione delle pianificazioni degli indicizzatori, vedere [Eseguire l'indicizzatore,](https://docs.microsoft.com/rest/api/searchservice/run-indexer) [Ottenere l'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/get-indexer)e [aggiornare l'indicizzatore](https://docs.microsoft.com/rest/api/searchservice/update-indexer) in Informazioni di riferimento sull'API REST.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>Pianificazione tramite .NET SDK

È possibile definire la pianificazione per un indicizzatore usando Azure Cognitive Search .NET SDK. A tale scopo, includere la proprietà **schedule** durante la creazione o l'aggiornamento di un indicizzatore.

Nell'esempio in Cè seguente viene creato un indicizzatore, usando un'origine dati e un indice predefiniti, e viene impostata la pianificazione per l'esecuzione una volta ogni giorno a partire da 30 minuti da ora:The following C' example creates an indexer, using a predefined data source and index, and sets its schedule to run once every day starting 30 minutes from now:

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
Se il parametro **schedule** viene omesso, l'indicizzatore verrà eseguito una sola volta immediatamente dopo la creazione.

Il parametro **startTime** può essere impostato su un'ora nel passato. In tal caso, la prima esecuzione viene pianificata come se l'indicizzatore fosse in esecuzione in modo continuo **dall'oggetto startTime**specificato.

La pianificazione viene definita utilizzando la classe [IndexingSchedule.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) Il **IndexingSchedule** costruttore richiede un **interval** parametro specificato utilizzando un **TimeSpan** oggetto. Il valore di intervallo più piccolo consentito è 5 minuti e il più grande è 24 ore. Il secondo parametro **startTime,** specificato come oggetto **DateTimeOffset,** è facoltativo.

.NET SDK consente di controllare le operazioni dell'indicizzatore utilizzando la classe [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) e la relativa proprietà [Indexers,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) che implementa i metodi dall'interfaccia **IIndexersOperations.** 

È possibile eseguire un indicizzatore su richiesta in qualsiasi momento utilizzando uno dei metodi [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)o [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) .

Per ulteriori informazioni sulla creazione, l'aggiornamento e l'esecuzione di indicizzatori, vedere [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
