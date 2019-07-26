---
title: Tipi di entità
titleSuffix: Language Understanding - Azure Cognitive Services
description: "Le entità estraggono i dati dall'espressione. I tipi di entità offrono un'estrazione prevedibile dei dati. Esistono due tipi di entità: Machine-learned e non-Machine-Learned. È importante individuare il tipo di entità che si sta utilizzando nelle espressioni."
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479121"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipi di entità e relativo scopo nel servizio LUIS

Le entità estraggono i dati dall'espressione. I tipi di entità offrono un'estrazione prevedibile dei dati. Esistono due tipi di entità: Machine-learned e non-Machine-Learned. È importante individuare il tipo di entità che si sta utilizzando nelle espressioni. 

## <a name="entity-compared-to-intent"></a>Entità e finalità

L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Un'espressione può includere molte entità oppure nessuna. Un'applicazione client può richiedere l'entità per eseguire la propria attività o usarla come guida di diverse opzioni da presentare all'utente. 

Entità:

* Rappresenta una classe che include una raccolta di oggetti simili (luoghi, elementi, persone, eventi o concetti). 
* Descrive le informazioni relative alla finalità


Ad esempio, un'app di ricerca di notizie potrebbe includere entità come "topic", "source", "keyword" e "publishing date", ovvero i dati chiave per la ricerca delle notizie. In un'app per la prenotazione di viaggi, "location", "date", "airline", "travel class" e "tickets" sono informazioni chiave per la prenotazione dei voli (rilevanti per la finalità "Bookflight").

La finalità rappresenta invece la previsione dell'intera espressione. 

## <a name="entities-help-with-data-extraction-only"></a>Le entità sono utili solo per l'estrazione dei dati

Le entità vengono etichettate o contrassegnate solo per la finalità di estrazione dell'entità; l'attività infatti non facilita la previsione della finalità.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati di cui si intende eseguire il pull dall'espressione. Può trattarsi di un nome, di una data, del nome di un prodotto o di un qualsiasi gruppo di parole. 

|Espressione|Entità|Data|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Location.Destination|3<br>New York|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>DatetimeV2 predefinito|New York<br>Londra<br>March 5, 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Le entità sono facoltative ma consigliate

A differenza delle finalità che sono obbligatorie, le entità sono facoltative. Non è necessario creare entità per ogni concetto nell'app, ma solo per quelli necessari all'applicazione client per eseguire un'azione. 

Se le espressioni non contengono dettagli che il bot deve continuare, non è necessario aggiungerle. Man mano che l'app evolve, sarà possibile aggiungerle in un secondo momento. 

