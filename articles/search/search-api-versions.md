---
title: Versioni dell&quot;API di Ricerca di Azure | Microsoft Azure | API di ricerca
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
ms.date: 10/27/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 66aec5feaa370aef6dc4a9f83119e26ab47d726a

---

# <a name="api-versions-in-azure-search"></a>Versioni API in Ricerca di Azure
Ricerca di Azure aggiorna le funzionalità a intervalli regolari. A volte, ma non sempre, questi aggiornamenti richiedono la pubblicazione di una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Questa pubblicazione consente di controllare come e quando integrare gli aggiornamenti del servizio di ricerca nel codice.

Di norma, le nuove versioni vengono pubblicate solo se necessario, perché ciò può comportare alcune operazioni di aggiornamento del codice per l'utilizzo di una nuova versione dell'API. Verrà pubblicata una nuova versione solo in caso di modifica di alcuni aspetti dell'API che interrompe la compatibilità con le versioni precedenti. Ciò può verificarsi in caso di correzioni alle funzionalità esistenti o di nuove funzionalità che modificano la superficie di attacco dell'API esistente.

Si applica la stessa regola per gli aggiornamenti all'SDK. Azure Search SDK segue le regole di [versionamento semantico](http://semver.org/) , ciò significa che la versione ha tre parti: principale, secondaria e numero di build (ad esempio, 1.1.0). Una nuova versione principale dell'SDK verrà rilasciata solo in caso di modifiche che interrompono la compatibilità con le versioni precedenti. Per aggiornamenti di funzionalità senza interruzione di compatibilità verrà incrementata la versione secondaria e, in caso di correzione di bug, verrà incrementata solo la versione build.

## <a name="snapshot-of-current-versions"></a>Panoramica delle versioni correnti
Di seguito è illustrata una panoramica delle versioni correnti di tutte le interfacce di programmazione di Ricerca di Azure. Le guide di orientamento e altri dettagli sono disponibili nelle sezioni successive di questo documento.

| Interfacce | Versione principale più recente | Stato |
| --- | --- | --- |
| [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) |1.1 |Disponibile a livello generale, rilasciata a febbraio 2016 |
| [Anteprima di .NET SDK](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) |2.0-preview |anteprima, rilasciata ad agosto 2016 |
| [API REST del servizio](https://msdn.microsoft.com/library/azure/dn798935.aspx) |2016-09-01 |Disponibile a livello generale |
| [Anteprima API REST del servizio](search-api-2015-02-28-preview.md) |2015-02-28-Preview |Preview |
| [API REST di gestione](https://msdn.microsoft.com/library/azure/dn832684.aspx) |2015-08-19 |Disponibile a livello generale |

Per le API REST, è necessario includere il parametro `api-version` a ogni chiamata. Questo facilita l'identificazione di una versione specifica, ad esempio un'API di anteprima. L'esempio seguente illustra come viene specificato il parametro `api-version` :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2016-09-01

> [!NOTE]
> Benché ogni richiesta abbia un parametro `api-version`, è consigliabile usare la stessa versione per tutte le richieste API. Ciò vale soprattutto nel caso in cui nuove versioni API introducano attributi oppure operazioni non riconosciuti dalle versioni precedenti. La combinazione di diverse versioni API può avere conseguenze impreviste e deve essere evitata.
>
> L'API REST del servizio e l'API REST di gestione sono sottoposte al controllo delle versioni in modo indipendente tra loro. L'eventuale somiglianza del numero di versione è causale.
>
>

Le API disponibili a livello generale (o GA) possono essere utilizzate nell'ambiente di produzione e sono soggette a contratti di servizio di Azure. Le versioni di anteprima offrono funzionalità sperimentali che non sempre vengono migrate a una versione GA. **È consigliabile evitare l'utilizzo delle API di anteprima in applicazioni di produzione.**

## <a name="sdk-version-roadmap"></a>Guida di orientamento alla versione SDK
Ogni versione di .NET SDK è destinata a una particolare versione dell'API REST del servizio. Le funzionalità sono distribuite prima nell'API REST e quindi implementate nel SDK.

.NET SDK è ora disponibile a livello generale e sono già in corso i lavori per la prossima versione. La tabella seguente riporta le versioni future del SDK per dare un'idea delle novità in arrivo.

| Versione di .NET SDK | Versione API REST | Funzionalità | ETA |
| --- | --- | --- | --- |
| 1.1 |2015-02-28 |Sintassi di query Lucene |Febbraio 2016 |
| 2.0-preview |2015-02-28-Preview |Analizzatori personalizzati, indicizzatori di tabelle e BLOB di Azure, mapping dei campi, ETag |Agosto 2016 |
| 3.x |2016-09-01 |Analoga alla versione 2.0-preview, meno il supporto per l'indicizzatore BLOB per i file con estensione csv e json |Novembre 2016 |

## <a name="about-preview-and-generally-available-versions"></a>Informazioni sull'anteprima e sulle versioni disponibili a livello generale
Ricerca di Azure rilascia sempre in via preliminare funzionalità sperimentali tramite l'API REST, quindi tramite versioni non definitive di .NET SDK.

La migrazione alla versione GA delle funzionalità di anteprima non è garantita. Mentre le funzionalità in una versione GA vengono considerate stabili e difficilmente modificabili, ad eccezione di piccoli miglioramenti e correzioni compatibili con le versioni precedenti, le funzionalità di anteprima sono disponibili per il testing e la sperimentazione, allo scopo di raccogliere commenti e suggerimenti sulla relativa progettazione e implementazione.

Tuttavia, poiché le funzionalità di anteprima sono soggette a modifiche, è consigliabile evitare di scrivere codice di produzione dipendente dalle versioni di anteprima. Se si utilizza una versione di anteprima precedente, è consigliabile eseguire la migrazione alla versione disponibile a livello generale (GA).

Per .NET SDK: le linee guida per la migrazione del codice sono disponibili nell'articolo sull' [aggiornamento di .NET SDK](search-dotnet-sdk-migration.md).

Con disponibilità a livello generale si intende che Ricerca di Azure è ora soggetta al contratto di servizio (SLA). Il contratto di servizio è reperibile in [Contratto di Servizio per Ricerca](https://azure.microsoft.com/support/legal/sla/search/v1_0/).



<!--HONumber=Nov16_HO3-->


