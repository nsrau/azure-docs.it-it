---
title: Finalità utente
titleSuffix: Language Understanding - Azure Cognitive Services
description: Una finalità rappresenta un'attività o un'azione che l'utente desidera eseguire. È un obiettivo espresso in un'espressione dell'utente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: ae1dd16e3296c11d6bce6ea623f590deaee8f65d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871354"
---
# <a name="concepts-about-intents-in-your-luis-app"></a>Concetti relativi alle finalità nell'app LUIS

Una finalità rappresenta un'attività o un'azione che l'utente desidera eseguire. È un obiettivo espresso in un'[espressione](luis-concept-utterance.md) dell'utente.

Definire un set di finalità che corrisponde alle azioni che gli utenti desiderano eseguire nell'applicazione. Ad esempio, un'app di viaggi definisce diverse finalità:

Finalità dell'app di viaggi   |   Espressioni di esempio   | 
------|------|
 BookFlight (PrenotaVolo)     |   "Prenotami un volo per Rio la settimana prossima" <br/> "Fammi volare a Rio il 24" <br/> "Ho bisogno di un biglietto aereo per Rio de Janeiro per domenica prossima"    |
 Saluti     |   "Ciao" <br/>"Salve" <br/>"Buongiorno"  |
 Meteo | "Com'è il tempo a Boston?" <br/> "Mostrami le previsioni per il weekend" |
 Nessuna         | "Dammi una ricetta di biscotti"<br>"Ha vinto la Roma?" |

Tutte le applicazioni vengono fornite con la finalità predefinita, "[None](#none-intent-is-fallback-for-app)" (Nessuna) che è la finalità di fallback. 

## <a name="prebuilt-domains-provide-intents"></a>I domini predefiniti forniscono finalità
Oltre alle finalità definite, è possibile usare finalità predefinite da uno dei domini predefiniti. Per ulteriori informazioni, vedere [Usare domini predefiniti nelle app LUIS](luis-how-to-use-prebuilt-domains.md) per imparare a personalizzare le finalità da un dominio predefinito per l'uso nell'app.

