---
title: Tipi di entità-LUIS
description: Un'entità estrae i dati da un enunciato utente al runtime di stima. Uno scopo secondario _facoltativo_è quello di aumentare la stima della finalità o di altre entità utilizzando l'entità come una funzionalità.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585641"
---
# <a name="extract-data-with-entities"></a>Estrarre dati con entità

Un'entità estrae i dati da un enunciato utente al runtime di stima. Uno scopo secondario _facoltativo_è quello di aumentare la stima della finalità o di altre entità utilizzando l'entità come una funzionalità.

Esistono diversi tipi di entità:

* [Entità basata su Machine Learning](reference-entity-machine-learned-entity.md)
* Non-Machine-learned usato come [funzionalità](luis-concept-feature.md) obbligatoria: per le corrispondenze esatte del testo, i criteri di ricerca o il rilevamento da entità predefinite
* [Pattern. any](#patternany-entity) : per estrarre il testo in formato libero, ad esempio i titoli dei libri da un [modello](reference-entity-pattern-any.md)

Le entità apprese dal computer forniscono la più ampia gamma di opzioni di estrazione dei dati. Le entità non apprese dal computer funzionano in base ai criteri di ricerca del testo e vengono usate come [funzionalità obbligatorie](#design-entities-for-decomposition) per un'entità o finalità appresa dal computer.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati che si desidera estrarre dall'espressione, ad esempio nomi, date, nomi di prodotto o qualsiasi gruppo di parole significativo. Un'espressione può includere molte entità oppure nessuna. Un'applicazione client _può_ richiedere i dati per eseguire la relativa attività.

Le entità devono essere etichettate in modo coerente in tutte le espressioni di training per ogni finalità di un modello.

 È possibile definire entità personalizzate o usare entità predefinite per risparmiare tempo per i concetti comuni, ad esempio [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinale](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md)e [numero di telefono](luis-reference-prebuilt-phonenumber.md).

|Espressione|Entità|Data|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Destination|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Le entità sono facoltative ma consigliate

Sebbene siano necessari gli [Intent](luis-concept-intent.md) , le entità sono facoltative. Non è necessario creare entità per tutti i concetti nell'app, ma solo per quelli in cui l'applicazione client richiede i dati o l'entità funge da hint o segnale per un'altra entità o finalità.

Quando l'applicazione si sviluppa e viene individuata una nuova esigenza di dati, è possibile aggiungere le entità appropriate al modello LUIS in un secondo momento.

## <a name="entity-compared-to-intent"></a>Entità e finalità

L'entità rappresenta un concetto di dati _all'interno dell'espressione_. Una finalità classifica l' _intera espressione_.

Si considerino le quattro espressioni seguenti:

|Espressione|Finalità stimata|Entità estratte|Spiegazione|
|--|--|--|--|
|Guida|help|-|Nessun elemento da estrarre.|
|Invia qualcosa|sendSomething|-|Nessun elemento da estrarre. Il modello non dispone di una funzionalità obbligatoria da estrarre `something` in questo contesto e non è stato dichiarato alcun destinatario.|
|Invia Bob a presente|sendSomething|`Bob`, `present`|Il modello estrae `Bob` aggiungendo una funzionalità obbligatoria dell'entità predefinita `personName` . Per estrarre è stata usata un'entità appresa dal computer `present` .|
|Invia Bob a box di cioccolato|sendSomething|`Bob`, `box of chocolates`|I due elementi importanti dei dati, `Bob` e `box of chocolates` , sono stati estratti dalle entità apprese dal computer.|

## <a name="design-entities-for-decomposition"></a>Progetta entità per la scomposizione

Le entità acquisite dal computer consentono di progettare lo schema dell'app per la scomposizione, suddividendo un concetto di grandi dimensioni in sottoentità.

La progettazione per la scomposizione consente a LUIS di restituire un livello di risoluzione approfondito dell'entità all'applicazione client. Ciò consente all'applicazione client di concentrarsi sulle regole business e lasciare la risoluzione dei dati a LUIS.

Trigger di entità appresa dal computer in base al contesto appreso tramite espressioni di esempio.

Le [**entità apprese dal computer**](tutorial-machine-learned-entity.md) sono gli estrattori di primo livello. Le sottoentità sono entità figlio di entità apprese dal computer.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipi di entità

Una sottoentità di un elemento padre deve essere un'entità appresa dal computer. La sottoentità può utilizzare come [funzionalità](luis-concept-feature.md)un'entità non appresa dal computer.

Scegliere l'entità in base a come devono essere estratti i dati e a come devono essere rappresentati dopo l'estrazione.

|Tipo di entità|Scopo|
|--|--|
|[**Apprendimento automatico**](tutorial-machine-learned-entity.md)|Estrae i dati annidati e complessi appresi dagli esempi con etichetta. |
|[**Elenco**](reference-entity-list.md)|Elenco di elementi e relativi sinonimi estratti con la **corrispondenza esatta del testo**.|
|[**Pattern. Any**](#patternany-entity)|Entità in cui la ricerca della fine dell'entità è difficile da determinare perché l'entità è in formato libero. Disponibile solo nei [modelli](luis-concept-patterns.md).|
|[**Predefinita**](luis-reference-prebuilt-entities.md)|È già stato eseguito il training per estrarre un tipo specifico di dati, ad esempio URL o posta elettronica. Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto.|
|[**Espressione regolare**](reference-entity-regular-expression.md)|Usa l'espressione regolare per la **corrispondenza esatta del testo**.|

## <a name="extracting-contextually-related-data"></a>Estrazione di dati correlati al contesto

Un'espressione può contenere due o più occorrenze di un'entità in cui il significato dei dati è basato sul contesto all'interno dell'espressione. Un esempio è un enunciato per la prenotazione di un volo con due posizioni geografiche, Origin e Destination.

`Book a flight from Seattle to Cairo`

È necessario estrarre le due posizioni in modo che l'applicazione client conosca il tipo di ogni località per completare l'acquisto del ticket.

Per estrarre l'origine e la destinazione, creare due sottoentità come parte dell'entità del ticket order Machine-Learned. Per ognuna delle sottoentità, creare una funzionalità obbligatoria che usa geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Uso delle funzionalità necessarie per vincolare le entità

Altre informazioni sulle [funzionalità necessarie](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entità pattern.any

Modello. Any è disponibile solo in un [modello](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Superamento dei limiti delle app per le entità

Se è necessario più del [limite](luis-limits.md#model-limits), contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status"></a>Stato stima entità

Il portale LUIS Mostra quando l'entità ha una stima di entità diversa rispetto all'entità selezionata per un esempio di espressione. Questo punteggio diverso si basa sul modello attualmente sottoposto a training. Utilizzare queste informazioni per risolvere gli errori di training utilizzando uno o più degli elementi seguenti:
* Creare una [funzionalità](luis-concept-feature.md) per l'entità per facilitare l'identificazione del concetto dell'entità
* Aggiungere altre [espressioni di esempio](luis-concept-utterance.md) ed etichetta con l'entità
* [Esaminare i suggerimenti di apprendimento attivi](luis-concept-review-endpoint-utterances.md) per eventuali espressioni ricevute nell'endpoint di stima che consentono di identificare il concetto dell'entità.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide.

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.

Per informazioni su come estrarre i dati strutturati da un enunciato usando l'entità di Machine Learning, vedere [esercitazione: estrarre dati strutturati da un enunciato utente con entità apprese dal computer in Language Understanding (Luis)](tutorial-machine-learned-entity.md) .

