---
title: Come pianificare gli indicizzatori-ricerca di Azure
description: Pianificare gli indicizzatori di ricerca di Azure per indicizzare il contenuto periodicamente o in momenti specifici.
ms.date: 05/31/2019
author: HeidiSteen
manager: HeidiSteen
ms.author: heidist
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.openlocfilehash: 245a2139aae0910ea1415811234667f2c06500ec
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855805"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Come pianificare gli indicizzatori per ricerca di Azure
Un indicizzatore viene in genere eseguito una volta, subito dopo la creazione. È possibile eseguirlo di nuovo su richiesta tramite il portale, l'API REST o .NET SDK. È anche possibile configurare un indicizzatore per l'esecuzione periodica in base a una pianificazione.

Di seguito sono riportate alcune situazioni in cui è utile pianificare l'indicizzatore:

* I dati di origine cambieranno nel tempo e si vuole che gli indicizzatori di ricerca di Azure elaborino automaticamente i dati modificati.
* L'indice verrà popolato da più origini dati e si desidera verificare che gli indicizzatori vengano eseguiti in momenti diversi per ridurre i conflitti.
* I dati di origine sono molto grandi e si vuole distribuire l'elaborazione dell'indicizzatore nel tempo. Per altre informazioni sull'indicizzazione di grandi volumi di dati, vedere [come indicizzare set di dati di grandi dimensioni in ricerca di Azure](search-howto-large-index.md).

L'utilità di pianificazione è una funzionalità predefinita di ricerca di Azure. Non è possibile usare un'utilità di pianificazione esterna per controllare gli indicizzatori di ricerca.

## <a name="define-schedule-properties"></a>Definire le proprietà della pianificazione

Una pianificazione dell'indicizzatore ha due proprietà:
* **Intervallo**, che definisce la quantità di tempo tra le esecuzioni pianificate dell'indicizzatore. L'intervallo minimo consentito è 5 minuti e il massimo è 24 ore.
* **Ora di inizio (UTC)** , che indica la prima volta in cui deve essere eseguito l'indicizzatore.