## <a name="return-all-intents-scores"></a>Restituire i punteggi di tutte le finalità
Assegnare un'espressione a una singola finalità. Quando LUIS riceve un'espressione nell'endpoint, restituisce la finalità principale per quella espressione. Per ottenere punteggi per tutte le finalità dell'espressione, è possibile fornire un `verbose=true` flag nella stringa query della [chiamata endpoint](https://aka.ms/v1-endpoint-api-docs) all'API. 

## <a name="intent-compared-to-entity"></a>Finalità ed entità a confronto
La finalità rappresenta l'azione che il chatbot deve eseguire per l'utente ed è basata sull'intera espressione. L'entità rappresenta parole o frasi contenute all'interno dell'espressione. Un'espressione può disporre di una sola finalità punteggio più alto, ma può avere molte entità. 

<a name="how-do-intents-relate-to-entities"></a> Creare una finalità quando l'_intenzione_ dell'utente avvierebbe un'azione nell'applicazione client, ad esempio una chiamata alla funzione checkweather(). Creare un'entità per rappresentare i parametri necessari per eseguire l'azione. 

|Finalità di esempio   | Entità | Entità in espressioni di esempio   | 
|------------------|------------------------------|------------------------------|
| Meteo | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Com'è il tempo a `Seattle` `tomorrow`? |
| Meteo | { "type": "date_range", "entity": "this weekend" } | Mostrami le previsioni per `this weekend` | 

## <a name="custom-intents"></a>Finalità personalizzate

[Espressioni](luis-concept-utterance.md) con finalità simili corrispondono a una singola finalità. Le espressioni nella finalità possono usare qualsiasi [entità](luis-concept-entity-types.md) nell'app poiché le entità non sono specifiche delle finalità. 

## <a name="prebuilt-domain-intents"></a>Finalità di domini predefiniti

I [domini predefiniti](luis-how-to-use-prebuilt-domains.md) presentano finalità con espressioni.  

## <a name="none-intent-is-fallback-for-app"></a>La finalità None (Nessuna) è la finalità di fallback per l'app
La finalità **None** (Nessuna) è una finalità catch-all o fallback. Consente di insegnare a LUIS espressioni che non sono importanti nel dominio dell'app (area di interesse). Il 10-20 percento del totale delle espressioni della finalità **None** (Nessuna) deve trovarsi nell'applicazione. Non lasciare quindi vuota la finalità None (Nessuna). 

### <a name="none-intent-helps-conversation-direction"></a>La finalità None (Nessuna) favorisce la direzione della conversazione
Quando un'espressione viene stimata come finalità None (Nessuna) e restituita al chatbot con quella stima, il bot può porre ulteriori domande o fornire un menu per indirizzare l'utente a scelte valide nel chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Nessuna espressione nella finalità None (Nessuna) distorce le stime
Se non si aggiungono espressioni per la finalità **None** (Nessuna), LUIS forza un'espressione che si trova all'esterno del dominio in una delle finalità del dominio. Questo distorcerà i punteggi di stima perché a LUIS verrà insegnata la finalità errata dell'espressione. 

### <a name="add-utterances-to-the-none-intent"></a>Aggiungere espressioni alla finalità None (Nessuna)
La finalità **None** (Nessuna) viene creata ma lasciata vuota di proposito. Inserire le espressioni che si trovano all'esterno del dominio. Una buona espressione per la finalità **None** si trova completamente all'esterno dell'app e del settore servito dall'app. Ad esempio, un'app di viaggi non dovrebbe usare espressioni per **None** (Nessuna) correlate ai viaggi, ad esempio prenotazioni, fatturazione, cibo, ospitalità, carico, intrattenimento in volo. 

Quale tipo di espressioni viene lasciato per la finalità None (Nessuna)? Iniziare con qualcosa di specifico a cui il bot non dovrebbe rispondere, ad esempio "Quale dinosauro ha i denti blu?" Si tratta di una domanda molto specifica ben distante da un'app di viaggi. 

### <a name="none-is-a-required-intent"></a>None è una finalità obbligatoria
La finalità **None** è obbligatoria e non può essere eliminata o rinominata.

## <a name="negative-intentions"></a>Intenzioni negative 
Per determinare intenzioni negative e positive, ad esempio "**Voglio** un'auto" e "**Non** voglio un'auto", è possibile creare due finalità (una positiva e una negativa) e aggiungere espressioni appropriate per ognuna. In alternativa, è possibile creare una singola finalità e contrassegnare i due diversi termini positivi e negativi come entità.  

## <a name="intent-balance"></a>Bilanciamento tra finalità
Nelle finalità del dominio dell'app le espressioni devono essere equamente distribuite. Evitare di avere una finalità con 10 espressioni e un'altra con 500. Questa non è una distribuzione bilanciata. Se dovesse crearsi questa situazione, esaminare la finalità con 500 espressioni per accertare se alcune delle finalità possono essere riorganizzate in un [criterio](luis-concept-patterns.md). 

La finalità **None** non è inclusa nel bilanciamento. La finalità dovrebbe contenere il 10% delle espressioni totali nell'app.

## <a name="intent-limits"></a>Limiti della finalità
Rivedere i [limiti](luis-boundaries.md#model-boundaries) per capire quante finalità è possibile aggiungere a un modello. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Se è necessario un numero superiore al numero massimo di finalità 
Valutare innanzitutto se il sistema usa troppe finalità. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>È possibile combinare più finalità in una singola finalità con entità 
Se le finalità sono troppo simili, LUIS le distinguerà con maggiore difficoltà. Le finalità devono essere sufficientemente varie per acquisire le attività principali che chiede l'utente, anche se non è necessario che acquisiscano ogni percorso accettato dal codice. Ad esempio, BookFlight e BookHotel potrebbero essere finalità separate in un'app di viaggi, ma BookInternationalFlight e BookDomesticFlight sono troppo simili. Se il sistema deve fare distinzione tra di essi, usare le entità o altra logica anziché le finalità. 

### <a name="dispatcher-model"></a>Modello dispatcher
Ulteriori informazioni sulla combinazione di app LUIS e QnA Maker con il [modello dispatcher](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Richiedere la guida per le app con un numero significativo di finalità
Se ridurre il numero delle finalità o dividere le finalità in più app non comporta alcun miglioramento, contattare l'assistenza. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulle [entità](luis-concept-entity-types.md), ovvero parole importanti rilevanti per le finalità
* Ulteriori informazioni sull'[aggiunta e la gestione delle finalità](luis-how-to-add-intents.md) nell'app LUIS.
* Rivedere le [procedure consigliate](luis-concept-best-practices.md) delle finalità
