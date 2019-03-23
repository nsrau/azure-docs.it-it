---
title: Tipi di entità
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Entità di estrarre dati dal utterance. Tipi di entità offrono stimabile estrazione dei dati. Esistono due tipi di entità: macchina-ottenute, non-machine. È importante sapere quale tipo di entità che si sta lavorando in espressioni.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: diberry
ms.openlocfilehash: d12ea20f9f510b0e2d3d3512d8d8c71a3fb96eec
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372523"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Tipi di entità e relativo scopo nel servizio LUIS

Entità di estrarre dati dal utterance. Tipi di entità offrono stimabile estrazione dei dati. Esistono due tipi di entità: macchina-ottenute, non-machine. È importante sapere quale tipo di entità che si sta lavorando in espressioni. 

## <a name="entity-compared-to-intent"></a>Entità e finalità

L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Un'espressione può includere molte entità oppure nessuna. Un'entità rappresenta una classe che include una raccolta di oggetti simili (luoghi, cose, persone, eventi o concetti). Le entità descrivono le informazioni rilevanti per la finalità e sono talvolta essenziali affinché l'app svolga la sua attività. Ad esempio, un'app di ricerca di notizie potrebbe includere entità come "topic", "source", "keyword" e "publishing date", ovvero i dati chiave per la ricerca delle notizie. In un'app per la prenotazione di viaggi, "location", "date", "airline", "travel class" e "tickets" sono informazioni chiave per la prenotazione dei voli (rilevanti per la finalità "Bookflight").

La finalità rappresenta invece la previsione dell'intera espressione. 

## <a name="entities-help-with-data-extraction-only"></a>Le entità sono utili solo per l'estrazione dei dati

Le entità vengono etichettate o contrassegnate solo per la finalità di estrazione dell'entità; l'attività infatti non facilita la previsione della finalità.

## <a name="entities-represent-data"></a>Le entità rappresentano dati

Le entità sono dati di cui si intende eseguire il pull dall'espressione. Può trattarsi di un nome, di una data, del nome di un prodotto o di un qualsiasi gruppo di parole. 

