---
title: Aggiornamento all&quot;API REST di Ricerca di Azure versione 2016-09-01 | Documentazione Microsoft
description: Aggiornamento all&quot;API REST di Ricerca di Azure versione 2016-09-01
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 6183fa6c-48bb-4af7-adae-4be3bc43c3ed
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 7d45759915f38ba4337b745eb2b28dcbc72dbbe0
ms.openlocfilehash: f6a189c2e314b91c490583a86d8bacca8ec78a0f

---
# <a name="upgrading-to-the-azure-search-service-rest-api-version-2016-09-01"></a>Aggiornamento all'API REST di Ricerca di Azure versione 2016-09-01
Se si usa la versione 2015-02-28 o 2015-02-28-Preview dell'[API REST di Ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx), questo articolo consente di aggiornare l'applicazione alla prima versione disponibile a livello generale dell'API, la versione 2016-09-01.

La versione 2016-09-01 dell'API REST include alcune modifiche rispetto alle versioni precedenti. Le versioni sono abbastanza compatibili tra loro, pertanto la modifica del codice richiede un impegno minimo, a seconda della versione in uso prima. Per istruzioni su come modificare il codice per usare la nuova versione dell'API, vedere [Steps to upgrade](#UpgradeSteps) (Passaggi per eseguire l'aggiornamento).

> [!NOTE]
> L'istanza del servizio Ricerca di Azure supporta diverse versioni di API REST, inclusa quella più recente. È possibile continuare a usare una versione anche se non è la più recente, ma si consiglia di migrare il codice per usare la versione più recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2016-09-01"></a>Novità della versione 2016-09-01
La versione 2016-09-01 è la seconda disponibile a livello generale dell'API REST di Ricerca di Azure. Le nuove funzionalità in questa versione dell'API includono:

* Gli [analizzatori personalizzati](https://aka.ms/customanalyzers), che consentono di controllare il processo di conversione del testo in token indicizzabili e ricercabili.
* Gli indicizzatori [Archiviazione BLOB di Azure](search-howto-indexing-azure-blob-storage.md) e [Archiviazione tabelle di Azure](search-howto-indexing-azure-tables.md), che consentono di importare facilmente dati da Archiviazione di Azure in Ricerca di Azure in base a una pianificazione o su richiesta.
* [Mapping dei campi](search-indexer-field-mappings.md), che consentono di personalizzare la modalità di importazione dei dati da parte degli indicizzatori in Ricerca di Azure.
* ETag, che consentono di aggiornare le definizioni di indici, indicizzatori e origini dati in modo indipendente dalla concorrenza. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Passaggi per eseguire l'aggiornamento
Se esegue l'aggiornamento dalla versione 2015-02-28, probabilmente non è necessario apportare modifiche al codice, oltre alla modifica del numero di versione. Le uniche situazioni in cui può essere necessario modificare il codice si verificano quando:

* Il codice ha esito negativo quando vengono restituite proprietà sconosciute in una risposta API. Per impostazione predefinita, l'applicazione deve ignorare le proprietà che non riconosce.
* Il codice rende persistenti le richieste API e tenta di inviarle nuovamente alla nuova versione dell'API. Ad esempio, questa situazione può verificarsi se l'applicazione mantiene i token di continuazione restituiti dall'API di ricerca. Per altre informazioni, cercare `@search.nextPageParameters` nel [riferimento all'API di ricerca](https://msdn.microsoft.com/library/azure/dn798927.aspx#Anchor_1).

Se una delle situazioni seguenti si applica al caso dell'utente, può essere necessario modificare il codice in modo appropriato. In caso contrario, non sono necessarie modifiche a meno che non si voglia iniziare a usre le [nuove funzionalità](#WhatsNew) della versione 2016-09-01.

Se si esegue l'aggiornamento dalla versione 2015-02-28-Preview, si applica la situazione precedente, ma è necessario essere consapevoli che alcune funzionalità di anteprima non sono disponibili nella versione 2016-09-01:

* Supporto per gli indicizzatori di Archiviazione BLOB di Azure per i file e i BLOB con estensione csv contenenti matrici JSON.
* Sinonimi
* Query "Altri elementi simili"

Se il codice usa queste funzionalità, non sarà possibile eseguire l'aggiornamento alla versione 2016-09-01 senza rimuovere l'utilizzo di tali funzionalità.

> [!IMPORTANT]
> Si ricordi che le API di anteprima servono per il test e la valutazione e non devono essere usate negli ambienti di produzione.
> 
> 

## <a name="conclusion"></a>Conclusione
Per altre informazioni sull'uso dell'API REST di Ricerca di Azure, vedere il [riferimento all'API](https://msdn.microsoft.com/library/azure/dn798935.aspx) su MSDN aggiornato di recente.

I commenti degli utenti su Ricerca di Azure sono molto apprezzati. In caso di problemi, è possibile richiedere assistenza nel [forum MSDN su Ricerca di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch) o in [StackOverflow](http://stackoverflow.com/). Se si pongono domande su Ricerca di Azure in StackOverflow, assicurarsi di contrassegnarle con `azure-search`.

Grazie per avere usato Ricerca di Azure.




<!--HONumber=Jan17_HO2-->


