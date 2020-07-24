---
title: Eseguire l'aggiornamento ad Azure search .NET Management SDK
titleSuffix: Azure Cognitive Search
description: Eseguire l'aggiornamento ad Azure search .NET Management SDK da versioni precedenti. Informazioni sulle nuove funzionalità e sulle modifiche al codice necessarie per la migrazione.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 5f6a198445f9c9bd8e02cd8b6df3405431263e0b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076417"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Aggiornamento delle versioni di Azure search .NET Management SDK

Questo articolo illustra come eseguire la migrazione a versioni successive di Azure search .NET Management SDK, usato per effettuare il provisioning o il deprovisioning dei servizi di ricerca, modificare la capacità e gestire le chiavi API.

Gli SDK di gestione sono destinati a una versione specifica dell'API REST di gestione. Per ulteriori informazioni sui concetti e sulle operazioni, vedere [gestione ricerca (REST)](https://docs.microsoft.com/rest/api/searchmanagement/).

## <a name="versions"></a>Versioni

| Versione dell'SDK | Versione API REST corrispondente | Funzionalità di aggiunta o modifica del comportamento |
|-------------|--------------------------------|-------------------------------------|
| [3,0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/3.0.0) | API-Version = 2020-30-20 | Aggiunge la sicurezza degli endpoint (firewall IP e integrazione con [collegamento privato di Azure](../private-link/private-endpoint-overview.md)) |
| [2.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/2.0.0) | API-Version = 2019-10-01 | Miglioramenti dell’usabilità. Modifica di rilievo per le [chiavi di query di elenco](https://docs.microsoft.com/rest/api/searchmanagement/querykeys/listbysearchservice) (Get non è più disponibile). |
| [1,0](https://www.nuget.org/packages/Microsoft.Azure.Management.Search/1.0.1) | API-Version = 2015-08-19  | Prima versione |

## <a name="how-to-upgrade"></a>Come eseguire l'aggiornamento

1. Aggiornare il riferimento a NuGet per `Microsoft.Azure.Management.Search` usando la console di gestione pacchetti NuGet o facendo clic con il pulsante destro del mouse sui riferimenti del progetto e scegliendo "Gestisci pacchetti NuGet". in Visual Studio.

1. Una volta che NuGet ha scaricato i nuovi pacchetti e le relative dipendenze, ricompilare il progetto. A seconda del modo in cui il codice è strutturato, è possibile che venga ricompilato correttamente, nel qual caso l'operazione è stata completata.

1. Se la compilazione non riesce, è possibile che siano state implementate alcune interfacce SDK (ad esempio, a scopo di testing delle unità) che sono state modificate. Per risolvere questo problema, è necessario implementare metodi più recenti, ad esempio `BeginCreateOrUpdateWithHttpMessagesAsync` .

1. Dopo aver corretto gli errori di compilazione, è possibile apportare modifiche all'applicazione per sfruttare le nuove funzionalità. 

## <a name="upgrade-to-30"></a>Aggiornamento a 3,0

La versione 3,0 aggiunge la protezione degli endpoint privati limitando l'accesso agli intervalli IP e, facoltativamente, l'integrazione con il collegamento privato di Azure per i servizi di ricerca che non devono essere visibili sulla rete Internet pubblica.

### <a name="new-apis"></a>Nuove API

| API | Category| Dettagli |
|-----|--------|------------------|
| [NetworkRuleSet](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate#networkruleset) | Firewall IP | Consente di limitare l'accesso a un endpoint di servizio a un elenco di indirizzi IP consentiti. Vedere [configurare il firewall IP](service-configure-firewall.md) per i concetti e le istruzioni del portale. |
| [Risorsa collegamento privato condiviso](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources) | Collegamento privato | Creare una risorsa di collegamento privata condivisa che deve essere utilizzata da un servizio di ricerca.  |
| [Connessioni a endpoint privati](https://docs.microsoft.com/rest/api/searchmanagement/privateendpointconnections) | Collegamento privato | Definire e gestire le connessioni a un servizio di ricerca tramite endpoint privato. Vedere [creare un endpoint privato](service-create-private-endpoint.md) per i concetti e le istruzioni del portale.|
| [Risorse di collegamento privato](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/) | Collegamento privato | Per un servizio di ricerca che dispone di una connessione a un endpoint privato, ottenere un elenco di tutti i servizi usati nella stessa rete virtuale. Se la soluzione di ricerca include indicizzatori che effettuano il pull da origini dati di Azure (archiviazione di Azure, Cosmos DB, SQL di Azure) o usano servizi cognitivi o Key Vault, tutte queste risorse dovrebbero avere endpoint nella rete virtuale e questa API dovrebbe restituire un elenco. |
| [PublicNetworkAccess](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate#publicnetworkaccess)| Collegamento privato | Si tratta di una proprietà per la creazione o l'aggiornamento delle richieste di servizio. Se disabilitato, il collegamento privato è l'unica modalità di accesso. |

### <a name="breaking-changes"></a>Modifiche di rilievo

Non è più possibile usare GET in un [elenco](https://docs.microsoft.com/rest/api/searchmanagement/querykeys/listbysearchservice) di richieste di chiavi di query. Nelle versioni precedenti è possibile utilizzare GET o POST, in questa versione e in tutte le versioni in avanti, solo POST è supportato. 

## <a name="upgrade-to-20"></a>Aggiornamento a 2,0

La versione 2 di Azure search .NET Management SDK è un aggiornamento secondario, pertanto la modifica del codice dovrebbe richiedere solo un impegno minimo. Le modifiche apportate all'SDK sono esclusivamente modifiche sul lato client per migliorare l'usabilità dell'SDK. Queste modifiche includono, ad esempio:

* `Services.CreateOrUpdate` e le relative versioni asincrone eseguono ora automaticamente il polling del provisioning di `SearchService` e non restituiscono alcun risultato fino a quando non viene completato il provisioning del servizio. In questo modo non è necessario scrivere manualmente il codice di polling.

* Se si vuole comunque eseguire manualmente il polling del provisioning del servizio, è possibile usare il nuovo metodo `Services.BeginCreateOrUpdate` o una delle relative versioni asincrone.

* Sono stati aggiunti all'SDK nuovi metodi `Services.Update` e le relative versioni asincrone. Questi metodi usano HTTP PATCH per supportare l'aggiornamento incrementale di un servizio. È ora possibile, ad esempio, ridimensionare un servizio passando a questi metodi un'istanza `SearchService` contenente solo le proprietà `partitionCount` e `replicaCount` desiderate. Il metodo usato in precedenza per chiamare `Services.Get`, modificando il servizio `SearchService` restituito e passandolo a `Services.CreateOrUpdate`, è ancora supportato, ma non è più necessario. 

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi, il forum migliore per la pubblicazione di domande è [stack overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Se si trova un bug, è possibile registrare il problema nel [repository di GitHub su Azure .NET SDK](https://github.com/Azure/azure-sdk-for-net/issues). Assicurarsi di etichettare il titolo del problema con "[Search]".
