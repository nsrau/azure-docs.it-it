---
title: Funzionalità di Machine Learning con LUIS
description: L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 08ab71375171d4bb4167c725bc7118bec2e1ebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91372016"
---
# <a name="machine-learning-features"></a>Funzionalità di Machine Learning

In Machine Learning, una *funzionalità*   è un tratto o un attributo distintivo dei dati osservati e appreso dal sistema.

Le funzionalità di Machine Learning offrono a LUIS importanti indicazioni per la posizione in cui cercare gli elementi che distinguono un concetto. Si tratta di suggerimenti che LUIS può usare, ma non regole rigide. LUIS utilizza questi hint insieme alle etichette per trovare i dati.

Una funzionalità può essere descritta come funzione, ad esempio f (x) = y. Nell'enunciato di esempio, la funzionalità indica dove cercare il tratto di distinzione. Usare queste informazioni per semplificare la creazione dello schema.

## <a name="types-of-features"></a>Tipi di funzionalità

Le funzionalità sono una parte necessaria della progettazione dello schema. LUIS supporta sia gli elenchi di frasi che i modelli come funzionalità:

* Funzionalità elenco frasi
* Modello (finalità o entità) come funzionalità

## <a name="find-features-in-your-example-utterances"></a>Trovare le funzionalità nelle espressioni di esempio

Poiché LUIS è un'applicazione basata sul linguaggio, le funzionalità sono basate su testo. Scegliere il testo che indica il tratto che si vuole distinguere. Per LUIS, l'unità più piccola è il *token*. Per la lingua inglese, un token è un intervallo contiguo di lettere e numeri senza spazi o segni di punteggiatura.

Poiché gli spazi e la punteggiatura non sono token, concentrarsi sugli indizi del testo che è possibile usare come funzionalità. Ricordarsi di includere varianti di parole, ad esempio:

* forme plurali
* tempi di verbi
* abbreviazioni
* ortografia e errori di ortografia

Determinare se il testo, poiché distingue un tratto, deve:

* Trovare la corrispondenza con una parola o una frase esatta: è consigliabile aggiungere un'entità di espressione regolare o un'entità di elenco come funzionalità all'entità o allo scopo.
* Trovare la corrispondenza con un concetto noto, ad esempio date, ore o nomi di persone: usare un'entità predefinita come funzionalità per l'entità o finalità.
* Informazioni sui nuovi esempi nel tempo: usare un elenco di frasi di alcuni esempi del concetto come funzionalità per l'entità o finalità.

## <a name="create-a-phrase-list-for-a-concept"></a>Creare un elenco di frasi per un concetto

Un elenco di frasi è un elenco di parole o frasi che descrivono un concetto. Un elenco di frasi viene applicato come corrispondenza senza distinzione tra maiuscole e minuscole a livello di token.

