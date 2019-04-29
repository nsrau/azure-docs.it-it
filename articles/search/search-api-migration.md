---
title: Aggiornare all'ultima versione dell'API REST del servizio di Ricerca di Azure - Ricerca di Azure
description: Esaminare le differenze nelle versioni API e ottenere informazioni su quali azioni sono necessarie per la migrazione del codice esistente per la versione più recente dell'API REST del servizio di Ricerca di Azure.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 23003859b9a75fb986fe65f5528004f3dd150f9b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126992"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Aggiornare all'ultima versione dell'API REST di Ricerca di Azure
Se si usa una versione precedente dell'[API REST di Ricerca di Azure](https://docs.microsoft.com/rest/api/searchservice/), questo articolo fornisce informazioni utili per aggiornare l'applicazione alla più recente versione disponibile a livello generale dell'API, la versione 2017-11-11.

La versione 2017-11-11 dell'API REST include alcune modifiche rispetto alle versioni precedenti. Le versioni sono abbastanza compatibili tra loro, pertanto la modifica del codice richiede un impegno minimo, a seconda della versione in uso prima. Per istruzioni su come modificare il codice per usare la nuova versione dell'API, vedere [Steps to upgrade](#UpgradeSteps) (Passaggi per eseguire l'aggiornamento).

> [!NOTE]
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2017-11-11"></a>Novità della versione 2017-11-11
La versione 2017-11-11 è la più recente disponibile a livello generale dell'API REST di Ricerca di Azure. Le nuove funzionalità in questa versione dell'API includono:

* [Sinonimi](search-synonyms.md). La nuova funzionalità Sinonimi consente di definire termini equivalenti e di espandere l'ambito della query.
* [Supporto per l'indicizzazione efficiente dei BLOB di testo](https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#IndexingPlainText). La nuova modalità di analisi `text` per gli indicizzatori BLOB di Azure migliora notevolmente le prestazioni dell'indicizzazione.
* [API delle statistiche del servizio](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics). Questa nuova API consente di visualizzare l'utilizzo corrente e i limiti delle risorse in Ricerca di Azure.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Se si esegue l'aggiornamento da una versione con disponibilità generale, la 2015-02-28 o la 2016-09-01, probabilmente non è necessario apportare modifiche al codice oltre alla modifica del numero di versione. Le uniche situazioni in cui può essere necessario modificare il codice si verificano quando:

* Il codice ha esito negativo quando vengono restituite proprietà sconosciute in una risposta API. Per impostazione predefinita, l'applicazione deve ignorare le proprietà che non riconosce.
* Il codice rende persistenti le richieste API e tenta di inviarle nuovamente alla nuova versione dell'API. Ad esempio, questa situazione può verificarsi se l'applicazione mantiene i token di continuazione restituiti dall'API di ricerca. Per altre informazioni, cercare `@search.nextPageParameters` nel [riferimento all'API di ricerca](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

Se una delle situazioni seguenti si applica al caso dell'utente, può essere necessario modificare il codice in modo appropriato. In caso contrario, non sono necessarie modifiche a meno che non si voglia iniziare a usare le [nuove funzionalità](#WhatsNew) della versione 2017-11-11.

Se si esegue l'aggiornamento da una versione in anteprima dell'API, si applica la situazione precedente, ma è necessario essere consapevoli che alcune funzionalità di anteprima non sono disponibili nella versione 2017-11-11:

* Supporto per gli indicizzatori di Archiviazione BLOB di Azure per i file e i BLOB con estensione csv contenenti matrici JSON.
* Query "Altri elementi simili"

Se il codice usa queste funzionalità, non sarà possibile eseguire l'aggiornamento alla versione 2017-11-11 senza rimuovere l'utilizzo di tali funzionalità.

> [!IMPORTANT]
> Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.
> 
> 

## <a name="conclusion"></a>Conclusioni
Per altre informazioni sull'uso dell'API REST di Ricerca di Azure, vedere il [riferimento all'API](https://docs.microsoft.com/rest/api/searchservice/) su MSDN aggiornato di recente.

I commenti degli utenti su Ricerca di Azure sono molto apprezzati. In caso di problemi, è possibile richiedere assistenza nel [forum MSDN su Ricerca di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) o in [StackOverflow](https://stackoverflow.com/). Se si pongono domande su Ricerca di Azure in StackOverflow, assicurarsi di contrassegnarle con `azure-search`.

Grazie per avere usato Ricerca di Azure.

