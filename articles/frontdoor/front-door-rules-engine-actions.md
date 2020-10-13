---
title: Azioni del motore regole di Frontdoor di Azure
description: Questo articolo fornisce un elenco delle azioni che è possibile eseguire con il motore regole del servizio Frontdoor di Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569784"
---
# <a name="azure-front-door-rules-engine-actions"></a>Azioni del motore regole di Frontdoor di Azure

Nel [motore regole di Frontdoor di Azure](front-door-rules-engine.md) una regola è costituita da zero o più condizioni di corrispondenza e azioni. Questo articolo fornisce descrizioni dettagliate delle azioni che è possibile usare nel motore regole di Frontdoor di Azure.

Un'azione definisce il comportamento applicato al tipo di richiesta identificata da una o più condizioni di corrispondenza. Nel motore regole di Frontdoor di Azure una regola può contenere fino a cinque azioni. Solo una di queste azioni può essere un'azione di sostituzione della configurazione delle route (inoltro o reindirizzamento).

Le azioni seguenti sono disponibili per l'uso nel motore regole di Frontdoor di Azure.  

## <a name="modify-request-header"></a>Modifica intestazione richiesta

Usare questa azione per modificare le intestazioni presenti nelle richieste inviate all'origine.

### <a name="required-fields"></a>Required fields

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | Se questa opzione viene selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore viene aggiunto a quello esistente. | string
Overwrite | Se questa opzione viene selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla richiesta con il valore specificato. Se l'intestazione è già presente, il valore specificato sovrascrive quello esistente. | string
Delete | Se questa opzione viene selezionata con regole corrispondenti e l'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla richiesta. | string

## <a name="modify-response-header"></a>Modifica intestazione risposta

Usare questa azione per modificare le intestazioni presenti nelle risposte restituite ai client.

### <a name="required-fields"></a>Required fields

Azione | Nome intestazione HTTP | valore
-------|------------------|------
Accoda | Se questa opzione viene selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla risposta con il **valore** specificato. Se l'intestazione è già presente, il **valore** viene aggiunto a quello esistente. | string
Overwrite | Se questa opzione è selezionata e la regola corrisponde, l'intestazione specificata in **Nome intestazione** viene aggiunta alla risposta con il **valore** specificato. Se l'intestazione è già presente, il **valore** sovrascrive quello esistente. | string
Delete | Se questa opzione viene selezionata e la regola corrispondente all'intestazione specificata nella regola è presente, l'intestazione viene eliminata dalla risposta. | string

## <a name="route-configuration-overrides"></a>Sostituzione delle configurazioni della route 

### <a name="route-type-redirect"></a>Tipo di route: reindirizzamento

Usare questa azione per reindirizzare i client a un nuovo URL. 

#### <a name="required-fields"></a>Required fields

Campo | Descrizione 
------|------------
Tipo di reindirizzamento | Selezionare il tipo di risposta da restituire al richiedente: Trovato (302), Spostato (301), Reindirizzamento temporaneo (307) e Reindirizzamento permanente (308).
Protocollo di reindirizzamento | Corrispondenza richiesta, HTTP, HTTPS.
Host di destinazione | Selezionare il nome host a cui reindirizzare la richiesta. Lasciare vuoto questo campo per mantenere l'host in ingresso.
Percorso di destinazione | Definire il percorso da usare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il percorso in ingresso.  
Stringa di query | Definire la stringa di query usata nel reindirizzamento. Lasciare vuoto questo campo per mantenere la stringa di query in ingresso. 
Frammento di destinazione | Definire il frammento da usare nel reindirizzamento. Lasciare vuoto questo campo per mantenere il frammento in ingresso. 


### <a name="route-type-forward"></a>Tipo di route: Avanti

Usare questa azione per inoltrare i client a un nuovo URL. Questa azione contiene anche azioni secondarie per le riscritture di URL e la memorizzazione nella cache. 

Campo | Descrizione 
------|------------
Pool back-end | Selezionare il pool back-end per eseguire l'override e rispondere alle richieste. In questo modo, vengono visualizzati anche tutti i pool back-end già configurati nel profilo di Frontdoor. 
Protocollo di inoltro | Corrispondenza richiesta, HTTP, HTTPS.
Riscrittura URL | Usare questa azione per riscrivere il percorso di una richiesta instradata all'origine. Se questa opzione è abilitata, vedere gli altri campi necessari seguenti
Memorizzazione nella cache | Enabled, Disabled. Se questa opzione è abilitata, vedere gli altri campi necessari seguenti. 

#### <a name="url-rewrite"></a>Riscrittura URL

Usare questa impostazione per configurare un **Percorso di inoltro personalizzato** facoltativo da usare per la creazione della richiesta da inoltrare al back-end.

Campo | Descrizione 
------|------------
Percorso di inoltro personalizzato | Definire il percorso a cui inoltrare le richieste. 

#### <a name="caching"></a>Memorizzazione nella cache

Usare queste impostazioni per controllare il modo in cui i file vengono memorizzati nella cache per le richieste che contengono stringhe di query. Indica se memorizzare nella cache il contenuto basato su tutti i parametri o su parametri selezionati. È possibile usare altre impostazioni per sovrascrivere il valore TTL (time to live) in modo da controllare per quanto tempo il contenuto deve rimanere nella cache. Per forzare la memorizzazione nella cache come azione, impostare il campo Memorizzazione nella cache su "Abilitata". Quando si impone la memorizzazione nella cache, vengono visualizzate le opzioni seguenti: 

Comportamento cache |  Descrizione              
---------------|----------------
Ignora stringhe di query | Una volta memorizzati gli asset nella cache, tutte le richieste che seguono ignorano le stringhe di query fino alla scadenza degli asset.
Memorizza nella cache tutti gli URL univoci | Ogni richiesta con un URL univoco, inclusa la stringa di query, viene trattata come asset univoco con la propria cache.
Ignora stringhe di query specificate | Le stringhe di query dell'URL della richiesta elencate nell'impostazione "Parametri query" vengono ignorate per la memorizzazione nella cache.
Includi stringhe di query specificate | Le stringhe di query dell'URL della richiesta elencate nell'impostazione "Parametri query" vengono usate per la memorizzazione nella cache.

Altri campi |  Descrizione 
------------------|---------------
Compressione dinamica | Frontdoor può comprimere dinamicamente il contenuto nel perimetro, generando una risposta più piccola e veloce.
Parametri di query | Un elenco di parametri consentiti (o non consentiti) delimitati da virgole da usare come base per la memorizzazione nella cache.
Durata cache | La durata della cache prima della scadenza, in giorni, ore, minuti e secondi. Tutti i valori devono essere numeri interi. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come configurare il primo [motore regole](front-door-tutorial-rules-engine.md). 
- Altre informazioni sulle [condizioni di corrispondenza del motore regole](front-door-rules-engine-match-conditions.md)
- Altre informazioni sul [motore regole di Frontdoor di Azure](front-door-rules-engine.md)
