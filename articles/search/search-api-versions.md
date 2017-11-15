---
title: Versioni dell'API di Ricerca di Azure | Microsoft Azure | API di ricerca
description: Version policy for Azure Search REST APIs and the client library in the .NET SDK.
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 0458053a-164e-4682-a802-00097ecde981
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 4b4cb2d168676d52a90942a116905e6669b16639
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="api-versions-in-azure-search"></a>Versioni API in Ricerca di Azure
Il servizio Ricerca di Azure Search distribuisce regolarmente aggiornamenti delle funzionalità. A volte, ma non sempre, questi aggiornamenti richiedono la pubblicazione di una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, le nuove versioni vengono pubblicate solo se necessario, perché ciò può comportare alcune operazioni di aggiornamento del codice per l'utilizzo di una nuova versione dell'API. Verrà pubblicata una nuova versione solo in caso di modifica di alcuni aspetti dell'API che interrompe la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

Si applica la stessa regola per gli aggiornamenti all'SDK. Azure Search SDK segue le regole di [versionamento semantico](http://semver.org/) , ciò significa che la versione ha tre parti: principale, secondaria e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK verrà rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per aggiornamenti di funzionalità senza interruzione di compatibilità verrà incrementata la versione secondaria e, in caso di correzione di bug, verrà incrementata solo la versione build.

> [!NOTE]
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente. Quando si usa l'API REST, è necessario specificare la versione dell'API in tutte le richieste tramite il parametro api-version. Quando si usa .NET SDK, la versione del componente SDK in uso determina la versione corrispondente dell'API REST. Se si usa una versione del componente SDK precedente, è possibile continuare a eseguire il codice senza apportare modifiche, anche se il servizio viene aggiornato per supportare una versione API più recente.

## <a name="snapshot-of-current-versions"></a>Panoramica delle versioni correnti
Di seguito è illustrata una panoramica delle versioni correnti di tutte le interfacce di programmazione di Ricerca di Azure.

| Interfacce | Versione principale più recente | Stato |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |3.0 |Disponibile a livello generale, rilasciata a novembre 2016 |
| [Anteprima di .NET SDK](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Anteprima, rilasciata a maggio 2017 |
| [API REST del servizio](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Disponibile a livello generale |
| [Anteprima API REST del servizio](search-api-2016-09-01-preview.md) |2016-09-01-Preview |Preview |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2015-08-19 |Disponibile a livello generale |
| [API REST di gestione](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Disponibile a livello generale |

Per le API REST, è necessario includere il parametro `api-version` a ogni chiamata. Questo facilita l'identificazione di una versione specifica, ad esempio un'API di anteprima. L'esempio seguente illustra come viene specificato il parametro `api-version` :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> Benché ogni richiesta abbia un parametro `api-version`, è consigliabile usare la stessa versione per tutte le richieste API. Ciò vale soprattutto nel caso in cui nuove versioni API introducano attributi oppure operazioni non riconosciuti dalle versioni precedenti. La combinazione di diverse versioni API può avere conseguenze impreviste e deve essere evitata.
>
> L'API REST del servizio e l'API REST di gestione sono sottoposte al controllo delle versioni in modo indipendente tra loro. L'eventuale somiglianza del numero di versione è causale.

Le API disponibili a livello generale (o GA) possono essere utilizzate nell'ambiente di produzione e sono soggette a contratti di servizio di Azure. Le versioni di anteprima offrono funzionalità sperimentali che non sempre vengono migrate a una versione GA. **È consigliabile evitare l'utilizzo delle API di anteprima in applicazioni di produzione.**

## <a name="about-preview-and-generally-available-versions"></a>Informazioni sull'anteprima e sulle versioni disponibili a livello generale
Ricerca di Azure rilascia sempre in via preliminare funzionalità sperimentali tramite l'API REST, quindi tramite versioni non definitive di .NET SDK.

La migrazione alla versione GA delle funzionalità di anteprima non è garantita. Mentre le funzionalità in una versione GA vengono considerate stabili e difficilmente modificabili, ad eccezione di piccoli miglioramenti e correzioni compatibili con le versioni precedenti, le funzionalità di anteprima sono disponibili per il testing e la sperimentazione, allo scopo di raccogliere commenti e suggerimenti sulla relativa progettazione e implementazione.

Tuttavia, poiché le funzionalità di anteprima sono soggette a modifiche, è consigliabile evitare di scrivere codice di produzione dipendente dalle versioni di anteprima. Se si utilizza una versione di anteprima precedente, è consigliabile eseguire la migrazione alla versione disponibile a livello generale (GA).

Per .NET SDK: le linee guida per la migrazione del codice sono disponibili nell'articolo sull' [aggiornamento di .NET SDK](search-dotnet-sdk-migration.md).

Con disponibilità a livello generale si intende che Ricerca di Azure è ora soggetta al contratto di servizio (SLA). Il contratto di servizio è reperibile in [Contratto di Servizio per Ricerca](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
