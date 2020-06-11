---
title: Funzionalità-LUIS
description: L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 823c51f0b58481e30ff54814dde03285ad094b9e
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677592"
---
# <a name="machine-learning-ml-features"></a>Funzionalità di Machine Learning (ML)

In Machine Learning, una **funzionalità**   è un tratto o un attributo distintivo dei dati osservati e appreso dal sistema.

Le funzionalità di Machine Learning offrono a LUIS importanti indicazioni per la posizione in cui cercare gli elementi che distinguono un concetto. Sono suggerimenti che LUIS può usare, ma non regole rigide.  Questi hint vengono usati insieme alle etichette per trovare i dati.

## <a name="what-is-a-feature"></a>Che cos'è una funzionalità

Una funzionalità è una caratteristica distintiva, che può essere descritta come funzione: f (x) = y. La funzionalità viene usata per individuare il punto in cui osservare, nell'espressione di esempio, il tratto di distinzione. Quando si crea lo schema, cosa si conosce per l'espressione di esempio che indica il tratto? La risposta è la migliore guida alla creazione di funzionalità.

## <a name="types-of-features"></a>Tipi di funzionalità

 LUIS supporta sia gli elenchi di frasi che i modelli come funzionalità:
* Funzionalità elenco frasi
* Modello (finalità o entità) come funzionalità

Le funzionalità devono essere considerate una parte necessaria della progettazione dello schema.

## <a name="how-you-find-features-in-your-example-utterances"></a>Come trovare le funzionalità nelle espressioni di esempio

Poiché LUIS è un'applicazione basata sul linguaggio, le funzionalità saranno basate su testo. Scegliere il testo che indica il tratto che si vuole distinguere. Per LUIS, l'unità più piccola basata su testo è il token. Per la lingua inglese, un token è un intervallo contiguo, senza spazi o punteggiatura, di lettere e numeri. Uno spazio non è un token.

Poiché gli spazi e la punteggiatura non sono token, concentrarsi sugli indizi del testo che è possibile usare come funzionalità. Ricordarsi di includere varianti di Word, ad esempio:
* forme plurali
* verbo teso
* abbreviazione
* ortografia e ortografia

Il testo, come tratto distinto, deve:
* Trovare la corrispondenza con una parola o una frase esatta: è consigliabile aggiungere un'entità di espressione regolare o un'entità di elenco come funzionalità all'entità o alla finalità
* Trovare la corrispondenza con un concetto noto, ad esempio date, ore o nomi di persone, usare un'entità predefinita come funzionalità per l'entità o finalità
* Informazioni sui nuovi esempi nel tempo: usare un elenco di frasi di alcuni esempi del concetto come funzionalità per l'entità o finalità

## <a name="combine-features"></a>Combinare le funzionalità

Poiché sono disponibili diverse opzioni per la descrizione di un tratto, è possibile usare più di una funzionalità che consente di descrivere tale tratto o concetto. Un abbinamento comune prevede l'uso di una funzionalità elenco di frasi e di uno dei tipi di entità usati comunemente come funzionalità: entità precompilata, entità di espressioni regolari o entità elenco.

### <a name="ticket-booking-entity-example"></a>Esempio di entità prenotazione ticket

Come primo esempio, si consideri un'app per la prenotazione di un volo con finalità di prenotazione dei voli e un'entità di prenotazione dei biglietti.

L'entità prenotazione ticket è un'entità appresa dal computer per la destinazione del volo. Per estrarre il percorso, usare due funzionalità:
* Elenco di parole rilevanti, ad esempio `plane` , `flight` , `reservation` ,`ticket`
* Entità predefinita `geographyV2` come funzionalità per l'entità

### <a name="pizza-entity-example"></a>Esempio di entità pizza

Per un altro esempio, si consideri un'app per l'ordine di una pizza con una finalità create Pizza Order e un'entità pizza.

L'entità pizza è un'entità di Machine Learn per i dettagli della pizza. Per estrarre i dettagli, usare due funzionalità:
* Elenco di parole rilevanti, ad esempio `cheese` , `crust` , `pepperoni` ,`pineapple`
* Entità predefinita `number` come funzionalità per l'entità

## <a name="a-phrase-list-for-a-particular-concept"></a>Elenco di frasi per un particolare concetto

Un elenco di frasi è un elenco di parole o frasi che incapsula un particolare concetto e viene applicato come corrispondenza senza distinzione tra maiuscole e minuscole a livello di token.

