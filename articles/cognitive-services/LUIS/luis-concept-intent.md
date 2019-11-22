---
title: Intent ed entità-LUIS
titleSuffix: Azure Cognitive Services
description: Una singola finalità rappresenta un'attività o un'azione che l'utente desidera eseguire. È un obiettivo espresso in un'espressione dell'utente. Definire un set di finalità che corrisponde alle azioni che gli utenti desiderano eseguire nell'applicazione.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 309a2592dbac2918aeb532fbe91e33d296f4e5a5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280894"
---
# <a name="intents-in-your-luis-app"></a>Intent nell'app LUIS

Una finalità rappresenta un'attività o un'azione che l'utente desidera eseguire. È un obiettivo espresso in un'[espressione](luis-concept-utterance.md) dell'utente.

Definire un set di finalità che corrisponde alle azioni che gli utenti desiderano eseguire nell'applicazione. Ad esempio, un'app di viaggi definisce diverse finalità:

Finalità dell'app di viaggi   |   Espressioni di esempio   | 
------|------|
 PrenotaVolo     |   "Prenotami un volo per Rio la settimana prossima" <br/> "Fammi volare a Rio il 24" <br/> "Ho bisogno di un biglietto aereo per Rio de Janeiro per domenica prossima"    |
 Saluti     |   "Ciao" <br/>"Salve" <br/>"Buongiorno"  |
 Meteo | "Com'è il tempo a Boston?" <br/> "Mostrami le previsioni per il weekend" |
 nessuno         | "Dammi una ricetta di biscotti"<br>"Ha vinto la Roma?" |

Tutte le applicazioni hanno lo scopo predefinito, ovvero "[None](#none-intent)", ovvero lo scopo del fallback. 

## <a name="prebuilt-domains-provide-intents"></a>I domini predefiniti forniscono finalità
Oltre agli Intent definiti, è possibile usare gli Intent predefiniti da uno dei [domini predefiniti](luis-how-to-use-prebuilt-domains.md). 

## <a name="return-all-intents-scores"></a>Restituire i punteggi di tutte le finalità
Assegnare un'espressione a una singola finalità. Quando LUIS riceve un enunciato sull'endpoint, per impostazione predefinita restituisce l'intento superiore per tale espressione. 

Se si desiderano i punteggi per tutti gli Intent per l'espressione, è possibile specificare un flag sulla stringa di query dell'API di stima. 

|Versione dell'API di stima|Flag|
|--|--|
|V2|`verbose=true`|
|V3|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Finalità ed entità a confronto
Lo scopo rappresenta l'azione che l'applicazione deve eseguire per l'utente e si basa sull'intera espressione. Un'espressione può disporre di una sola finalità punteggio più alto, ma può avere molte entità. 

<a name="how-do-intents-relate-to-entities"></a>

Creare una finalità quando l' _intenzione_ dell'utente attiverà un'azione nell'applicazione client, ad esempio una chiamata alla funzione checkweather (). Quindi creare entità per rappresentare i parametri necessari per eseguire l'azione. 

|Finalità   | Entità | Espressione di esempio   | 
|------------------|------------------------------|------------------------------|
| Meteo | { "type": "location", "entity": "seattle" }<br>{ "type": "builtin.datetimeV2.date","entity": "tomorrow","resolution":"2018-05-23" } | Com'è il tempo a `Seattle` `tomorrow`? |
| Meteo | { "type": "date_range", "entity": "this weekend" } | Mostrami le previsioni per `this weekend` | 
||||

## <a name="prebuilt-domain-intents"></a>Finalità di domini predefiniti

I [domini predefiniti](luis-how-to-use-prebuilt-domains.md) forniscono gli Intent con espressioni. 

## <a name="none-intent"></a>Finalità None

La finalità **None** (Nessuna) viene creata ma lasciata vuota di proposito. La finalità **None** è obbligatoria e non può essere eliminata o rinominata. Inserire le espressioni che si trovano all'esterno del dominio.

Lo scopo di **nessuno** è l'intento di fallback, importante in ogni app, e deve avere il 10% delle espressioni totali. Consente di insegnare a LUIS espressioni che non sono importanti nel dominio dell'app (area di interesse). Se non si aggiungono espressioni per la finalità **None** (Nessuna), LUIS forza un'espressione che si trova all'esterno del dominio in una delle finalità del dominio. Questo distorcerà i punteggi di stima perché a LUIS verrà insegnata la finalità errata dell'espressione. 

Quando un enunciato viene stimato come intento per nessuno, l'applicazione client può porre altre domande o fornire un menu per indirizzare l'utente a scelte valide. 

## <a name="negative-intentions"></a>Intenzioni negative 
Per determinare intenzioni negative e positive, ad esempio "**Voglio** un'auto" e "**Non** voglio un'auto", è possibile creare due finalità (una positiva e una negativa) e aggiungere espressioni appropriate per ognuna. In alternativa, è possibile creare una singola finalità e contrassegnare i due diversi termini positivi e negativi come entità.  

## <a name="intents-and-patterns"></a>Finalità e modelli

Se sono presenti espressioni di esempio, che possono essere definite in parte o intere come espressione regolare, è consigliabile usare l' [entità di espressione regolare](luis-concept-entity-types.md#regular-expression-entity) abbinata a un [modello](luis-concept-patterns.md). 

L'uso di un'entità di espressione regolare garantisce l'estrazione dei dati in modo che il modello venga associato. Il criterio di ricerca garantisce che venga restituito un preventivo esatto. 

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
