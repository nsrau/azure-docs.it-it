---
title: Valutazione delle funzionalità - Personalizer
titleSuffix: Azure Cognitive Services
description: Quando si esegue una valutazione nella risorsa Personalizer dal portale di Azure, Personalizer fornisce informazioni sulle funzionalità di contesto e azioni che influenzano il modello.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242422"
---
# <a name="feature-evaluation"></a>Valutazione delle funzionalità

Quando si esegue una valutazione nella risorsa Personalizer dal portale di [Azure,](https://portal.azure.com)Personalizer fornisce informazioni sulle funzionalità di contesto e azioni che influenzano il modello. 

Ciò è utile per:

* Immagina funzionalità aggiuntive che potresti usare, ispirandoti a quali funzionalità sono più importanti nel modello.
* Vedere quali funzionalità non sono importanti e potenzialmente rimuoverle o analizzare ulteriormente ciò che potrebbe influire sull'utilizzo.
* Fornisci indicazioni ai team editoriali o di cura sui nuovi contenuti o prodotti che vale la pena inserire nel catalogo.
* Risolvere i problemi comuni e gli errori che si verificano durante l'invio di funzionalità al personalizer.

Le caratteristiche più importanti hanno pesi più forti nel modello. Poiché queste caratteristiche hanno un peso maggiore, tendono ad essere presenti quando Personalizer ottiene ricompense più alte.

## <a name="getting-feature-importance-evaluation"></a>Ottenere la valutazione dell'importanza delle funzionalitàGetting feature importance evaluation

Per visualizzare i risultati dell'importanza delle funzionalità, è necessario eseguire una valutazione. La valutazione crea etichette di entità geografiche leggibili in base ai nomi di feature osservati durante il periodo di valutazione.

Le informazioni risultanti sull'importanza delle feature rappresentano il modello online Personalizer corrente. La valutazione analizza l'importanza del modello salvato alla data di fine del periodo di valutazione, dopo aver subito tutti i corsi di formazione eseguiti durante la valutazione, con l'attuale politica di apprendimento online. 

I risultati dell'importanza delle entità geografiche non rappresentano altri criteri e modelli testati o creati durante la valutazione.  La valutazione non includerà le funzioni inviate al Personalizer dopo la fine del periodo di valutazione.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Come interpretare la valutazione dell'importanza delle caratteristiche

Il programma di visualizzazione valuta le feature creando "gruppi" di feature con un'importanza simile. Si può dire che un gruppo abbia un'importanza generale maggiore di altre, ma all'interno del gruppo, l'ordine delle caratteristiche è in ordine alfabetico.

Le informazioni su ogni feature includono:

* Se la funzionalità proviene da Contesto o Azioni.
* Chiave e valore della funzione.

Ad esempio, un'app di ordinazione di gelateria potrebbe vedere "Context.Weather:Hot" come una caratteristica molto importante.

Personalizer visualizza le correlazioni delle caratteristiche che, se prese in considerazione insieme, producono ricompense più elevate.

Ad esempio, è possibile visualizzare "Context.Weather:Hot *with* Action.MenuItem:IceCream" e "Context.Weather:Cold *con* Action.MenuItem:WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Azioni che è possibile eseguire in base alla valutazione delle funzionalità

### <a name="imagine-additional-features-you-could-use"></a>Immagina funzionalità aggiuntive che potresti usare

Ispirati alle caratteristiche più importanti del modello. Ad esempio, se vedi "Context.MobileBattery:Low" in un'app video per dispositivi mobili, potresti pensare che il tipo di connessione possa anche far sì che i clienti scelgano di vedere un clip video su un altro, quindi aggiungi funzionalità sul tipo di connettività e sulla larghezza di banda nella tua app.

### <a name="see-what-features-are-not-important"></a>Scopri quali funzionalità non sono importanti

Rimuovere potenzialmente le funzionalità non importanti o analizzare ulteriormente ciò che può influire sull'utilizzo. Le caratteristiche possono essere classificate in basso per molte ragioni. Uno potrebbe essere che veramente la funzione non influisce sul comportamento dell'utente. Ma potrebbe anche significare che la funzione non è evidente per l'utente. 

Ad esempio, un sito di video potrebbe vedere che "Action.VideoResolution 4k" è una funzionalità di bassa importanza, contraddicendo la ricerca degli utenti. La causa potrebbe essere che l'applicazione non menziona o mostra nemmeno la risoluzione video, quindi gli utenti non cambierebbero il loro comportamento in base ad essa.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Fornire indicazioni ai team editoriali o curativi

Fornisci indicazioni su nuovi contenuti o prodotti che vale la pena inserire nel catalogo. Personalizer è progettato per essere uno strumento che aumenta l'intuizione umana e team. Un modo per farlo è fornire informazioni ai gruppi editoriali su che cosa è su prodotti, articoli o contenuti che guidano il comportamento. Ad esempio, lo scenario dell'applicazione video potrebbe mostrare che esiste una caratteristica importante chiamata "Action.VideoEntities.Cat:true", che richiede al team editoriale di portare più video per gatti.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Risolvere i problemi e gli errori comuni

I problemi e gli errori comuni possono essere risolti modificando il codice dell'applicazione in modo che non invii funzionalità inappropriate o formattate in modo non corretto a Personalizer. 

Gli errori comuni durante l'invio di funzionalità includono quanto segue:

* Invio di informazioni personali (PII). Le informazioni personali specifiche di un singolo utente (ad esempio nome, numero di telefono, numeri di carta di credito, indirizzi IP) non devono essere utilizzate con Personalizer. Se l'applicazione deve tenere traccia degli utenti, utilizzare un UUID non identificante o un altro numero UserID. Nella maggior parte degli scenari questo è anche problematico.
* Con un numero elevato di utenti, è improbabile che l'interazione di ogni utente pesi più di tutta l'interazione della popolazione, quindi l'invio di ID utente (anche se non PII) probabilmente aggiungerà più rumore del valore al modello.
* Invio di campi data-ora come timestamp precisi anziché come valori di ora featurizzati. La presenza di funzionalità quali Context.TimeStamp.Day, lunedì o "Context.TimeStamp.Hour""13" è più utile. Ci saranno al massimo 7 o 24 valori di funzionalità per ciascuno. Ma "Context.TimeStamp":"1985-04-12T23:20:50.52" è così preciso che non ci sarà modo di imparare da esso perché non accadrà mai più.

## <a name="next-steps"></a>Passaggi successivi

Comprendere [la scalabilità e le prestazioni](concepts-scalability-performance.md) con Personalizer.Understand scalability and performance with Personalizer.

