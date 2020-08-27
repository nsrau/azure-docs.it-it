---
title: Versioni dell'API
titleSuffix: Azure Cognitive Search
description: Criteri di versione per le API REST di Azure ricerca cognitiva e la libreria client in .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 73037ac3725ad1e7e9dd84597936c47406244630
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949882"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versioni API in Azure ricerca cognitiva

Azure ricerca cognitiva esegue regolarmente il rollup degli aggiornamenti delle funzionalità. Talvolta, ma non sempre, questi aggiornamenti richiedono una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, il team di ricerca cognitiva di Azure pubblica le nuove versioni solo quando necessario, poiché può comportare un certo impegno nell'aggiornamento del codice per l'uso di una nuova versione dell'API. Una nuova versione è necessaria solo se alcuni aspetti dell'API sono stati modificati in modo da interrompere la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

La stessa regola vale per gli aggiornamenti all'SDK. Azure ricerca cognitiva SDK segue le regole di [controllo delle versioni semantiche](https://semver.org/) , il che significa che la versione include tre parti: principale, minore e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK viene rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per gli aggiornamenti di funzionalità che non comportano interruzioni di compatibilità viene incrementata la versione secondaria, mentre in caso di correzione di bug viene incrementata solo la versione build.

> [!Important]
> Azure SDK per .NET, Java, Python e JavaScript implementa nuove librerie client per Azure ricerca cognitiva. Attualmente, nessuna delle librerie di Azure SDK supporta completamente le API REST di ricerca più recenti (2020-06-30) o le API REST di gestione (2020-03-13), ma ciò cambierà nel tempo. È possibile controllare periodicamente questa pagina o le [novità](whats-new.md) per gli annunci sui miglioramenti funzionali.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Versioni non supportate

Aggiornare le soluzioni di ricerca esistenti alla versione più recente dell'API REST entro il 15 ottobre 2020. In quel momento, le seguenti versioni dell'API REST di Azure ricerca cognitiva verranno ritirate e non sono più supportate:

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **Versione di anteprima del 31/07/2014**
+ **Versione di anteprima del 20/10/2014**

Inoltre, le versioni di Azure ricerca cognitiva .NET SDK precedenti a [**3.0.0-RC**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) verranno ritirate perché sono destinate a una di queste versioni dell'API REST. 

Dopo questa data, le applicazioni che usano una delle versioni dell'API REST deprecata o dell'SDK non funzioneranno più e dovranno essere aggiornate. Come per qualsiasi modifica di questo tipo, viene dato il preavviso di 12 mesi, quindi è necessario un tempo adeguato per la regolazione.

Per continuare a usare ricerca cognitiva di Azure, eseguire la migrazione del codice esistente destinato all' [API REST](search-api-migration.md) all' [api REST versione 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/) o a un SDK più recente entro il 15 ottobre 2020.  Per eventuali domande sull'aggiornamento alla versione più recente, inviare un messaggio di posta elettronica al azuresearch_contact@microsoft.com 15 maggio 2020 per assicurarsi di disporre di tempo sufficiente per l'aggiornamento del codice.

## <a name="rest-apis"></a>API REST

Un'istanza del servizio ricerca cognitiva di Azure supporta diverse versioni dell'API REST, inclusa quella più recente. È possibile continuare a usare una versione quando non è più recente, ma è consigliabile [eseguire la migrazione del codice](search-api-migration.md) per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

La tabella seguente illustra la cronologia delle versioni delle versioni correnti e rilasciate in precedenza dell'API REST di servizio di ricerca. La documentazione viene pubblicata solo per le versioni più recenti stabili e di anteprima.

### <a name="search-service-apis"></a>API servizio di ricerca

Creare e gestire il contenuto in un servizio di ricerca.

