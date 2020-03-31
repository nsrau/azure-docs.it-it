---
title: Gestione delle versioni API per .NET e REST
titleSuffix: Azure Cognitive Search
description: Criteri di versione per le API REST di Ricerca cognitiva di Azure e la libreria client in .NET SDK.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137289"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API versions in Azure Cognitive Search

Ricerca cognitiva di Azure implementa regolarmente gli aggiornamenti delle funzionalità. Talvolta, ma non sempre, questi aggiornamenti richiedono una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, il team di Ricerca cognitiva di Azure pubblica nuove versioni solo quando necessario, poiché può richiedere un certo sforzo per aggiornare il codice per usare una nuova versione dell'API. Una nuova versione è necessaria solo se alcuni aspetti dell'API sono stati modificati in modo da interrompere la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

La stessa regola vale per gli aggiornamenti all'SDK. Azure Cognitive Search SDK segue le regole di controllo delle versioni [semantiche,](https://semver.org/) ovvero la versione è composta da tre parti: principale, secondaria e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK viene rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per gli aggiornamenti di funzionalità che non comportano interruzioni di compatibilità viene incrementata la versione secondaria, mentre in caso di correzione di bug viene incrementata solo la versione build.

> [!NOTE]
> L'istanza del servizio Ricerca cognitiva di Azure supporta diverse versioni dell'API REST, inclusa la più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

## <a name="snapshot-of-current-versions"></a>Panoramica delle versioni correnti
Di seguito è riportato uno snapshot delle versioni correnti di tutte le interfacce di programmazione in Ricerca cognitiva di Azure.Below is a snapshot of the current versions of all programming interfaces to Azure Cognitive Search.


| Interfacce | Versione principale più recente | Stato |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Generalmente disponibile, rilasciato maggio 2019 |
| [Anteprima di .NET SDK](https://aka.ms/search-sdk-preview) |8.0-anteprima |Preview, rilasciato ad aprile 2019 |
| [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Disponibile a livello generale |
| [API REST di servizio 2019-05-06-PreviewService REST API 2019-05-06-Preview](search-api-preview.md) |2019-05-06-Anteprima |Anteprima |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Disponibile a livello generale |
| [API REST di gestione ](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Disponibile a livello generale |

Per le API REST, è necessario includere il parametro `api-version` a ogni chiamata. L'uso di `api-version` facilita l'identificazione di una versione specifica, ad esempio un'API di anteprima. L'esempio seguente illustra come viene specificato il parametro `api-version` :

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Benché ogni richiesta abbia un parametro `api-version`, è consigliabile usare la stessa versione per tutte le richieste API. Ciò vale soprattutto nel caso in cui nuove versioni API introducano attributi oppure operazioni non riconosciuti dalle versioni precedenti. La combinazione di diverse versioni API può avere conseguenze impreviste e deve essere evitata.
>
> L'API REST del servizio e l'API REST di gestione sono sottoposte al controllo delle versioni in modo indipendente tra loro. L'eventuale somiglianza del numero di versione è causale.

Le API disponibili a livello generale (o GA) possono essere utilizzate nell'ambiente di produzione e sono soggette a contratti di servizio di Azure. Le versioni di anteprima offrono funzionalità sperimentali che non sempre vengono migrate a una versione GA. **L'uso delle API di anteprima in applicazioni di produzione è altamente sconsigliato.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Aggiornamento alla versione più recente dell'API REST entro il 15 ottobre 2020
Le versioni seguenti dell'API REST di Ricerca cognitiva di Azure verranno ritirate e non saranno più supportate a partire dal 15 ottobre 2020: **2014-07-31-Preview**, **2014-10-20-Preview**, **2015-02-28-Preview**e **2015-02-28**. Inoltre, anche le versioni di Ricerca cognitiva di Azure .NET SDK precedenti alla **3.0.0 RC** verranno ritirate poiché sono destinate a una di queste versioni dell'API REST. Dopo questa data, le applicazioni che usano una delle versioni deprecate dell'API REST o dell'SDK non funzioneranno più e devono essere aggiornate. Come per qualsiasi cambiamento di questo tipo, diamo 12 mesi di preavviso, in modo da avere il tempo sufficiente per regolare.  Per continuare a usare Ricerca cognitiva di Azure, eseguire la migrazione del codice esistente destinato [all'API REST](search-api-migration.md) alla [versione 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) o successiva dell'API ST .NET alla [versione 3.0](search-dotnet-sdk-migration.md) o successiva entro il 15 ottobre 2020.  Se avete domande sull'aggiornamento alla versione più azuresearch_contact@microsoft.com recente, si prega di inviare posta a entro il 15 maggio 2020 per assicurarsi di avere abbastanza tempo per aggiornare il codice.

## <a name="about-preview-and-generally-available-versions"></a>Informazioni sull'anteprima e sulle versioni disponibili a livello generale
Ricerca cognitiva di Azure rilascia sempre prima le funzionalità sperimentali tramite l'API REST prima, quindi tramite le versioni non definitive di .NET SDK.

Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti sulla progettazione e l'implementazione di funzionalità. Per questo motivo le funzionalità di anteprima sono soggette a modifiche nel tempo, che talvolta possono interromperne la compatibilità con le versioni precedenti. Sono quindi diverse dalle funzionalità di una versione disponibile a livello generale (GA), che sono stabili e con molta probabilità rimangono invariate, fatta eccezione per piccoli miglioramenti e correzioni compatibili con le versioni precedenti. Inoltre, le funzionalità di anteprima non sempre si trasformano in una versione GA.

È quindi consigliabile evitare di scrivere codice di produzione con dipendenze da versioni di anteprima. Se si usa una versione di anteprima precedente, è consigliabile eseguire la migrazione alla versione GA.

Per .NET SDK: le linee guida per la migrazione del codice sono disponibili nell'articolo sull' [aggiornamento di .NET SDK](search-dotnet-sdk-migration-version-9.md).

Disponibilità generale significa che Ricerca cognitiva di Azure è ora sotto il contratto di servizio (SLA). Il contratto di servizio è disponibile in Contratti di [livello servizio Ricerca cognitiva](https://azure.microsoft.com/support/legal/sla/search/v1_0/)di Azure.
