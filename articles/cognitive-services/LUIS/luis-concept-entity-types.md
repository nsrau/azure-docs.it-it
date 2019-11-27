---
title: Tipi di entità-LUIS
titleSuffix: Azure Cognitive Services
description: "Le entità estraggono i dati dall'espressione. I tipi di entità offrono un'estrazione prevedibile dei dati. Esistono due tipi di entità: Machine-learned e non-Machine-Learned. È importante individuare il tipo di entità che si sta utilizzando nelle espressioni."
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 808e110ccb45b0b4f7bf34a43597c1f7a7bc0fed
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422575"
---
# <a name="entities-and-their-purpose-in-luis"></a>Entità e relativo scopo in LUIS

Lo scopo principale delle entità è fornire all'applicazione client l'estrazione dei dati prevedibile. Uno scopo _facoltativo_, secondario è quello di aumentare la stima della finalità o di altre entità con i descrittori.

Esistono due tipi di entità:

* dal contesto di apprendimento automatico
* non-Machine-Learned: per le corrispondenze esatte del testo, i criteri di ricerca o il rilevamento da entità predefinite

Le entità apprese dal computer forniscono la più ampia gamma di opzioni di estrazione dei dati. Le entità non apprese dal computer funzionano in base alla corrispondenza del testo e possono essere usate in modo indipendente o come [vincolo](#design-entities-for-decomposition) in un'entità appresa dal computer.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati che si desidera estrarre dall'espressione, ad esempio nomi, date, nomi di prodotto o qualsiasi gruppo di parole significativo. Un'espressione può includere molte entità oppure nessuna. Un'applicazione client _può_ richiedere i dati per eseguire la relativa attività.

Le entità devono essere etichettate in modo coerente in tutte le espressioni di training per ogni finalità di un modello.

 È possibile definire entità personalizzate o usare entità predefinite per risparmiare tempo per i concetti comuni, ad esempio [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinale](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md)e [numero di telefono](luis-reference-prebuilt-phonenumber.md).

|Espressione|Entità|Dati|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Location.Destination|3<br>New York|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>DatetimeV2 predefinito|New York<br>Londra<br>March 5, 2018|

### <a name="entities-are-optional"></a>Le entità sono facoltative

A differenza delle finalità che sono obbligatorie, le entità sono facoltative. Non è necessario creare entità per ogni concetto nell'app, ma solo per quelli necessari all'applicazione client per eseguire un'azione.

Se le espressioni non dispongono di dati richiesti dall'applicazione client, non è necessario aggiungere entità. Quando l'applicazione si sviluppa e viene individuata una nuova esigenza di dati, è possibile aggiungere le entità appropriate al modello LUIS in un secondo momento.

## <a name="entity-compared-to-intent"></a>Entità e finalità

L'entità rappresenta un concetto di dati all'interno dell'espressione che si desidera estrarre.

Un enunciato può includere facoltativamente entità. Per confronto, la stima dello scopo di un enunciato è _obbligatoria_ e rappresenta l'intera espressione. LUIS richiede che le espressioni di esempio siano contenute in un Intent.

Si considerino le 4 espressioni seguenti:

|Espressione|Finalità stimata|Entità estratte|Spiegazione|
|--|--|--|--|
|Guida|help|-|Nessun elemento da estrarre.|
|Invia qualcosa|sendSomething|-|Nessun elemento da estrarre. Non è stato eseguito il training del modello per estrarre `something` in questo contesto e non esiste alcun destinatario.|
|Invia Bob a presente|sendSomething|`Bob`, `present`|È stato eseguito il training del modello con l'entità predefinita [PersonName](luis-reference-prebuilt-person.md) , che ha estratto il nome `Bob`. Per estrarre `present`è stata usata un'entità Machine-Learned.|
|Invia Bob a box di cioccolato|sendSomething|`Bob`, `box of chocolates`|Le due parti importanti dei dati, `Bob` e le `box of chocolates`, sono state estratte dalle entità.|

## <a name="design-entities-for-decomposition"></a>Progetta entità per la scomposizione

Si tratta di una progettazione di entità efficace che rende l'entità di livello superiore un'entità appresa dal computer. Questo consente di modificare la progettazione dell'entità nel tempo e l'utilizzo di **sottocomponenti** (entità figlio), facoltativamente con i **vincoli** e i **descrittori**, per scomporre l'entità di primo livello nelle parti richieste dall'applicazione client.

La progettazione per la scomposizione consente a LUIS di restituire un livello di risoluzione approfondito dell'entità all'applicazione client. Ciò consente all'applicazione client di concentrarsi sulle regole business e lasciare la risoluzione dei dati a LUIS.

### <a name="machine-learned-entities-are-primary-data-collections"></a>Le entità apprese dal computer sono raccolte dati primarie

Le [**entità apprese dal computer**](tutorial-machine-learned-entity.md) sono l'unità dati di primo livello. I sottocomponenti sono entità figlio di entità apprese dal computer.

Trigger di entità appresa dal computer in base al contesto acquisito tramite espressioni di training. I **vincoli** sono regole facoltative applicate a un'entità appresa dal computer che vincola ulteriormente l'attivazione in base alla definizione di corrispondenza esatta del testo di un'entità non appresa dal computer, ad esempio un [elenco](reference-entity-list.md) o un' [espressione regolare](reference-entity-regular-expression.md). Ad esempio, un'entità `size` Machine-learned può avere un vincolo di un'entità `sizeList` list che vincola l'entità `size` a essere attivata solo quando vengono rilevati valori contenuti all'interno dell'entità `sizeList`.

I [**descrittori**](luis-concept-feature.md) sono funzionalità applicate per aumentare la pertinenza delle parole o delle frasi per la stima. Sono denominati *descrittori* perché vengono usati per *descrivere* uno scopo o un'entità. I descrittori descrivono la distinzione tra tratti o attributi dei dati, ad esempio parole o frasi importanti. che LUIS osserva e apprende.

Quando si crea una funzionalità di elenco di frasi nell'app LUIS, questa viene abilitata a livello globale per impostazione predefinita e si applica in modo uniforme a tutti gli Intent ed entità. Tuttavia, se si applica l'elenco di frasi come descrittore (funzionalità) di un'entità appresa dal computer (o *modello*), l'ambito viene ridotto in modo da applicare solo a tale modello e non viene più utilizzato con tutti gli altri modelli. L'utilizzo di un elenco di frasi come descrittore per un modello consente la scomposizione mediante l'assistenza con l'accuratezza del modello a cui viene applicato.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipi di entità

Scegliere l'entità in base a come devono essere estratti i dati e a come devono essere rappresentati dopo l'estrazione.

|Tipo di entità|Scopo|
|--|--|
|[**Apprendimento automatico**](tutorial-machine-learned-entity.md)|Le entità apprese dal computer imparano dal contesto nell'espressione. Raggruppamento padre di entità, indipendentemente dal tipo di entità. In questo modo la variazione del posizionamento in espressioni di esempio è significativa. |
|[**Elenco**](reference-entity-list.md)|Elenco di elementi e relativi sinonimi estratti con la **corrispondenza esatta del testo**.|
|[**Pattern.any**](reference-entity-pattern-any.md)|Entità di cui è difficile determinare la fine. |
|[**Predefinita**](luis-reference-prebuilt-entities.md)|È già stato eseguito il training per estrarre un tipo specifico di dati, ad esempio URL o posta elettronica. Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto.|
|[**Espressione regolare**](reference-entity-regular-expression.md)|Usa l'espressione regolare per la **corrispondenza esatta del testo**.|

## <a name="extracting-contextually-related-data"></a>Estrazione di dati correlati al contesto

Un'espressione può contenere due o più occorrenze di un'entità in cui il significato dei dati è basato sul contesto all'interno dell'espressione. Un esempio è un enunciato per la prenotazione di un volo con due località, Origin e Destination.

`Book a flight from Seattle to Cairo`

È necessario estrarre i due esempi di un'entità `location`. Per completare l'acquisto del ticket, l'applicazione client deve conoscerne il tipo di posizione.

Sono disponibili due tecniche per l'estrazione dei dati correlati al contesto:

 * L'entità `location` è un'entità appresa dal computer e usa due entità sottocomponenti per acquisire i `origin` e `destination` (scelta consigliata)
 * L'entità `location` utilizza due **ruoli** di `origin` e `destination`

Più entità possono esistere in un enunciato e possono essere estratte senza usare la scomposizione o i ruoli se il contesto in cui vengono usati non ha alcun significato. Se, ad esempio, l'espressione include un elenco di percorsi, `I want to travel to Seattle, Cairo, and London.`, si tratta di un elenco in cui ogni elemento non ha un significato aggiuntivo.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>Uso di entità sottocomponenti di un'entità appresa dal computer per la definizione del contesto

È possibile usare un' [**entità appresa dal computer**](tutorial-machine-learned-entity.md) per estrarre i dati che descrivono l'azione di prenotazione di un volo e quindi scomporre l'entità di primo livello nelle parti separate richieste dall'applicazione client.

In questo esempio `Book a flight from Seattle to Cairo`, l'entità di livello superiore potrebbe essere `travelAction` ed essere etichettata per estrarre `flight from Seattle to Cairo`. Vengono quindi create due entità sottocomponenti, denominate `origin` e `destination`, con un vincolo applicato dall'entità `geographyV2` predefinita. Negli enunciati di training, il `origin` e il `destination` sono etichettati in modo appropriato.

### <a name="using-entity-role-to-define-context"></a>Uso del ruolo entità per la definizione del contesto

Un ruolo è un alias denominato per un'entità in base al contesto all'interno dell'espressione. Un ruolo può essere usato con qualsiasi tipo di entità predefinita o personalizzata, e può essere usato sia nelle espressioni che nei modelli di esempio. In questo esempio, l'entità `location` necessita di due ruoli di `origin` e `destination` ed entrambi devono essere contrassegnati nelle espressioni di esempio.

Se LUIS trova il `location` ma non riesce a determinare il ruolo, l'entità location viene ancora restituita. L'applicazione client dovrà completare una domanda per determinare il tipo di posizione che l'utente ha significato.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se sono necessarie più entità rispetto al numero massimo

Se è necessario più del limite, contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stato stima entità

Il portale LUIS Mostra quando l'entità, in un enunciato di esempio, ha una stima di entità diversa rispetto all'entità selezionata. Questo punteggio diverso si basa sul modello attualmente sottoposto a training.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide.

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.

Per informazioni su come estrarre i dati strutturati da un enunciato usando l'entità di Machine Learning, vedere [esercitazione: estrarre dati strutturati da un enunciato utente con entità apprese dal computer in Language Understanding (Luis)](tutorial-machine-learned-entity.md) .