|Espressione|Entità|Dati|
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
|✔|✔|[✔](luis-quickstart-intent-and-hier-entity.md)|[✔](luis-concept-data-extraction.md#hierarchical-entity-data)|[**Gerarchica**](#hierarchical-entity)|Raggruppamento di entità semplici.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Elenco**](#list-entity)|Elenco di elementi e relativi sinonimi estratti con corrispondenza di testo esatta.|
|Mista||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|Entità di cui è difficile determinare la fine.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Predefinita**](#prebuilt-entity)|Già sottoposta a training per estrarre vari tipi di dati.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Espressione regolare**](#regular-expression-entity)|Usa un'espressione regolare in base a cui trovare una corrispondenza di testo.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Semplice**](#simple-entity)|Contiene un singolo concetto in parole o frasi.|

Solo le entità basate su Machine Learning devono essere contrassegnate nelle espressioni di esempio per ogni finalità. Le entità basate su Machine Learning funzionano meglio quando vengono testate tramite [query endpoint](luis-concept-test.md#endpoint-testing) e vengono [esaminate le espressioni endpoint](luis-how-to-review-endoint-utt.md). 

Le entità Pattern.any devono essere contrassegnate negli esempi di modello [Pattern](luis-how-to-model-intent-pattern.md), non negli esempi di finalità utente. 

Le entità miste usano una combinazione di metodi di rilevamento delle entità.

## <a name="composite-entity"></a>Entità composita

Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressione regolare, elenco, gerarchiche. Le entità separate formano un'entità intera. 

Questa entità è idonea quando i dati:

* Sono correlati tra loro. 
* Sono correlati tra loro nel contesto dell'espressione.
* Usano vari tipi di entità.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.
* Hanno una serie di espressioni utente che richiedono l'apprendimento automatico.

![entità composita](./media/luis-concept-entities/composite-entity.png)

[Esercitazione](luis-tutorial-composite-entity.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="hierarchical-entity"></a>Entità gerarchica

Un'entità gerarchica è una categoria di entità semplici con apprendimento su base contestuale chiamate entità figlio.

Questa entità è idonea quando i dati:

* Sono entità semplici.
* Sono correlati tra loro nel contesto dell'espressione.
* Usano parole specifiche per indicare ogni entità figlio. Le parole usate sono, ad esempio, from/to, leaving/headed to, away from/toward.
* Le entità figlio si trovano spesso nella stessa espressione. 
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

Non usare se:

* È necessaria un'entità che abbia corrispondenze di testo esatte per gli elementi figlio, indipendentemente dal contesto. Usare invece un'[entità elenco](#list-entity). 
* È necessaria un'entità per una relazione padre-figlio con altri tipi di entità. Usare un'[entità composita](#composite-entity).

![entità gerarchica](./media/luis-concept-entities/hierarchical-entity.png)

[Esercitazione](luis-quickstart-intent-and-hier-entity.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#hierarchical-entity-data)<br>

### <a name="roles-versus-hierarchical-entities"></a>Ruoli ed entità gerarchiche

I [Ruoli](luis-concept-roles.md#roles-versus-hierarchical-entities) di un criterio risolvono lo stesso problema delle entità gerarchiche ma si applicano a tutti i tipi di entità. I ruoli sono attualmente disponibili solo nei criteri. Non sono disponibili nelle espressioni di esempio della finalità.  

## <a name="list-entity"></a>Entità elenco

Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi. LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente. Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint. 

Questa entità è idonea quando i dati di testo:

* Sono un set noto.
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza esatta con un sinonimo o il nome canonico. LUIS non usa l'elenco di là delle corrispondenze esatte del testo. Stemming, plurali e altre varianti non vengono risolti con un'entità elenco. Per gestire le variazioni, è consigliabile usare un [criterio](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) con la sintassi del testo facoltativo.

![entità elenco](./media/luis-concept-entities/list-entity.png)

[Esercitazione](luis-quickstart-intent-and-list-entity.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Entità pattern.any

Pattern.any è un segnaposto di lunghezza variabile usato solo nell'espressione del modello del criterio per contrassegnare l'inizio e la fine dell'entità.  

Questa entità è idonea quando:

* La fine dell'entità può essere confusa con il testo rimanente dell'espressione. 
[Esercitazione](luis-tutorial-pattern.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#patternany-entity-data)

**Esempio**  
Data un'applicazione client che esegue la ricerca di libri in base al titolo, l'entità pattern.any estrae il titolo completo. Un'espressione di criterio con pattern.any per questa ricerca è `Was {BookTitle} written by an American this year[?]`. 

Nella tabella seguente, ogni riga presenta due versioni dell'espressione. L'espressione superiore è quella visualizzata inizialmente da LUIS nella quale non è chiaro dove il titolo del libro inizia e finisce. L'espressione inferiore mostra come LUIS riesce a individuare il titolo del libro quando è stato definito un criterio per l'estrazione. 

|Espressione|
|--|
|Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?<br>Was **The Man Who Mistook His Wife for a Hat and Other Clinical Tales** written by an American this year?|
|Was Half Asleep in Frog Pajamas written by an American this year?<br>Was **Half Asleep in Frog Pajamas** written by an American this year?|
|Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?<br>Was **The Particular Sadness of Lemon Cake: A Novel** written by an American this year?|
|Was There's A Wocket In My Pocket! written by an American this year?<br>Was **There's A Wocket In My Pocket!** written by an American this year?|

## <a name="prebuilt-entity"></a>Entità predefinita

Le entità predefinite sono tipi predefiniti che rappresentano concetti comuni, ad esempio indirizzo di posta elettronica, URL e numero di telefono. I nomi delle entità predefinite sono riservati. [Tutte le entità predefinite](luis-prebuilt-entities.md) che vengono aggiunte all'applicazione vengono restituite nella query di stima dell'endpoint se vengono individuate nell'espressione. 

Questa entità è idonea quando:

* I dati corrispondono a un caso d'uso comune supportato da entità predefinite per le impostazioni della lingua di destinazione. 

Le entità predefinite possono essere aggiunte e rimosse in qualsiasi momento.

![entità predefinita number](./media/luis-concept-entities/number-entity.png)

[Esercitazione](luis-tutorial-prebuilt-intents-entities.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#prebuilt-entity-data)

Alcune di queste entità predefinite sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto. 

### <a name="troubleshooting-prebuilt-entities"></a>Risoluzione dei problemi relativi a entità predefinite

Nel portale di LUIS, viene contrassegnata un'entità predefinite anziché all'entità personalizzata, sono disponibili alcune opzioni come risolvere il problema.

Le entità predefinite aggiunte all'app verranno _sempre_ restituiti, anche se il utterance deve estrarre le entità personalizzate per il testo stesso. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Modificare le entità con tag in utterance di esempio

Se l'entità predefinito è lo stesso testo o i token dell'entità personalizzate, selezionare il testo utterance di esempio e modificare il utterance con tag. 

Se l'entità predefinito viene contrassegnato con più testo o un token rispetto all'entità personalizzata, sono disponibili un paio di opzioni di come risolvere il problema:

* [Rimuovere utterance esempio](#remove-example-utterance-to-fix-tagging) (metodo)
* [Rimuovi entità predefinite](#remove-prebuilt-entity-to-fix-tagging) (metodo)

#### <a name="remove-example-utterance-to-fix-tagging"></a>Rimuovere utterance di esempio per correggere l'assegnazione di tag 

Il metodo preferito consiste nell'eliminare il utterance di esempio e ripetere il training dell'app. Aggiungere di nuovo solo la parola o una frase che rappresenta l'entità come utterance un esempio, quindi contrassegna l'entità e il training. A questo punto aggiungere di nuovo l'entità predefinito e utterance l'esempio originale. L'entità personalizzata deve continuare a essere contrassegnato invece l'entità predefinito. 

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Rimuovi entità predefiniti per correggere l'assegnazione di tag

La seconda opzione consiste nel rimuovere l'entità predefinita dall'app, contrassegnare l'entità personalizzata in utterance l'esempio, quindi aggiungere l'entità predefinito all'app. Questa correzione si presuppone che l'entità predefinito non fa parte di un'entità composta. 

## <a name="regular-expression-entity"></a>Entità di espressione regolare 

Un'espressione regolare è ideale per il testo di un'espressione non elaborata. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico a livello di carattere e non a livello di token. Se l'espressione regolare è troppo complessa, ad esempio con molte parentesi, non è possibile aggiungere l'espressione al modello. Usa parte ma non tutte le [Regex .NET](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) libreria. 

Questa entità è idonea quando:

* I dati sono formattati in modo coerente con qualsiasi variazione altrettanto coerente.
* L'espressione regolare non richiede più di due livelli di annidamento. 

![Entità di espressione regolare](./media/luis-concept-entities/regex-entity.png)

[Esercitazione](luis-quickstart-intents-regex-entity.md)<br>
[Risposta JSON di esempio per l'entità](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

## <a name="simple-entity"></a>Entità semplice 

Un'entità semplice è un'entità generica che descrive un singolo concetto e che viene appresa dal contesto basato su Machine Learning. Poiché le entità semplici sono in genere nomi, come nomi di società, nomi di prodotto o altre categorie di nomi, è consigliabile aggiungere un [elenco di frasi](luis-concept-feature.md) quando si usa un'entità semplice per migliorare il segnale dei nomi usati. 

Questa entità è idonea quando:

* I dati non sono formattati in modo coerente ma indicano la stessa cosa. 

![entità semplice](./media/luis-concept-entities/simple-entity.png)

[Esercitazione](luis-quickstart-primary-and-secondary-data.md)<br/>
[Risposta di esempio per l'entità](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>Limiti delle entità

Esaminare i [limiti](luis-boundaries.md#model-boundaries) per comprendere il numero di entità di ciascun tipo che è possibile aggiungere a un modello.

## <a name="composite-vs-hierarchical-entities"></a>Entità composite e gerarchiche

Sia le entità composite che le entità gerarchiche hanno relazioni padre-figlio e sono basate su Machine Learning. L'apprendimento basato su Machine Learning consente a LUIS di comprendere le entità in base ai diversi contesti (disposizione delle parole). Le entità composite sono più flessibili perché consentono tipi di entità diversi come figli. I figli delle entità gerarchiche sono solo entità semplici. 

|Type|Scopo|Esempio|
|--|--|--|
|Gerarchica|Padre-figlio di entità semplici|Location.Origin=New York<br>Location.Destination=London|
|Composita|Entità padre-figlio: predefinite, elenco, semplici, gerarchiche| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se sono necessarie più entità rispetto al numero massimo 

Potrebbe essere necessario usare entità gerarchiche e composite. Le entità gerarchiche riflettono la relazione tra le entità che condividono caratteristiche o che sono membri di una categoria. Le entità figlio sono tutti i membri della categoria del relativo padre. Ad esempio, un'entità gerarchica denominata PlaneTicketClass potrebbe presentare le entità figlio EconomyClass e FirstClass. La gerarchia si estende a un solo livello di profondità.  

Le entità composite rappresentano parti di un intero. Ad esempio, un'entità composita denominata PlaneTicketOrder potrebbe presentare le entità figlio Airline, Destination, DepartureCity, DepartureDate e PlaneTicketClass. Si crea un'entità composita da entità semplici preesistenti, da figli di entità gerarchiche o da entità predefinite.  

LUIS fornisce anche il tipo di entità elenco non basato su Machine Learning ma che consente all'app LUIS di specificare un elenco fisso di valori. Vedere il riferimento [Limiti di LUIS](luis-boundaries.md) per esaminare i limiti del tipo di entità elenco. 

Se sono state considerate le entità gerarchiche, composite ed elenco e comunque ne occorrono altre in numero superiore al limite, contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide. 

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per altre informazioni sull'aggiunta di entità all'app LUIS.