Quando si aggiunge un elenco di frasi, è possibile impostare la funzionalità come **[globale](#global-features)**. Una funzionalità globale si applica all'intera app.

### <a name="when-to-use-a-phrase-list"></a>Quando usare un elenco di frasi

Usare un elenco di frasi quando è necessario che l'app LUIS generalizza e identifichi i nuovi elementi per il concetto. Gli elenchi di frasi sono simili al vocabolario specifico del dominio. Migliorano la qualità della comprensione per gli Intent e le entità.

### <a name="how-to-use-a-phrase-list"></a>Come usare un elenco di frasi

Con un elenco di frasi, LUIS considera il contesto e le generalizzazioni per identificare gli elementi simili a, ma non corrispondono esattamente al testo. Per usare un elenco di frasi, attenersi alla procedura seguente:

1. Inizia con un'entità di Machine Learning:
    1. Aggiungere espressioni di esempio.
    1. Etichetta con un'entità di machine learning.
1. Aggiungere un elenco di frasi:
    1. Aggiungere parole con un significato simile. Non aggiungere tutte le parole o frasi possibili. Aggiungere invece alcune parole o frasi alla volta. Quindi ripetere il training e pubblicare.
    1. Esaminare e aggiungere le parole suggerite.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Uno scenario tipico per un elenco di frasi

Uno scenario tipico per un elenco di frasi è quello di migliorare le parole correlate a un'idea specifica.

Le condizioni mediche sono un valido esempio di parole che potrebbero richiedere un elenco di frasi per incrementare la loro importanza. Questi termini possono avere un significato fisico, chimico, terapeutico o astratto specifico. LUIS non saprà che i termini sono importanti per il dominio dell'oggetto senza un elenco di frasi.

Per estrarre le condizioni mediche:

1. Creare espressioni di esempio ed etichettare i termini medici all'interno di tali enunciazioni.
2. Creare un elenco di frasi con esempi dei termini all'interno del dominio dell'oggetto. Questo elenco di frasi deve includere il termine effettivo etichettato e altri termini che descrivono lo stesso concetto.
3. Aggiungere l'elenco di frasi all'entità o alla sottoentità che estrae il concetto usato nell'elenco di frasi. Lo scenario più comune è un componente (figlio) di un'entità di machine learning. Se l'elenco di frasi deve essere applicato a tutti gli Intent o alle entità, contrassegnare l'elenco di frasi come un elenco di frasi globali. Il flag **enabledForAllModels** controlla questo ambito del modello nell'API.

### <a name="token-matches-for-a-phrase-list"></a>Corrispondenze del token per un elenco di frasi

Un elenco di frasi viene sempre applicato a livello di token. La tabella seguente illustra in che modo un elenco di frasi con la parola **Ann** si applica alle varianti degli stessi caratteri nell'ordine specificato.


| Variazione del token di **Ann** | Corrispondenza dell'elenco di frasi quando viene trovato il token |
|--------------------------|---------------------------------------|
| **ANN**<br>**aNN**<br>           | Sì: il token è **Ann**                  |
| **Ann**                    | Sì: il token è **Ann**                  |
| **Anne**                     | Nessun token è **Anne**                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Un modello come funzionalità consente a un altro modello

È possibile aggiungere un modello (finalità o entità) come funzione a un altro modello (finalità o entità). Aggiungendo un Intent o un'entità esistente come funzionalità, si aggiunge un concetto ben definito con esempi con etichetta.

Quando si aggiunge un modello come funzionalità, è possibile impostare la funzionalità come:
* **[Obbligatorio](#required-features)**. È necessario trovare una funzionalità obbligatoria affinché il modello venga restituito dall'endpoint di stima.
* **[Globale](#global-features)**. Una funzionalità globale si applica all'intera app.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usare un'entità come funzionalità per finalità

Aggiungere un'entità come funzionalità a uno scopo quando il rilevamento di tale entità è significativo per lo scopo.

Ad esempio, se lo scopo è quello di prenotare un volo, ad esempio **BookFlight**, e l'entità è costituita da informazioni sui ticket, ad esempio il numero di postazioni, l'origine e la destinazione, la ricerca dell'entità Ticket-Information dovrebbe aggiungere un peso significativo alla stima dello scopo del **BookFlight** .

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usare un'entità come funzionalità per un'altra entità

Un'entità (A) deve essere aggiunta come caratteristica a un'altra entità (B) quando il rilevamento di tale entità (A) è significativo per la stima dell'entità (B).

Se, ad esempio, un'entità Address Shipping è contenuta in una sottoentità via-indirizzo, la ricerca della sottoentità Street-Address aggiunge un peso significativo alla stima per l'entità Address shipping.

* Indirizzo di spedizione (entità Machine Learning):

    * Numero civico (sottoentità)
    * Via (sottoentità)
    * Città (sottoentità)
    * Stato o provincia (sottoentità)
    * Paese/area geografica (sottoentità)
    * Cap (sottoentità)

## <a name="nested-subentities-with-features"></a>Sottoentità nidificate con funzionalità

Una sottoentità Machine Learning indica che è presente un concetto per l'entità padre. L'elemento padre può essere un'altra sottoentità o l'entità principale. Il valore della sottoentità funge da funzionalità per il relativo elemento padre.

Una sottoentità può includere sia un elenco di frasi sia un modello (un'altra entità) come una funzionalità.

Quando la sottoentità ha un elenco di frasi, aumenta il vocabolario del concetto, ma non aggiunge informazioni alla risposta JSON della stima.

Quando la sottoentità ha una funzionalità di un'altra entità, la risposta JSON include i dati estratti di quell'altra entità.


## <a name="required-features"></a>Funzionalità necessarie

È necessario trovare una funzionalità obbligatoria affinché il modello venga restituito dall'endpoint di stima. Usare una funzionalità obbligatoria quando si è certi che i dati in ingresso devono corrispondere alla funzionalità.

Se il testo dell'espressione non corrisponde alla funzionalità richiesta, non verrà estratto.

Una funzionalità obbligatoria usa un'entità non di apprendimento automatico:

* Entità di espressioni normali
* Entità elenco
* Entità predefinita

Se si è certi che il modello verrà trovato nei dati, impostare la funzionalità come richiesto. Una funzionalità obbligatoria non restituisce alcun valore se non viene trovata.

Continuando con l'esempio di indirizzo di spedizione:

Indirizzo di spedizione (entità Machine Learn)

 * Numero civico (sottoentità)
 * Via (sottoentità)
 * Nome via (sottoentità)
 * Città (sottoentità)
 * Stato o provincia (sottoentità)
 * Paese/area geografica (sottoentità)
 * Cap (sottoentità)

### <a name="required-feature-using-prebuilt-entities"></a>Funzionalità obbligatoria con le entità predefinite

Città, stato e paese/area geografica sono in genere un set di elenchi chiuso, ovvero non cambiano molto nel tempo. Queste entità potrebbero avere le funzionalità consigliate pertinenti e tali funzionalità potrebbero essere contrassegnate come obbligatorie. Ciò significa che l'intero indirizzo di spedizione non viene restituito se le entità che hanno le funzionalità richieste non sono state trovate.

Cosa accade se la città, lo stato o il paese/area geografica sono presenti nell'espressione, ma si trovano in una posizione o sono slang non previsti da LUIS? Se si vuole fornire una fase di post-elaborazione per facilitare la risoluzione dell'entità, a causa di un punteggio a bassa confidenza da LUIS, non contrassegnare la funzionalità come richiesto.

Un altro esempio di una funzionalità obbligatoria per l'indirizzo di spedizione è quello di rendere il numero di strada un numero obbligatorio e [precompilato](luis-reference-prebuilt-entities.md) . Questo consente a un utente di immettere "1 Microsoft Way" o "One Microsoft Way". Entrambi si risolvono nel numero "1" per la sottoentità numero civico.

### <a name="required-feature-using-list-entities"></a>Funzionalità obbligatoria con le entità elenco

Un' [entità elenco](reference-entity-list.md) viene utilizzata come elenco di nomi canonici insieme ai relativi sinonimi. Come funzionalità obbligatoria, se l'espressione non include il nome canonico o un sinonimo, l'entità non viene restituita come parte dell'endpoint di stima.

Si supponga che l'azienda venga fornita solo a un set limitato di paesi/aree geografiche. È possibile creare un'entità elenco che includa diversi modi per fare in modo che i clienti facciano riferimento al paese o all'area geografica. Se LUIS non trova una corrispondenza esatta all'interno del testo dell'espressione, l'entità, che ha la funzionalità richiesta dell'entità list, non viene restituita nella stima.

|Nome canonico|Sinonimi|
|--|--|
|Stati Uniti|U.S.<br>U. S. A<br>US<br>USA<br>0|

Un'applicazione client, ad esempio un bot di chat, può richiedere una domanda di completamento per la guida. Ciò consente al cliente di comprendere che la selezione del paese/area geografica è limitata e *obbligatoria*.

### <a name="required-feature-using-regular-expression-entities"></a>Funzionalità obbligatoria con entità di espressioni regolari

Un' [entità di espressione regolare](reference-entity-regular-expression.md) usata come funzionalità obbligatoria fornisce funzionalità di corrispondenza del testo avanzate.

Nell'esempio di indirizzo di spedizione è possibile creare un'espressione regolare che acquisisca le regole di sintassi dei codici postali del paese/area geografica.

## <a name="global-features"></a>Funzionalità globali

Sebbene l'uso più comune consiste nell'applicare una funzionalità a un modello specifico, è possibile configurare la funzionalità come **funzionalità globale** per applicarla all'intera applicazione.

L'uso più comune di una funzionalità globale è l'aggiunta di un vocabolario aggiuntivo all'app. Se, ad esempio, i clienti usano una lingua primaria, ma si prevede di poter usare un altro linguaggio all'interno della stessa espressione, è possibile aggiungere una funzionalità che includa parole della lingua secondaria.

Poiché l'utente prevede di usare la lingua secondaria in qualsiasi finalità o entità, aggiungere parole dalla lingua secondaria all'elenco di frasi. Configurare l'elenco di frasi come funzionalità globale.

## <a name="combine-features-for-added-benefit"></a>Combina le funzionalità per i vantaggi aggiunti

È possibile usare più di una funzionalità per descrivere un tratto o un concetto. Un'associazione comune consiste nell'usare:

* Funzionalità elenco frasi: è possibile usare più elenchi di frasi come funzionalità per lo stesso modello.
* Un modello come funzionalità: entità [predefinita](luis-reference-prebuilt-entities.md), entità di [espressioni regolari](reference-entity-regular-expression.md), entità [List](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Esempio: funzionalità dell'entità di prenotazione di ticket per un'app di viaggio  

Come esempio di base, si consideri un'app per la prenotazione di un volo con _finalità_ di prenotazione del volo e un' _entità_di prenotazione di ticket. L'entità prenotazione biglietti acquisisce le informazioni per prenotare un ticket aereo in un sistema di prenotazione. 

L'entità Machine Learning per il ticket-Book ha due sottoentità per l'acquisizione dell'origine e della destinazione. Le funzionalità devono essere aggiunte a ogni sottoentità, non all'entità di primo livello.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Schema di entità Ticketbooking":::

L'entità di prenotazione del ticket è un'entità di Machine Learning, con sottoentità, tra cui l' _origine_ e la _destinazione_. Entrambe le sottoentità indicano una posizione geografica. Per estrarre i percorsi e distinguere tra l' _origine_ e la _destinazione_, è necessario che ogni sottoentità disponga di funzionalità.

|Type|Sottoentità di origine |Sottoentità di destinazione|
|--|--|--|
|Modello come funzionalità|entità predefinita [geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3)|entità predefinita [geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3)|
|Elenco di frasi|**Parole di origine**: `start at` , `begin from` , `leave`|**Parole di destinazione**: `to` , `arrive` , `land at` , `go` , `going` , `stay` , `heading`|
|Elenco di frasi|Codici aeroportuali-stesso elenco per origine e destinazione|Codici aeroportuali-stesso elenco per origine e destinazione|
|Elenco di frasi|Nomi aeroportuali-stesso elenco per origine e destinazione|Codici aeroportuali-stesso elenco per origine e destinazione|

Se si prevede che gli utenti usino i codici aeroportuali e i nomi degli aeroporti, è necessario che LUIS disponga di elenchi di frasi che usano entrambi i tipi di frasi. I codici aeroportuali possono essere più comuni con il testo immesso in un chatbot, mentre i nomi degli aeroporti possono essere più comuni con la conversazione vocale, ad esempio un chatbot abilitato per la voce.

I dettagli corrispondenti delle funzionalità vengono restituiti solo per i modelli, non per gli elenchi di frasi perché solo i modelli vengono restituiti nel codice JSON di stima.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Assegnazione di etichette ai biglietti nella finalità

Dopo aver creato l'entità Machine Learning, è necessario aggiungere espressioni di esempio a un Intent ed etichettare l'entità padre e tutte le sottoentità.

Per l'esempio di prenotazione di ticket, etichettare le espressioni di esempio nello scopo con l' `TicketBooking` entità e le eventuali sottoentità nel testo.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Schema di entità Ticketbooking":::

### <a name="example-pizza-ordering-app"></a>Esempio: Pizza ordering app

Per un secondo esempio, si consideri un'app per un ristorante pizzeria che riceve gli ordini di pizza, inclusi i dettagli del tipo di pizza che un utente sta ordinando. Per completare l'elaborazione degli ordini, è necessario estrarre ogni dettaglio della pizza, se possibile.

L'entità Machine Learning in questo esempio è più complessa con le sottoentità nidificate, gli elenchi di frasi, le entità predefinite e le entità personalizzate.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Schema di entità Ticketbooking":::

In questo esempio vengono utilizzate le funzionalità a livello di sottoentità e figlio del livello di sottoentità. Il livello che ottiene il tipo di elenco di frasi o di modello come funzionalità è una parte importante della progettazione dell'entità.

Sebbene le sottoentità possano avere molti elenchi di frasi come funzionalità che consentono di rilevare l'entità, ogni sottoentità ha un solo modello come funzionalità. In questa [app](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)per la pizza questi modelli sono principalmente elenchi.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Schema di entità Ticketbooking":::

Le espressioni di esempio correttamente etichettate vengono visualizzate in modo da illustrare il modo in cui le entità sono annidate. 


## <a name="best-practices"></a>Procedure consigliate

Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* [Estendi](schema-change-prediction-runtime.md) i tuoi modelli di app al runtime di previsione.
* Vedere [aggiungere funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app Luis.