In caso di dubbi su come usare le informazioni, aggiungere alcune entità predefinite comuni, ad esempio [datetimeV2](luis-reference-prebuilt-datetimev2.md), [ordinal](luis-reference-prebuilt-ordinal.md), [email](luis-reference-prebuilt-email.md) e [phone number](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Assegnare etichette per il significato delle parole

Se la scelta o la disposizione della parola è la stessa ma il significato è diverso, non etichettarla con le entità. 

Nelle espressioni seguenti, la parola `fair` è un omografo. L'ortografia è la stessa ma il significato è diverso:

|Espressione|
|--|
|Quali sono le fiere in programma nell'area di Seattle la prossima estate?|
|L'attuale classificazione per la recensione di Seattle è valida?|

Se si desidera che un'entità di evento trovi tutti i dati dell'evento, etichettare la parola `fair` nella prima espressione, ma non nella seconda.

## <a name="entities-are-shared-across-intents"></a>Le entità vengono condivise tra tutte le finalità

Le entità vengono condivise tra le finalità. Non appartengono a una singola finalità. Finalità ed entità possono essere associate semanticamente, ma non si tratta di una relazione esclusiva.

Nell'espressione "Book me a ticket to Paris", "Paris" è un'entità che fa riferimento a una località. Attraverso il riconoscimento delle entità che vengono menzionate nell'espressione dell'utente, LUIS aiuta l'applicazione client a scegliere le azioni specifiche da eseguire per soddisfare la richiesta dell'utente.

## <a name="mark-entities-in-none-intent"></a>Contrassegnare le entità nella finalità None (Nessuna)

Quando possibile, tutte le finalità, inclusa la finalità **None** (Nessuna), devono avere entità etichettate. Ciò consente a LUIS di apprendere maggiori informazioni sulla posizione in cui si trovano le entità nell'espressione e su quali parole circondano le entità. 

## <a name="entity-status-for-predictions"></a>Stato delle entità per le previsioni

Il portale di LUIS indica quando l'entità in un'espressione di esempio è diversa dall'entità contrassegnata o è troppo simile a un'altra entità e pertanto poco chiara. Questa condizione è indicata da una sottolineatura rossa nell'espressione di esempio. 

Per altre informazioni, vedere la sezione sulle [previsioni relative allo stato dell'entità](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Tipi di entità

LUIS offre numerosi tipi di entità. Scegliere l'entità in base a come devono essere estratti i dati e a come devono essere rappresentati dopo l'estrazione.

Le entità possono essere estratte tramite Machine Learning. In questo modo LUIS continua ad apprendere come l'entità viene visualizzata nell'espressione. Le entità possono essere estratte senza Machine Learning, con corrispondenza esatta del testo o di un'espressione regolare. Le entità nei criteri possono essere estratte con un approccio misto. 

Dopo aver estratto l'entità, i relativi dati possono essere rappresentati come una singola unità di informazioni o combinati con altre entità in modo da costituire un'unità di informazioni utilizzabile dall'applicazione client.

|Basata su Machine Learning|Può essere contrassegnata|Esercitazione|Esempio<br>Risposta|Tipo di entità|Scopo|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Composita**](#composite-entity)|Raggruppamento di entità, indipendentemente dal tipo di entità.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Elenco**](#list-entity)|Elenco di elementi e relativi sinonimi estratti con corrispondenza di testo esatta.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entità di cui è difficile determinare la fine.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Predefinita**](#prebuilt-entity)|Già sottoposta a training per estrarre vari tipi di dati.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Espressione regolare**](#regular-expression-entity)|Usa un'espressione regolare in base a cui trovare una corrispondenza di testo.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Semplice**](#simple-entity)|Contiene un singolo concetto in parole o frasi.|

Nelle espressioni di esempio è necessario contrassegnare solo le entità apprese dal computer. Le entità basate su Machine Learning funzionano meglio quando vengono testate tramite [query endpoint](luis-concept-test.md#endpoint-testing) e vengono [esaminate le espressioni endpoint](luis-how-to-review-endoint-utt.md). 

Le entità Pattern.any devono essere contrassegnate negli esempi di modello [Pattern](luis-how-to-model-intent-pattern.md), non negli esempi di finalità utente. 

Le entità miste usano una combinazione di metodi di rilevamento delle entità.

## <a name="machine-learned-entities-use-context"></a>Il contesto viene usato dalle entità apprese dal computer

Le entità apprese dal computer imparano dal contesto nell'espressione. In questo modo la variazione del posizionamento in espressioni di esempio è significativa. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Le entità non apprese dal computer non usano il contesto

Le seguenti entità non acquisite dal computer non prendono in considerazione il contesto di espressione quando le entità corrispondenti: 

* [Entità predefinite](#prebuilt-entity)
* [Entità Regex](#regular-expression-entity)
* [Elenca entità](#list-entity) 

Queste entità non richiedono l'assegnazione di etichette o il training del modello. Una volta aggiunta o configurata l'entità, le entità vengono estratte. Il compromesso è che queste entità possono essere sottoposte a overmatching, in cui se il contesto è stato preso in considerazione, la corrispondenza non è stata eseguita. 

Ciò accade con le entità elenco sui nuovi modelli di frequente. Il modello viene compilato e testato con un'entità di elenco, ma quando si pubblica il modello e si ricevono query dall'endpoint, si noterà che il modello è in fase di overmatching a causa della mancanza di contesto. 

Se si desidera trovare una corrispondenza di parole o frasi e prendere in considerazione il contesto, sono disponibili due opzioni. Il primo consiste nell'usare un'entità semplice abbinata a un elenco di frasi. L'elenco di frasi non verrà usato per la corrispondenza, ma consentirà di segnalare parole relativamente simili (elenco interscambiabile). Se è necessario avere una corrispondenza esatta anziché le varianti di un elenco di frasi, usare un'entità list con un ruolo, descritta di seguito.

### <a name="context-with-non-machine-learned-entities"></a>Contesto con entità non apprese dal computer

Se si desidera che il contesto dell'espressione sia rilevante per le entità non acquisite dal computer, è necessario utilizzare i [ruoli](luis-concept-roles.md).

Se si dispone di un'entità non appresa dal computer, ad esempio [entità predefinite](#prebuilt-entity), entità [Regex](#regular-expression-entity) o [elenco](#list-entity) di entità, che corrisponde al di fuori dell'istanza desiderata, provare a creare un'entità con due ruoli. Un ruolo acquisisce ciò che si sta cercando e un ruolo acquisisce ciò che non si sta cercando. Entrambe le versioni dovranno essere etichettate in espressioni di esempio.  

## <a name="composite-entity"></a>Entità composita

Un' [entità composita](reference-entity-composite.md) è costituita da altre entità, ad esempio entità predefinite, semplici, espressioni regolari ed elenchi. Le entità separate formano un'entità intera. 

## <a name="list-entity"></a>Entità elenco

Le [entità di elenco](reference-entity-list.md) rappresentano un set fisso e chiuso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint. 

## <a name="patternany-entity"></a>Entità pattern.any

[Pattern. any](reference-entity-pattern-any.md) è un segnaposto a lunghezza variabile usato solo nell'espressione di modello di un modello per contrassegnare l'inizio e la fine dell'entità.  

## <a name="prebuilt-entity"></a>Entità predefinita

Le entità predefinite sono tipi predefiniti che rappresentano concetti comuni, ad esempio indirizzo di posta elettronica, URL e numero di telefono. I nomi delle entità predefinite sono riservati. [Tutte le entità predefinite](luis-prebuilt-entities.md) che vengono aggiunte all'applicazione vengono restituite nella query di stima dell'endpoint se vengono individuate nell'espressione. 

Questa entità è idonea quando:

* I dati corrispondono a un caso d'uso comune supportato da entità predefinite per le impostazioni della lingua di destinazione. 

Le entità predefinite possono essere aggiunte e rimosse in qualsiasi momento.

![entità predefinita number](./media/luis-concept-entities/number-entity.png)

[Esercitazione](luis-tutorial-prebuilt-intents-entities.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#prebuilt-entity-data)

Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto. 

### <a name="troubleshooting-prebuilt-entities"></a>Risoluzione dei problemi relativi alle entità predefinite

Nel portale LUIS, se un'entità predefinita è contrassegnata al posto dell'entità personalizzata, sono disponibili alcune opzioni per risolvere il problema.

Verranno _sempre_ restituite le entità predefinite aggiunte all'app, anche se l'espressione deve estrarre entità personalizzate per lo stesso testo. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Modifica dell'entità con tag nell'espressione di esempio

Se l'entità predefinita è lo stesso testo o token dell'entità personalizzata, selezionare il testo nell'espressione di esempio e modificare l'espressione con tag. 

Se l'entità predefinita è contrassegnata con più testo o token rispetto all'entità personalizzata, sono disponibili due opzioni per risolvere il problema:

* [Rimuovi](#remove-example-utterance-to-fix-tagging) metodo enunciato di esempio
* [Rimuovi metodo di entità predefinito](#remove-prebuilt-entity-to-fix-tagging)

#### <a name="remove-example-utterance-to-fix-tagging"></a>Rimuovere l'espressione di esempio per correggere l'assegnazione di tag 

La prima scelta consiste nel rimuovere l'espressione di esempio. 

1. Eliminare l'espressione di esempio.
1. Ripetere il training dell'app. 
1. Aggiungere di nuovo solo la parola o la frase che rappresenta l'entità, contrassegnata come entità predefinita, come espressione di esempio completa. Per la parola o la frase sarà ancora stata contrassegnata l'entità precompilata. 
1. Selezionare l'entità nell'enunciato di esempio nella pagina **finalità** e passare all'entità personalizzata e eseguire di nuovo il training. Questo dovrebbe impedire a LUIS di contrassegnare questo testo esatto come entità predefinita in qualsiasi espressione di esempio che utilizza tale testo. 
1. Aggiungere nuovamente l'intero enunciato di esempio originale allo scopo. L'entità personalizzata deve continuare a essere contrassegnata al posto dell'entità precompilata. Se l'entità personalizzata non è contrassegnata, è necessario aggiungere altri esempi di tale testo nelle espressioni.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Rimuovere l'entità precompilata per correggere l'assegnazione di tag

1. Rimuovere l'entità predefinita dall'app. 
1. Nella pagina **finalità** contrassegnare l'entità personalizzata nell'espressione di esempio.
1. Eseguire il training dell'app.
1. Aggiungere di nuovo l'entità predefinita all'app ed eseguire il training dell'app. Questa correzione presuppone che l'entità predefinita non faccia parte di un'entità composita.

## <a name="regular-expression-entity"></a>Entità di espressione regolare 

Un' [entità di espressione regolare](reference-entity-regular-expression.md) estrae un'entità in base a un modello di espressione regolare fornito dall'utente.

## <a name="simple-entity"></a>Entità semplice

Un'[entità semplice](reference-entity-simple.md) è un valore appreso in modo automatico. Può trattarsi di una parola o di una frase.
## <a name="entity-limits"></a>Limiti delle entità

Esaminare i [limiti](luis-boundaries.md#model-boundaries) per comprendere il numero di entità di ciascun tipo che è possibile aggiungere a un modello.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se sono necessarie più entità rispetto al numero massimo 

Potrebbe essere necessario usare entità composite in combinazione con i ruoli di entità.

Le entità composite rappresentano parti di un intero. Ad esempio, un'entità composita denominata PlaneTicketOrder potrebbe presentare le entità figlio Airline, Destination, DepartureCity, DepartureDate e PlaneTicketClass.

LUIS fornisce anche il tipo di entità elenco non basato su Machine Learning ma che consente all'app LUIS di specificare un elenco fisso di valori. Vedere il riferimento [Limiti di LUIS](luis-boundaries.md) per esaminare i limiti del tipo di entità elenco. 

Se sono state prese in considerazione queste entità e non è ancora necessario il limite, contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide. 

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per altre informazioni sull'aggiunta di entità all'app LUIS.
