---
title: Gestione delle versioni dell'API per .NET SDK e API REST - Ricerca di Azure
description: Version policy for Azure Search REST APIs and the client library in the .NET SDK.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: c07a0c8f5440033455c69fe40806adf9b548c16f
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631155"
---
# <a name="api-versions-in-azure-search"></a>Versioni API in Ricerca di Azure
Il servizio Ricerca di Azure Search distribuisce regolarmente aggiornamenti delle funzionalità. Talvolta, ma non sempre, questi aggiornamenti richiedono una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, il team di Ricerca di Azure pubblica nuove versioni solo se necessario, perché ciò può richiedere l'aggiornamento del codice per l'uso di una nuova versione dell'API. Una nuova versione è necessaria solo se alcuni aspetti dell'API sono stati modificati in modo da interrompere la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

La stessa regola vale per gli aggiornamenti all'SDK. Azure Search SDK segue le regole di [versionamento semantico](https://semver.org/) , ciò significa che la versione ha tre parti: principale, secondaria e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK viene rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per gli aggiornamenti di funzionalità che non comportano interruzioni di compatibilità viene incrementata la versione secondaria, mentre in caso di correzione di bug viene incrementata solo la versione build.

> [!NOTE]
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

## <a name="snapshot-of-current-versions"></a>Panoramica delle versioni correnti
Di seguito è illustrata una panoramica delle versioni correnti di tutte le interfacce di programmazione di Ricerca di Azure.

| Interfacce | Versione principale più recente | Status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Disponibile a livello generale, rilasciata ad aprile 2018 |
| [Anteprima di .NET SDK](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Anteprima, rilasciata a maggio 2017 |
| [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Disponibile a livello generale |
| [API REST del servizio 2017-11-11-Preview](search-api-2017-11-11-preview.md) |2017-11-11-Preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Disponibile a livello generale |
| [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponibile a livello generale |

Per le API REST, è necessario includere il parametro `api-version` a ogni chiamata. L'uso di `api-version` facilita l'identificazione di una versione specifica, ad esempio un'API di anteprima. L'esempio seguente illustra come viene specificato il parametro `api-version` :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Benché ogni richiesta abbia un parametro `api-version`, è consigliabile usare la stessa versione per tutte le richieste API. Ciò vale soprattutto nel caso in cui nuove versioni API introducano attributi oppure operazioni non riconosciuti dalle versioni precedenti. La combinazione di diverse versioni API può avere conseguenze impreviste e deve essere evitata.
>
> L'API REST del servizio e l'API REST di gestione sono sottoposte al controllo delle versioni in modo indipendente tra loro. L'eventuale somiglianza del numero di versione è causale.

Le API disponibili a livello generale (o GA) possono essere utilizzate nell'ambiente di produzione e sono soggette a contratti di servizio di Azure. Le versioni di anteprima offrono funzionalità sperimentali che non sempre vengono migrate a una versione GA. **L'uso delle API di anteprima in applicazioni di produzione è altamente sconsigliato.**

## <a name="about-preview-and-generally-available-versions"></a>Informazioni sull'anteprima e sulle versioni disponibili a livello generale
Ricerca di Azure rilascia sempre in via preliminare funzionalità sperimentali tramite l'API REST, quindi tramite versioni non definitive di .NET SDK.

Le funzionalità di anteprima sono disponibili per i test e la sperimentazione allo scopo di raccogliere commenti e suggerimenti sulla progettazione e l'implementazione di funzionalità. Per questo motivo le funzionalità di anteprima sono soggette a modifiche nel tempo, che talvolta possono interromperne la compatibilità con le versioni precedenti. Sono quindi diverse dalle funzionalità di una versione disponibile a livello generale (GA), che sono stabili e con molta probabilità rimangono invariate, fatta eccezione per piccoli miglioramenti e correzioni compatibili con le versioni precedenti. Inoltre, le funzionalità di anteprima non sempre si trasformano in una versione GA.

È quindi consigliabile evitare di scrivere codice di produzione con dipendenze da versioni di anteprima. Se si usa una versione di anteprima precedente, è consigliabile eseguire la migrazione alla versione GA.

Per .NET SDK: le linee guida per la migrazione del codice sono disponibili nell'articolo sull'[aggiornamento di .NET SDK](search-dotnet-sdk-migration.md).

Con disponibilità a livello generale si intende che Ricerca di Azure è ora soggetta al contratto di servizio (SLA). Il contratto di servizio è reperibile in [Contratto di Servizio per Ricerca](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
