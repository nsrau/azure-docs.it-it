---
title: Valutazione delle funzionalità-personalizzatore
titleSuffix: Azure Cognitive Services
description: Quando si esegue una valutazione nella risorsa di personalizzazione dall'portale di Azure, il Personalizzatore fornisce informazioni sulle funzionalità del contesto e delle azioni che influiscono sul modello.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: d03c5f66f760a2bea9f99501cec478831a347c5d
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668724"
---
# <a name="feature-evaluation"></a>Valutazione delle funzionalità

Quando si esegue una valutazione nella risorsa di personalizzazione dall' [portale di Azure](https://portal.azure.com), il Personalizzatore fornisce informazioni sulle funzionalità del contesto e delle azioni che influiscono sul modello. 

Questo è utile per:

* Immagina le funzionalità aggiuntive che puoi usare per ottenere ispirazione dalle funzionalità più importanti del modello.
* Vedere quali funzionalità non sono importanti e potenzialmente rimuoverle o analizzare ulteriormente ciò che può influire sull'utilizzo.
* Fornire indicazioni ai team editoriali o curanti sui nuovi contenuti o prodotti che è opportuno inserire nel catalogo.
* Risolvere i problemi comuni e gli errori che si verificano quando si inviano funzionalità al personalizzatore.

Le funzionalità più importanti hanno un peso maggiore nel modello. Poiché queste funzionalità hanno un peso maggiore, tendono a essere presenti quando il Personalizzatore ottiene ricompense più elevate.

## <a name="getting-feature-importance-evaluation"></a>Recupero della valutazione dell'importanza della funzionalità

Per visualizzare i risultati dell'importanza della funzionalità, è necessario eseguire una valutazione. Le informazioni risultanti sull'importanza della funzionalità rappresentano il modello online di personalizzazione corrente. La valutazione analizza l'importanza della funzionalità del modello salvato alla data di fine del periodo di valutazione. 

La valutazione crea etichette di funzionalità leggibili in base ai nomi delle funzionalità osservate durante il periodo di valutazione.

I risultati dell'importanza della funzionalità non rappresentano altri criteri e modelli testati o creati durante la valutazione.  La valutazione non includerà le funzionalità inviate al personalizzatore dopo la fine del periodo di valutazione.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>Come interpretare la valutazione dell'importanza della funzionalità

Il Personalizzatore valuta le funzionalità creando "gruppi" di funzionalità con un'importanza simile. Un gruppo può essere definito in modo da avere una maggiore importanza globale rispetto ad altri, ma all'interno del gruppo l'ordinamento delle funzionalità è alfabeticamente.

Le informazioni su ogni funzionalità includono:

* Indica se la funzionalità deriva dal contesto o dalle azioni.
* Chiave e valore della funzionalità.

Ad esempio, un'app per l'ordinamento di Ice Cream Shop potrebbe visualizzare "context. Weather: Hot" come funzionalità molto importante.

Personalizzator Visualizza le correlazioni delle funzionalità che, quando vengono prese in considerazione insieme, producono ricompense più elevate.

Ad esempio, è possibile visualizzare "context. Weather: Hot *with* Action. MenuItem: gelato" e "context. Weather: Cold *con* Action. MenuItem: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Azioni che è possibile eseguire in base alla valutazione delle funzionalità

### <a name="imagine-additional-features-you-could-use"></a>Immagina le funzionalità aggiuntive che puoi usare

Trarre spunto dalle funzionalità più importanti del modello. Se, ad esempio, viene visualizzato "context. MobileBattery: low" in un'app per dispositivi mobili video, si potrebbe pensare che il tipo di connessione possa anche fare in modo che i clienti scelgano di visualizzare un clip video rispetto a un altro, quindi aggiungono funzionalità relative al tipo di connettività e alla larghezza di banda nell'app.

### <a name="see-what-features-are-not-important"></a>Scopri le funzionalità non importanti

Rimuovere potenzialmente le funzionalità non importanti o analizzare ulteriormente ciò che può influire sull'utilizzo. Le funzionalità possono avere un rango basso per diversi motivi. Uno potrebbe essere che la funzionalità non influisce sul comportamento dell'utente. Ma potrebbe anche significare che la funzionalità non è visibile all'utente. 

Ad esempio, un sito video potrebbe vedere che "Action. VideoResolution = 4K" è una funzionalità di scarsa importanza, contraddicendo la ricerca degli utenti. È possibile che l'applicazione non menzioni o mostri la risoluzione dei video, in modo che gli utenti non modifichino il proprio comportamento in base a questo.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Fornire linee guida per i team editoriali o curativi

Fornire informazioni aggiuntive sui nuovi contenuti o sui prodotti che è opportuno includere nel catalogo. Il Personalizzatore è progettato per essere uno strumento che consente di migliorare le informazioni umane e i team. Un modo per ottenere questo risultato è fornire informazioni ai gruppi editoriali in merito a prodotti, articoli o contenuti che guidano il comportamento. Ad esempio, lo scenario dell'applicazione video potrebbe indicare che è presente una funzionalità importante denominata "Action. VideoEntities. cat: true", che richiede al team editoriale di inserire più video Cat.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Risolvere i problemi comuni e gli errori

I problemi comuni e gli errori possono essere corretti cambiando il codice dell'applicazione in modo che non invii funzionalità non appropriate o formattate in modo errato per la personalizzazione. 

Gli errori comuni quando si inviano funzionalità sono i seguenti:

* Invio di informazioni personali (PII). Le informazioni personali specifiche di un singolo utente (ad esempio nome, numero di telefono, numeri di carta di credito, indirizzi IP) non devono essere usate con la personalizzazione. Se l'applicazione deve tenere traccia degli utenti, usare un UUID non di identificazione o un altro numero di ID utente. Nella maggior parte degli scenari questo problema è problematico.
* Con un numero elevato di utenti, è improbabile che l'interazione di ogni utente peserà più di tutte le interazioni della popolazione, quindi l'invio di ID utente (anche se non PII) aggiungerà probabilmente più rumore del valore al modello.
* Invio di campi di data e ora come timestamp precisi anziché valori trasformato tempo. La presenza di funzionalità quali context. TimeStamp. Day = Monday o "context. TimeStamp. hour" = "13" è più utile. Per ognuno saranno presenti al massimo 7 o 24 valori di funzionalità. Ma "context. TimeStamp": "1985-04-12T23:20:50.52 Z" è così precisa che non vi sarà alcun modo per imparare da questo perché non si verificherà mai più.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla [scalabilità e sulle prestazioni](concepts-scalability-performance.md) con la personalizzazione.