Quando si aggiunge un elenco di frasi, è possibile impostare la funzionalità come:
* **[Globale](#global-features)**. Una funzionalità globale si applica all'intera app.

### <a name="when-to-use-a-phrase-list"></a>Quando usare un elenco di frasi

Quando è necessario che l'app LUIS sia in grado di generalizzare e identificare nuovi elementi per il concetto, usare un elenco di frasi. Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità.

### <a name="how-to-use-a-phrase-list"></a>Come usare un elenco di frasi

Con un elenco di frasi, LUIS considera il contesto e generalizza per identificare gli elementi simili a, ma non una corrispondenza esatta del testo.

Passaggi per l'utilizzo di un elenco di frasi:
* Inizia con un'entità di Machine Learning
    * Aggiungere espressioni di esempio
    * Etichetta con un'entità Machine Learning
* Aggiungere un elenco di frasi
    * Aggiungere parole con un significato simile: **non** aggiungere tutte le parole o frasi possibili. Aggiungere invece alcune parole o frasi alla volta, quindi ripetere il training e pubblicare.
    * Esaminare e aggiungere parole suggerite

### <a name="a-typical-scenario-for-a-phrase-list"></a>Uno scenario tipico per un elenco di frasi

Uno scenario tipico per un elenco di frasi è quello di migliorare le parole correlate a un'idea specifica.

Un esempio di parole che potrebbero richiedere un elenco di frasi per migliorare la loro importanza sono le condizioni mediche. I termini possono avere un significato fisico, chimico, terapeutico o astratto specifico. LUIS non saprà che i termini sono importanti per il dominio dell'oggetto senza un elenco di frasi.

Se si desidera estrarre le condizioni mediche:
* Creare prima di tutto espressioni di esempio ed etichettare i termini medici all'interno di tali enunciazioni.
* Creare quindi un elenco di frasi con esempi dei termini all'interno del dominio dell'oggetto. Questo elenco di frasi deve includere il termine effettivo etichettato e altri termini che descrivono lo stesso concetto.
* Aggiungere l'elenco di frasi all'entità o alla sottoentità che estrae il concetto usato nell'elenco di frasi. Lo scenario più comune è un componente (figlio) di un'entità di machine learning. Se l'elenco di frasi deve essere applicato a tutti gli Intent o alle entità, contrassegnare l'elenco di frasi come elenco di frasi globali. Il `enabledForAllModels` flag controlla questo ambito del modello nell'API.

### <a name="token-matches-for-a-phrase-list"></a>Corrispondenze del token per un elenco di frasi

Un elenco di frasi si applica a livello di token, indipendentemente dalla distinzione tra maiuscole e minuscole. Il grafico seguente mostra in che modo un elenco di frasi contenente la parola `Ann` viene applicato alle varianti degli stessi caratteri nell'ordine specificato.


| Variazione del token`Ann` | Corrispondenza elenco frasi quando viene trovato il token |
|--------------------------|---------------------------------------|
| ANN<br>aNN<br>           | Sì: il token è`Ann`                  |
| Ann                    | Sì: il token è`Ann`                  |
| Anne                     | Nessun token è`Anne`                  |


<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Un modello come funzionalità consente a un altro modello

È possibile aggiungere un modello (finalità o entità) come funzione a un altro modello (finalità o entità). Aggiungendo un Intent o un'entità esistente come funzionalità, l'aggiunta di un concetto ben definito con esempi con etichetta.

Quando si aggiunge un modello come funzionalità, è possibile impostare la funzionalità come:
* **[Obbligatorio](#required-features)**. È necessario trovare una funzionalità obbligatoria affinché il modello venga restituito dall'endpoint di stima.
* **[Globale](#global-features)**. Una funzionalità globale si applica all'intera app.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Quando usare un'entità come funzionalità per finalità

Aggiungere un'entità come funzionalità a uno scopo quando il rilevamento di tale entità è significativo per lo scopo.

Ad esempio, se lo scopo è quello di prenotare un volo, `BookFlight` e l'entità è costituita da informazioni sui ticket, ad esempio il numero di postazioni, di origine e di destinazione, la ricerca dell'entità Information ticket dovrebbe aggiungere un peso significativo alla stima dello `BookFlight` scopo.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Quando usare un'entità come funzionalità per un'altra entità

Un'entità (A) deve essere aggiunta come caratteristica a un'altra entità (B) quando il rilevamento di tale entità (A) è significativo per la stima dell'entità (B).

Se, ad esempio, l'entità Address di spedizione contiene una sottoentità Address via, la ricerca della sottoentità Address via aggiunge un peso significativo alla stima per l'entità Address shipping.

* Indirizzo di spedizione (entità Machine Learn)
    * Numero civico (sottoentità)
    * Via (sottoentità)
    * Città (sottoentità)
    * Stato o provincia (sottoentità)
    * Paese/area geografica (sottoentità)
    * Cap (sottoentità)

## <a name="nested-subentities-with-features"></a>Sottoentità nidificate con funzionalità

Una sottoentità appresa dal computer indica che è presente un concetto per l'entità padre, se tale elemento padre è un'altra sottoentità o l'entità superiore. Il valore della sottoentità funge da funzionalità per il relativo elemento padre.

Una sottoentità può avere sia un elenco di frasi come funzionalità che un modello (un'altra entità) come una funzionalità.

Quando la sottoentità ha un elenco di frasi, il vocabolario del concetto verrà incrementato, ma non verranno aggiunte informazioni alla risposta JSON della stima.

Quando la sottoentità ha una funzionalità di un'altra entità, la risposta JSON include i dati estratti di quell'altra entità.

## <a name="required-features"></a>Funzionalità necessarie

È necessario trovare una funzionalità obbligatoria affinché il modello venga restituito dall'endpoint di stima. Usare una funzionalità obbligatoria quando si è certi che i dati in ingresso devono corrispondere alla funzionalità.

Se il testo dell'espressione non corrisponde alla funzionalità richiesta, non verrà estratto.

**Una funzionalità obbligatoria usa un'entità non acquisita nel computer**:
* Entità di espressione regolare
* Entità elenco
* Entità predefinita

Quali sono le funzionalità valide da contrassegnare come richiesto? Se si è certi che il modello verrà trovato nei dati, impostare la funzionalità come richiesto. Una funzionalità obbligatoria non restituisce nulla, se non è stata trovata.

Continuando con l'esempio di indirizzo di spedizione:
* Indirizzo di spedizione (entità Machine Learn)
    * Numero civico (sottoentità)
    * Via (sottoentità)
    * Nome via (sottoentità)
    * Città (sottoentità)
    * Stato o provincia (sottoentità)
    * Paese/area geografica (sottoentità)
    * Cap (sottoentità)

### <a name="required-feature-using-prebuilt-entities"></a>Funzionalità obbligatoria con le entità predefinite

Città, stato e paese/area geografica sono in genere un set di elenchi chiuso, ovvero non cambiano molto nel tempo. Queste entità potrebbero avere le funzionalità consigliate pertinenti e tali funzionalità potrebbero essere contrassegnate come obbligatorie. Questo significa che non viene restituito l'intero indirizzo di spedizione perché non sono state trovate le entità con funzionalità obbligatorie.

Cosa accade se la città, lo stato o il paese/area geografica si trovano nell'espressione, ma in una posizione o in uno slang non previsto da LUIS? Se si vuole fornire una fase di post-elaborazione per facilitare la risoluzione dell'entità, a causa di un punteggio di confidenza basso di LUIS, non contrassegnare la funzionalità come richiesto.

Un altro esempio di una funzionalità obbligatoria per l'indirizzo di spedizione è quello di fare in modo che il numero di strada sia un numero [precompilato](luis-reference-prebuilt-entities.md) obbligatorio. Questo consente a un utente di immettere "1 Microsoft Way" o "One Microsoft Way". Entrambe le soluzioni vengono risolte in un numero di "1" per la sottoentità numero civico.

### <a name="required-feature-using-list-entities"></a>Funzionalità obbligatoria con le entità elenco

Un' [entità elenco](reference-entity-list.md) viene utilizzata come elenco di nomi canonici insieme ai relativi sinonimi. Come funzionalità obbligatoria, se l'espressione non include il nome canonico o un sinonimo, l'entità non viene restituita come parte dell'endpoint di stima.

Continuando con l'esempio di indirizzo di spedizione, si supponga che la società venga fornita solo a un set limitato di paesi/aree geografiche. È possibile creare un'entità di elenco che includa diversi modi in cui il cliente può fare riferimento al paese. Se LUIS non trova una corrispondenza esatta all'interno del testo dell'espressione, l'entità, che ha la funzionalità richiesta dell'entità list, non viene restituita nella stima.

|Nome canonico|Sinonimi|
|--|--|
|Stati Uniti|U.S.<br>U. S. A<br>Stati Uniti<br>USA<br>0|

L'applicazione client, ad esempio un bot di chat, può porre una domanda di seguito, in modo che il cliente possa capire che la selezione del paese/area geografica è limitata e _obbligatoria_.

### <a name="required-feature-using-regular-expression-entities"></a>Funzionalità obbligatoria con entità di espressioni regolari

Un' [entità di espressione regolare](reference-entity-regular-expression.md) utilizzata come funzionalità obbligatoria fornisce funzionalità di corrispondenza del testo avanzate.

Continuando con l'indirizzo di spedizione, è possibile creare un'espressione regolare che acquisisca le regole di sintassi dei codici postali del paese/area geografica.

## <a name="global-features"></a>Funzionalità globali

Sebbene l'uso più comune consiste nell'applicare una funzionalità a un modello specifico, è possibile configurare la funzionalità come **funzionalità globale** per applicarla all'intera applicazione.

L'uso più comune di una funzionalità globale è l'aggiunta di un vocabolario aggiuntivo, ad esempio le parole da un'altra lingua, all'app. Se i clienti usano un linguaggio primario, ma si aspettano di poter usare un altro linguaggio all'interno della stessa espressione, è possibile aggiungere una funzionalità che includa parole della lingua secondaria.

Poiché l'utente prevede di usare la seconda lingua per qualsiasi finalità o entità, deve essere aggiunta in un elenco di frasi con l'elenco di frasi configurato come funzionalità globale.

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* [Estendi](schema-change-prediction-runtime.md) i tuoi modelli di app al runtime di stima
* Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.
