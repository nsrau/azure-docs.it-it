---
title: Eseguire l'aggiornamento ad Azure Search .NET SDK versione 5 - Ricerca di Azure
description: Eseguire la migrazione di codice ad Azure Search .NET SDK versione 5 da versioni precedenti. Informazioni sulle novità e sulle modifiche al codice necessarie.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 743ac433418386281acc58ad1deef06ee75e38d9
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316872"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Aggiornamento ad Azure Search .NET SDK versione 5
Se si usa la versione 4.0-preview o una versione precedente di [Azure Search .NET SDK](https://aka.ms/search-sdk), questo articolo include informazioni utili per aggiornare l'applicazione per l'uso della versione 5.

Per una procedura dettagliata più generale relativa all'SDK, inclusi gli esempi, vedere [Come usare Ricerca di Azure da un'applicazione .NET](search-howto-dotnet-sdk.md).

La versione 5 di Azure Search .NET SDK contiene alcune modifiche rispetto alle versioni precedenti, ma per lo più secondarie, quindi il codice potrà essere modificato facilmente. Per le istruzioni su come modificare il codice per usare la nuova versione dell'SDK, vedere [Passaggi per eseguire l'aggiornamento](#UpgradeSteps) .

> [!NOTE]
> Se si usa la versione 2.0-preview o precedente, è consigliabile eseguire prima l'aggiornamento alla versione 3 e quindi l'aggiornamento alla versione 5. Per informazioni, vedere [Aggiornamento a .NET SDK Ricerca di Azure versione 3](search-dotnet-sdk-migration.md).
>
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Novità della versione 5
La versione 5 di Azure Search .NET SDK ha come destinazione la versione più recente disponibile a livello generale dell'API REST di Ricerca di Azure, in particolare la versione 2017-11-11. Questo rende possibile usare le nuove funzionalità di Ricerca di Azure da un'applicazione .NET, incluse le seguenti:

* [Sinonimi](search-synonyms.md).
* Ora è possibile accedere a livello di programmazione agli avvisi nella cronologia di esecuzione dell'indicizzatore (per altre informazioni, vedere la proprietà `Warning` di `IndexerExecutionResult` nelle [informazioni di riferimento su .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)).
* Supporto per .NET Core 2.
* La nuova struttura del pacchetto supporta l'uso solo delle parti dell'SDK necessarie (per altre informazioni, vedere [Modifiche di rilievo nella versione 5](#ListOfChanges)).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Per prima cosa aggiornare il riferimento a NuGet per `Microsoft.Azure.Search` usando NuGet Package Manager Console o facendo clic con il pulsante destro del mouse sui riferimenti di progetto e scegliendo "Gestisci pacchetti NuGet" in Visual Studio.

Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A meno che non si usi una funzionalità in anteprima non inclusa nel nuovo SDK con disponibilità generale, la ricompilazione dovrebbe avvenire correttamente (n caso contrario, comunicarlo su [GitHub](https://github.com/azure/azure-sdk-for-net/issues)). In questo caso è possibile procedere.

Si noti che, a causa delle modifiche nella creazione del pacchetto di Azure Search .NET SDK, è necessario ricompilare l'applicazione per poter usare la versione 5. Queste modifiche sono descritte in dettaglio in [Modifiche di rilievo nella versione 5](#ListOfChanges).

È possibile che vengano visualizzati avvisi di compilazione aggiuntivi correlati a proprietà o metodi obsoleti. Gli avvisi indicheranno istruzioni sulle operazioni da eseguire al posto della funzionalità deprecata. Ad esempio, se l'applicazione usa il metodo `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType`, si dovrebbe ricevere un avviso che informa che questo comportamento è ora abilitato per impostazione predefinita, quindi non è più necessario chiamare questo metodo.

Dopo avere corretto gli avvisi di compilazione, è possibile apportare modifiche all'applicazione per sfruttare la nuova funzionalità, se si vuole. Le nuove funzionalità nell'SDK sono descritte in dettaglio in [Novità della versione 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Modifiche di rilievo nella versione 5
La modifica più sostanziale nella versione 5 è che l'assembly `Microsoft.Azure.Search` e il suo contenuto sono stati divisi in quattro assembly separati che vengono ora distribuiti come quattro pacchetti NuGet distinti:

 - `Microsoft.Azure.Search`: è un meta-pacchetto che include tutti gli altri pacchetti di Ricerca di Azure come dipendenze. Se si sta eseguendo l'aggiornamento da una versione precedente dell'SDK, il semplice aggiornamento del pacchetto e la ricompilazione dovrebbero essere sufficienti per iniziare a usare la nuova versione.
 - `Microsoft.Azure.Search.Data`: usare questo pacchetto se si sta sviluppando un'applicazione .NET con Ricerca di Azure ed è sufficiente eseguire una query o aggiornare i documenti negli indici. Se invece occorre anche creare o aggiornare gli indici, le mappe di sinonimi o altre risorse a livello di servizio, usare il pacchetto `Microsoft.Azure.Search`.
 - `Microsoft.Azure.Search.Service`: usare questo pacchetto se si sta sviluppando l'automazione in .NET per gestire gli indici di Ricerca di Azure, le mappe di sinonimi, gli indicizzatori, le origini dati o altre risorse a livello di servizio. Se invece occorre solo eseguire query o aggiornare i documenti negli indici, usare il pacchetto `Microsoft.Azure.Search.Data`. Nel caso in cui servano tutte le funzionalità di Ricerca di Azure, usare il pacchetto `Microsoft.Azure.Search`.
 - `Microsoft.Azure.Search.Common`: tipi comuni necessari per le librerie .NET di Ricerca di Azure. Non è necessario usare questo pacchetto direttamente nell'applicazione. Dovrà essere usato solo come una dipendenza.
 
Questa modifica causa un'interruzione a livello tecnico, dal momento che numerosi tipi sono stati spostati tra gli assembly. Ecco perché è necessario ricompilare l'applicazione per poter eseguire l'aggiornamento alla versione 5 dell'SDK.

La versione 5 include poche altre modifiche di rilievo che potrebbero richiedere modifiche al codice oltre alla ricompilazione dell'applicazione.

### <a name="removed-obsolete-members"></a>Rimuovere i membri obsoleti

È possibile che vengano visualizzati errori di compilazione correlati a proprietà o metodi contrassegnati come obsoleti nelle versioni precedenti e successivamente rimossi nella versione 5. Se si verificano tali errori, ecco come risolverli:

- Se in precedenza si usava il metodo `IndexingParametersExtensions.IndexStorageMetadataOnly`, usare invece `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)`.
- Se in precedenza si usava il metodo `IndexingParametersExtensions.SkipContent`, usare invece `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)`.

### <a name="removed-preview-features"></a>Funzionalità di anteprima rimosse

Se esegue l'aggiornamento dalla versione 4.0-preview alla versione 5, tenere presente che è stato rimosso il supporto dell'analisi delle matrici JSON e CSV per gli indicizzatori BLOB, perché queste funzionalità sono ancora in anteprima. In particolare, sono stati rimossi i metodi seguenti della classe `IndexingParametersExtensions`:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Se l'applicazione dipende in modo sostanziale da queste funzionalità, non sarà possibile completare l'aggiornamento alla versione 5 di Azure Search .NET SDK. È possibile continuare a usare la versione 4.0-preview. Tenere presente, tuttavia, che **non è consigliabile usare SDK in anteprima nelle applicazioni di produzione**. Le funzionalità di anteprima sono destinate esclusivamente alla valutazione e sono soggette a modifiche.

## <a name="conclusion"></a>Conclusioni
Per altri dettagli sull'uso di .NET SDK Ricerca di Azure, vedere le [Procedure .NET](search-howto-dotnet-sdk.md).

I commenti degli utenti sull'SDK saranno molto apprezzati. In caso di problemi, è possibile richiedere assistenza nel [forum MSDN su Ricerca di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Verificare di avere anteposto al titolo del problema il prefisso "[Ricerca di Azure]".

Grazie per avere usato Ricerca di Azure.
