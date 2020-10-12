---
title: Tipi di entità-LUIS
description: Un'entità estrae i dati da un enunciato utente al runtime di stima. Uno scopo secondario _facoltativo_è quello di aumentare la stima della finalità o di altre entità utilizzando l'entità come una funzionalità.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316563"
---
# <a name="extract-data-with-entities"></a>Estrarre dati con entità

Un'entità estrae i dati da un enunciato utente al runtime di stima. Uno scopo secondario _facoltativo_è quello di aumentare la stima della finalità o di altre entità utilizzando l'entità come una funzionalità.

Esistono diversi tipi di entità:

* [entità Machine Learning](reference-entity-machine-learned-entity.md) : questa è l'entità primaria. È consigliabile progettare lo schema con questo tipo di entità prima di usare altre entità.
* Non Machine Learning usato come [funzionalità](luis-concept-feature.md) obbligatoria: per le corrispondenze esatte del testo, i criteri di ricerca o il rilevamento da entità predefinite
* [Pattern. any](#patternany-entity) : per estrarre il testo in formato libero, ad esempio i titoli dei libri da un [modello](reference-entity-pattern-any.md)

le entità di Machine Learning offrono la più ampia gamma di opzioni di estrazione dei dati. Le entità non di apprendimento automatico funzionano in base alla corrispondenza del testo e vengono usate come [funzionalità obbligatorie](#design-entities-for-decomposition) per un'entità o una finalità di machine learning.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati che si desidera estrarre dall'espressione, ad esempio nomi, date, nomi di prodotto o qualsiasi gruppo di parole significativo. Un'espressione può includere molte entità oppure nessuna. Un'applicazione client _può_ richiedere i dati per eseguire la relativa attività.

Le entità devono essere etichettate in modo coerente in tutte le espressioni di training per ogni finalità di un modello.

 È possibile definire entità personalizzate o usare entità predefinite per risparmiare tempo per i concetti comuni, ad esempio [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinale](luis-reference-prebuilt-ordinal.md), [e-mail](luis-reference-prebuilt-email.md)e [numero di telefono](luis-reference-prebuilt-phonenumber.md).

|Espressione|Entità|Dati|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Destination|3<br>New York|


### <a name="entities-are-optional-but-recommended"></a>Le entità sono facoltative ma consigliate

Sebbene siano necessari gli [Intent](luis-concept-intent.md) , le entità sono facoltative. Non è necessario creare entità per tutti i concetti nell'app, ma solo per quelli in cui l'applicazione client richiede i dati o l'entità funge da hint o segnale per un'altra entità o finalità.

Quando l'applicazione si sviluppa e viene individuata una nuova esigenza di dati, è possibile aggiungere le entità appropriate al modello LUIS in un secondo momento.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>L'entità rappresenta l'estrazione dei dati

L'entità rappresenta un concetto di dati _all'interno dell'espressione_. Una finalità classifica l' _intera espressione_.

Si considerino le quattro espressioni seguenti:

|Espressione|Finalità stimata|Entità estratte|Spiegazione|
|--|--|--|--|
|Guida|help|-|Nessun elemento da estrarre.|
|Invia qualcosa|sendSomething|-|Nessun elemento da estrarre. Il modello non dispone di una funzionalità obbligatoria da estrarre `something` in questo contesto e non è stato dichiarato alcun destinatario.|
|Invia Bob a presente|sendSomething|`Bob`, `present`|Il modello estrae `Bob` aggiungendo una funzionalità obbligatoria dell'entità predefinita `personName` . Per estrarre è stata usata un'entità Machine Learning `present` .|
|Invia Bob a box di cioccolato|sendSomething|`Bob`, `box of chocolates`|I due elementi importanti dei dati, `Bob` e `box of chocolates` , sono stati estratti dalle entità di machine learning.|

## <a name="label-entities-in-all-intents"></a>Etichettare entità in tutti gli Intent

Le entità estraggono i dati indipendentemente dalla finalità prevista. Assicurarsi di contrassegnare _tutte le_ espressioni di esempio in tutti gli Intent. Il `None` preventivo mancato contrassegno dell'entità causa confusione anche se sono state apportate più espressioni di training per gli altri scopi.

## <a name="design-entities-for-decomposition"></a>Progetta entità per la scomposizione

le entità di Machine Learning consentono di progettare lo schema dell'app per la scomposizione, suddividendo un concetto di grandi dimensioni in sottoentità.

La progettazione per la scomposizione consente a LUIS di restituire un livello di risoluzione approfondito dell'entità all'applicazione client. Ciò consente all'applicazione client di concentrarsi sulle regole business e lasciare la risoluzione dei dati a LUIS.

Un'entità di Machine Learning viene attivata in base al contesto acquisito tramite espressioni di esempio.

le [**entità di Machine Learning**](tutorial-machine-learned-entity.md) sono gli estrattori di primo livello. Le sottoentità sono entità figlio di entità di machine learning.

## <a name="effective-machine-learned-entities"></a>Entità di apprendimento automatico valide

Per compilare efficacemente le entità apprese dal computer:

* Le etichette devono essere coerenti tra gli Intent. Sono incluse anche le espressioni fornite nell'intento **None** che includono questa entità. In caso contrario, il modello non sarà in grado di determinare in modo efficace le sequenze.
* Se si dispone di un'entità Machine learned con sottoentità, assicurarsi che i diversi ordini e varianti delle entità e delle sottoentità siano presentati nelle espressioni con etichetta. Le espressioni di esempio con etichetta devono includere tutti i moduli validi e includere le entità che vengono visualizzate e sono assenti e riordinate all'interno dell'espressione.
* È consigliabile evitare di sovramontare le entità a un set molto fisso. L' **overfitting** si verifica quando il modello non generalizza correttamente ed è un problema comune nei modelli di machine learning. Ciò implica che l'app non funzionerà in modo adeguato ai nuovi dati. A sua volta, è necessario variare le espressioni di esempio con etichetta in modo che l'app possa generalizzare oltre gli esempi limitati forniti. È necessario variare le diverse sottoentità con un numero sufficiente di modifiche affinché il modello pensi a un maggior numero di concetti anziché solo agli esempi mostrati.

## <a name="effective-prebuilt-entities"></a>Entità predefinite valide

Per compilare entità efficaci che estraggono dati comuni, ad esempio quelli forniti dalle [entità predefinite](luis-reference-prebuilt-entities.md), è consigliabile eseguire la procedura seguente.

Migliorare l'estrazione dei dati portando i propri dati a un'entità come funzionalità. In questo modo, tutte le etichette aggiuntive dei dati apprenderanno il contesto in cui si trovano i nomi di persona nell'applicazione.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>Tipi di entità

Una sottoentità di un elemento padre deve essere un'entità di machine learning. La sottoentità può usare un'entità non machine learning come una [funzionalità](luis-concept-feature.md).

Scegliere l'entità in base a come devono essere estratti i dati e a come devono essere rappresentati dopo l'estrazione.

|Tipo di entità|Scopo|
|--|--|
|[**Apprendimento automatico**](tutorial-machine-learned-entity.md)|Estrae i dati annidati e complessi appresi dagli esempi con etichetta. |
|[**Elenco**](reference-entity-list.md)|Elenco di elementi e relativi sinonimi estratti con la **corrispondenza esatta del testo**.|
|[**Pattern. Any**](#patternany-entity)|Entità in cui la ricerca della fine dell'entità è difficile da determinare perché l'entità è in formato libero. Disponibile solo nei [modelli](luis-concept-patterns.md).|
|[**Predefinita**](luis-reference-prebuilt-entities.md)|È già stato eseguito il training per estrarre un tipo specifico di dati, ad esempio URL o posta elettronica. Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto.|
|[**Espressione regolare**](reference-entity-regular-expression.md)|Usa l'espressione regolare per la **corrispondenza esatta del testo**.|


## <a name="extraction-versus-resolution"></a>Estrazione rispetto alla risoluzione

Le entità estraggono i dati quando i dati vengono visualizzati nell'espressione. Le entità non modificano o non risolvono i dati. L'entità non fornirà alcuna soluzione se il testo è un valore valido per l'entità.

Ci sono modi per portare la risoluzione nell'estrazione, ma è necessario tenere presente che questo limita la capacità dell'app di essere immune a variazioni ed errori.

Le entità elenco ed espressioni regolari (corrispondenti al testo) possono essere utilizzate come [funzionalità necessarie](luis-concept-feature.md#required-features) per una sottoentità e che funge da filtro per l'estrazione. È consigliabile usarlo con cautela per non ostacolare la capacità dell'app di prevedere.

## <a name="extracting-contextually-related-data"></a>Estrazione di dati correlati al contesto

Un'espressione può contenere due o più occorrenze di un'entità in cui il significato dei dati è basato sul contesto all'interno dell'espressione. Un esempio è un enunciato per la prenotazione di un volo con due posizioni geografiche, Origin e Destination.

`Book a flight from Seattle to Cairo`

È necessario estrarre le due posizioni in modo che l'applicazione client conosca il tipo di ogni località per completare l'acquisto del ticket.

Per estrarre l'origine e la destinazione, creare due sottoentità come parte dell'entità di Machine Learning Order Order. Per ognuna delle sottoentità, creare una funzionalità obbligatoria che usa geographyV2.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>Uso delle funzionalità necessarie per vincolare le entità

Altre informazioni sulle [funzionalità necessarie](luis-concept-feature.md)

## <a name="patternany-entity"></a>Entità pattern.any

Modello. Any è disponibile solo in un [modello](luis-concept-patterns.md).

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>Superamento dei limiti delle app per le entità

Se è necessario più del [limite](luis-limits.md#model-limits), contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/).

## <a name="entity-prediction-status-and-errors"></a>Stato di stima dell'entità ed errori

Il portale LUIS Mostra quando l'entità ha una stima di entità diversa rispetto all'entità selezionata per un esempio di espressione. Questo punteggio diverso si basa sul modello attualmente sottoposto a training. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Il portale LUIS Mostra quando l'entità ha una stima di entità diversa rispetto all'entità selezionata per un esempio di espressione.":::

Il testo di errore viene evidenziato all'interno dell'espressione di esempio e la riga dell'espressione di esempio ha un indicatore di errore a destra, visualizzato come triangolo rosso. 

Utilizzare queste informazioni per risolvere gli errori dell'entità utilizzando uno o più degli elementi seguenti:
* Il testo evidenziato viene erroneamente etichettato. Per correggere, rivedere, correggere e ripetere il training. 
* Creare una [funzionalità](luis-concept-feature.md) per l'entità per facilitare l'identificazione del concetto dell'entità
* Aggiungere altre [espressioni di esempio](luis-concept-utterance.md) ed etichetta con l'entità
* [Esaminare i suggerimenti di apprendimento attivi](luis-concept-review-endpoint-utterances.md) per eventuali espressioni ricevute nell'endpoint di stima che consentono di identificare il concetto dell'entità.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide.

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.

Per informazioni su come estrarre i dati strutturati da un enunciato usando l'entità Machine Learning, vedere [esercitazione: estrarre dati strutturati da un enunciato utente con entità di Machine Learning in Language Understanding (Luis)](tutorial-machine-learned-entity.md) .