È possibile specificare una pianificazione quando si crea prima l'indicizzatore oppure aggiornando le proprietà dell'indicizzatore in un secondo momento. Le pianificazioni dell'indicizzatore possono essere impostate tramite il [portale](#portal), l' [API REST](#restApi)o [.NET SDK](#dotNetSdk).

È possibile effettuare solo l'esecuzione di un indicizzatore specificato per volta. Se un indicizzatore è già in esecuzione quando viene pianificata l'esecuzione successiva, l'esecuzione viene posticipata fino alla successiva ora pianificata.

Ecco un esempio per rendere il discorso più concreto. Si supponga di configurare una pianificazione dell'indicizzatore con un **intervallo** di ore e l' **ora di inizio** del 1 ° giugno 2019 alle 8:00:00 AM UTC. Ecco cosa può accadere quando l'esecuzione di un indicizzatore richiede più di un'ora:

* La prima esecuzione dell'indicizzatore inizia a partire dal 1 ° giugno 2019 alle 8:00 AM UTC. Si supponga che l'esecuzione richieda 20 minuti (o un tempo qualsiasi inferiore a 1 ora).
* La seconda esecuzione inizia a partire dal 1 ° giugno 2019 9:00 AM UTC. Si supponga che questa esecuzione riprenda 70 minuti (più di un'ora) e che non venga completata fino all'ora UTC 10:10.
* La terza esecuzione è pianificata per iniziare alle 10:00 AM UTC, ma in quel momento l'esecuzione precedente è ancora in esecuzione. L'esecuzione pianificata viene quindi ignorata. L'esecuzione successiva dell'indicizzatore non verrà avviata fino al 11:00 UTC.

> [!NOTE]
> Se un indicizzatore è impostato su una determinata pianificazione ma ripetutamente ha esito negativo sullo stesso documento più volte ogni volta che viene eseguito, l'indicizzatore inizierà a essere eseguito in un intervallo meno frequente (fino al massimo almeno una volta ogni 24 ore) finché non apporterà correttamente lo stato di avanzamento in.  Se si ritiene di aver risolto il problema che causava il blocco dell'indicizzatore in un determinato momento, è possibile eseguire un'esecuzione su richiesta dell'indicizzatore e, se l'operazione ha esito positivo, l'indicizzatore tornerà nuovamente all'intervallo di pianificazione impostato.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definire una pianificazione nel portale

La procedura guidata Importa dati nel portale consente di definire la pianificazione per un indicizzatore al momento della creazione. L'impostazione predefinita della pianificazione è ogni **ora**, ovvero l'indicizzatore viene eseguito una volta dopo la creazione e viene eseguito di nuovo ogni ora in seguito.

È possibile modificare l'impostazione di pianificazione su **una volta** se non si vuole che l'indicizzatore venga eseguito di nuovo automaticamente o su **ogni** giorno per eseguirlo una volta al giorno. Impostarla su **personalizzata** se si vuole specificare un intervallo diverso o un'ora di avvio futura specifica.

Quando si imposta la pianificazione su **personalizzata**, vengono visualizzati i campi che consentono di specificare l' **intervallo** e l' **ora di inizio (UTC)** . L'intervallo di tempo più breve consentito è 5 minuti e il più lungo è 1440 minuti (24 ore).

   ![Impostazione della pianificazione dell'indicizzatore nell'importazione guidata dati](media/search-howto-schedule-indexers/schedule-import-data.png "Impostazione della pianificazione dell'indicizzatore nell'importazione guidata dati")

Dopo aver creato un indicizzatore, è possibile modificare le impostazioni di pianificazione usando il pannello di modifica dell'indicizzatore. I campi della pianificazione sono identici a quelli della procedura guidata Importa dati.

   ![Impostazione della pianificazione nel pannello di modifica dell'indicizzatore](media/search-howto-schedule-indexers/schedule-edit.png "Impostazione della pianificazione nel pannello di modifica dell'indicizzatore")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definire una pianificazione tramite l'API REST

È possibile definire la pianificazione per un indicizzatore usando l'API REST. A tale scopo, includere la proprietà **Schedule** durante la creazione o l'aggiornamento dell'indicizzatore. Nell'esempio seguente viene illustrata una richiesta PUT per aggiornare un indicizzatore esistente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

È richiesto il parametro **interval** . L'intervallo fa riferimento al tempo tra l'inizio di due esecuzioni consecutive dell'indicizzatore. L'intervallo minimo consentito è di 5 minuti, quello massimo di un giorno. Il valore deve essere formattato come valore XSD "dayTimeDuration" (un subset limitato di un valore [duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Il modello è: `P(nD)(T(nH)(nM))`. Esempi: `PT15M` ogni 15 minuti, `PT2H` ogni due ore.

Il **StartTime** facoltativo indica quando devono iniziare le esecuzioni pianificate. Se viene omesso, verrà usata l'ora UTC corrente. Questa volta può essere nel passato, nel qual caso la prima esecuzione è pianificata come se l'indicizzatore venisse eseguito in modo continuativo rispetto al **StartTime**originale.

È anche possibile eseguire un indicizzatore su richiesta in qualsiasi momento usando la chiamata a Esegui indicizzatore. Per altre informazioni sull'esecuzione degli indicizzatori e sull'impostazione delle pianificazioni degli indicizzatori, vedere [eseguire](https://docs.microsoft.com/rest/api/searchservice/run-indexer)l'indicizzatore, [ottenere](https://docs.microsoft.com/rest/api/searchservice/get-indexer)un indicizzatore e [aggiornare](https://docs.microsoft.com/rest/api/searchservice/update-indexer) l'indicizzatore nelle informazioni di riferimento sull'API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definire una pianificazione con .NET SDK

È possibile definire la pianificazione per un indicizzatore usando Azure search .NET SDK. A tale scopo, includere la proprietà **Schedule** durante la creazione o l'aggiornamento di un indicizzatore.

Nell'esempio C# seguente viene creato un indicizzatore utilizzando un'origine dati e un indice predefiniti e viene impostata la pianificazione per l'esecuzione una volta al giorno a partire da 30 minuti da ora:

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
Se il parametro **Schedule** viene omesso, l'indicizzatore verrà eseguito una sola volta immediatamente dopo la creazione.

Il parametro **StartTime** può essere impostato su un'ora nel passato. In tal caso, la prima esecuzione viene pianificata come se l'indicizzatore venisse eseguito in modo continuativo dopo il **StartTime**specificato.

La pianificazione viene definita utilizzando la classe [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) . Il costruttore **IndexingSchedule** richiede un parametro **Interval** specificato utilizzando un oggetto **TimeSpan** . Il valore di intervallo minimo consentito è 5 minuti e il massimo è 24 ore. Il secondo parametro **StartTime** , specificato come oggetto **DateTimeOffset** , è facoltativo.

.NET SDK consente di controllare le operazioni dell'indicizzatore usando la classe [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) e la relativa proprietà [Indexers](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) , che implementa i metodi dell'interfaccia **IIndexersOperations** . 

È possibile eseguire un indicizzatore su richiesta in qualsiasi momento usando uno dei metodi [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)o [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) .

Per ulteriori informazioni sulla creazione, l'aggiornamento e l'esecuzione degli indicizzatori, vedere [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
