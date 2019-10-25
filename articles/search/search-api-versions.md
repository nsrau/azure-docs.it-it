---
title: Gestione delle versioni delle API per .NET SDK e API REST
titleSuffix: Azure Cognitive Search
description: Criteri di versione per le API REST di Azure ricerca cognitiva e la libreria client in .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a976655d0f634c7026f008d64516a629947e90c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793067"
---
# <a name="api-versions-in-azure-cognitive-search"></a>Versioni API in Azure ricerca cognitiva

Azure ricerca cognitiva esegue regolarmente il rollup degli aggiornamenti delle funzionalità. Talvolta, ma non sempre, questi aggiornamenti richiedono una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, il team di ricerca cognitiva di Azure pubblica le nuove versioni solo quando necessario, poiché può comportare un certo impegno nell'aggiornamento del codice per l'uso di una nuova versione dell'API. Una nuova versione è necessaria solo se alcuni aspetti dell'API sono stati modificati in modo da interrompere la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

La stessa regola vale per gli aggiornamenti all'SDK. Azure ricerca cognitiva SDK segue le regole di [controllo delle versioni semantiche](https://semver.org/) , il che significa che la versione include tre parti: principale, minore e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK viene rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per gli aggiornamenti di funzionalità che non comportano interruzioni di compatibilità viene incrementata la versione secondaria, mentre in caso di correzione di bug viene incrementata solo la versione build.

> [!NOTE]
> L'istanza del servizio ricerca cognitiva di Azure supporta diverse versioni dell'API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

## <a name="snapshot-of-current-versions"></a>Panoramica delle versioni correnti
Di seguito è riportato uno snapshot delle versioni correnti di tutte le interfacce di programmazione per Azure ricerca cognitiva.


| Interfacce | Versione principale più recente | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Disponibile a livello generale, rilasciato il 2019 maggio |
| [Anteprima di .NET SDK](https://aka.ms/search-sdk-preview) |8,0-Anteprima |Anteprima, rilasciata il 2019 aprile |
| [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Disponibile a livello generale |
| [API REST del servizio 2019-05-06-Preview](search-api-preview.md) |2019-05-06-anteprima |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Disponibile a livello generale |
| [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponibile a livello generale |

Per le API REST, è necessario includere il parametro `api-version` a ogni chiamata. L'uso di `api-version` facilita l'identificazione di una versione specifica, ad esempio un'API di anteprima. L'esempio seguente illustra come viene specificato il parametro `api-version` :

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Benché ogni richiesta abbia un parametro `api-version`, è consigliabile usare la stessa versione per tutte le richieste API. Ciò vale soprattutto nel caso in cui nuove versioni API introducano attributi oppure operazioni non riconosciuti dalle versioni precedenti. La combinazione di diverse versioni API può avere conseguenze impreviste e deve essere evitata.
>
> L'API REST del servizio e l'API REST di gestione sono sottoposte al controllo delle versioni in modo indipendente tra loro. L'eventuale somiglianza del numero di versione è causale.

Le API disponibili a livello generale (o GA) possono essere utilizzate nell'ambiente di produzione e sono soggette a contratti di servizio di Azure. Le versioni di anteprima offrono funzionalità sperimentali che non sempre vengono migrate a una versione GA. **L'uso delle API di anteprima in applicazioni di produzione è altamente sconsigliato.**

## <a name="about-preview-and-generally-available-versions"></a>Informazioni sull'anteprima e sulle versioni disponibili a livello generale
Azure ricerca cognitiva sempre versioni preliminari delle funzionalità sperimentali tramite l'API REST, quindi tramite le versioni provvisorie di .NET SDK.

Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti sulla progettazione e l'implementazione di funzionalità. Per questo motivo le funzionalità di anteprima sono soggette a modifiche nel tempo, che talvolta possono interromperne la compatibilità con le versioni precedenti. Sono quindi diverse dalle funzionalità di una versione disponibile a livello generale (GA), che sono stabili e con molta probabilità rimangono invariate, fatta eccezione per piccoli miglioramenti e correzioni compatibili con le versioni precedenti. Inoltre, le funzionalità di anteprima non sempre si trasformano in una versione GA.

È quindi consigliabile evitare di scrivere codice di produzione con dipendenze da versioni di anteprima. Se si usa una versione di anteprima precedente, è consigliabile eseguire la migrazione alla versione GA.

Per .NET SDK: le linee guida per la migrazione del codice sono disponibili nell'articolo sull' [aggiornamento di .NET SDK](search-dotnet-sdk-migration-version-9.md).

La disponibilità a livello generale indica che Azure ricerca cognitiva è ora sotto il contratto di servizio (SLA). Il contratto di servizio è disponibile in [Azure ricerca cognitiva contratti di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