| Versione&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Stato | Problema di compatibilità con le versioni precedenti |
|-------------------------|--------|------------------------------|
| [Ricerca 2020-06-30](/rest/api/searchservice/index)| Stable | Versione stabile più recente delle API REST di ricerca con miglioramenti in merito alla valutazione e alla disponibilità generale per l'archivio delle informazioni.|
| [Ricerca 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Anteprima | Versione di anteprima associata alla versione stabile. Include più [funzionalità di anteprima](search-api-preview.md). |
| Ricerca 2019-05-06 | Stable  | Aggiunge [tipi complessi](search-howto-complex-data-types.md). |
| Ricerca 2019-05-06-Preview | Anteprima | Versione di anteprima associata alla versione stabile. |
| Cerca 2017-11-11 | Stable | Aggiunge skillsets e l' [arricchimento di intelligenza artificiale](cognitive-search-concept-intro.md). |
| Cerca 2017-11-11-Anteprima | Anteprima | Versione di anteprima associata alla versione stabile. |
| Cerca 2016-09-01 |Stable | Aggiunge gli [indicizzatori](search-indexer-overview.md). |
| Cerca 2016-09-01-anteprima | Anteprima | Versione di anteprima associata alla versione stabile.|
| Cerca 2015-02-28 | Non supportato dopo 10-10-2020   | Prima versione disponibile a livello generale.  |
| Cerca 2015-02-28-anteprima | Non supportato dopo 10-10-2020  | Versione di anteprima associata alla versione stabile. |
| Cerca 2014-10-20-anteprima | Non supportato dopo 10-10-2020 | Seconda anteprima pubblica. |
| Cerca 2014-07-31-anteprima | Non supportato dopo 10-10-2020  | Prima anteprima pubblica. |

### <a name="management-rest-apis"></a>API REST di gestione

Creare e configurare un servizio di ricerca e gestire le chiavi API.

| Versione&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Stato | Problema di compatibilità con le versioni precedenti |
|-------------------------|--------|------------------------------|
| [Gestione 2020-03-13](/rest/api/searchmanagement/) | Stable | Versione stabile più recente delle API REST di gestione, con miglioramenti in Endpoint Protection. Aggiunge un [endpoint privato](service-create-private-endpoint.md) tramite collegamento privato e [regole IP di rete](service-configure-firewall.md) per i nuovi servizi. |
| [Gestione 2019-10-01-Preview](/rest/api/searchmanagement/index-2019-10-01-preview) | Anteprima  | Nonostante il numero di versione, si tratta ancora della versione di anteprima corrente delle API REST di gestione. Al momento non sono disponibili funzionalità di anteprima. Tutte le funzionalità in anteprima sono state recentemente passate alla disponibilità generale. |
| Gestione 2015-08-19  | Stable | Prima versione disponibile a livello generale delle API REST di gestione. Fornisce il provisioning del servizio, la scalabilità verticale e la gestione delle chiavi API. |
| Gestione 2015-08-19-anteprima | Anteprima | Prima versione di anteprima delle API REST di gestione. |

## <a name="azure-sdk-for-net"></a>Azure SDK per .NET

Nella tabella seguente vengono forniti i collegamenti alle versioni più recenti di SDK. 

| Versione dell'SDK | Stato | Descrizione |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11,0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | Nuova libreria client da Azure .NET SDK, rilasciata il 2020 luglio. Imposta come destinazione l'API REST di ricerca API-Version = 2020-06-30 ma non supporta ancora, i filtri geografici o [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). |
| [Microsoft. Azure. search 10,0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Rilasciato il 2019 maggio. Targets The Search REST API-Version = 2019-05-06.|
| [Microsoft. Azure. search 8,0-Anteprima](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Anteprima | rilasciato il 2019 aprile. Ha come destinazione l'API REST di ricerca-Version = 2019-05-06-Preview.|
| [Microsoft. Azure. Management. Search 3.0.0](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | Targets The Management REST API-Version = 2015-08-19.  |

Per ulteriori informazioni sulle versioni precedenti di Microsoft. Azure. search, passare a una [pagina del pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Search/) per qualsiasi versione, scorrere fino alla **cronologia delle versioni**ed espandere la sezione per visualizzare l'elenco completo delle versioni.

## <a name="azure-sdk-for-java"></a>Azure SDK per Java

| Versione dell'SDK | Stato | Descrizione  |
|-------------|--------|------------------------------|
| [Java Azure-ricerca-documenti 11](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | Nuova libreria client da Azure .NET SDK, rilasciata il 2020 luglio. Targets The Search REST API-Version = 2019-05-06. |
| [1.35.0 client di gestione Java](/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | Targets The Management REST API-Version = 2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK per JavaScript

| Versione dell'SDK | Stato | Descrizione  |
|-------------|--------|------------------------------|
| [JavaScript Azure-ricerca 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Nuova libreria client da Azure .NET SDK, rilasciata il 2020 luglio. Imposta come destinazione l'API REST di ricerca-Version = 2016-09-01. |
| [JavaScript Azure-ARM-ricerca](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Targets The Management REST API-Version = 2015-08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK per Python

| Versione dell'SDK | Stato | Descrizione  |
|-------------|--------|------------------------------|
| [Python Azure-ricerca-documenti 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Nuova libreria client da Azure .NET SDK, rilasciata il 2020 luglio. Targets The Search REST API-Version = 2019-05-06. |
| [Python Azure-Mgmt-ricerca 1,0](/python/api/overview/azure/search?view=azure-python) | Stable | Targets The Management REST API-Version = 2015-08-19. |