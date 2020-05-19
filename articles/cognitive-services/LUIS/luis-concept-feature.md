---
title: Funzionalità-LUIS
description: L'aggiunta di funzionalità a un modello linguistico consente di fornire suggerimenti sul riconoscimento dell'input a cui assegnare un'etichetta o da classificare.
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 906876e39eb7ff31c2e6b954d1514d8afc50bf3a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591897"
---
# <a name="machine-learning-ml-features"></a>Funzionalità di Machine Learning (ML)

In Machine Learning, una **funzionalità**   è un tratto o un attributo distintivo dei dati osservati dal sistema.

Le funzionalità di Machine Learning offrono a LUIS importanti indicazioni per la posizione in cui cercare gli elementi che distinguono un concetto. Sono suggerimenti che LUIS può usare, ma non regole rigide.  Questi hint vengono usati insieme alle etichette per trovare i dati.

 LUIS supporta entrambi gli elenchi di frasi e l'uso di altre entità come funzionalità:
* Funzionalità elenco frasi
* Modello (finalità o entità) come funzionalità

Le funzionalità devono essere considerate una parte necessaria della progettazione dello schema.

## <a name="a-phrase-list-for-a-particular-concept"></a>Elenco di frasi per un particolare concetto

Un elenco di frasi è un elenco di parole o frasi che incapsula un particolare concetto.

Quando si aggiunge un elenco di frasi, è possibile impostare la funzionalità come:
* **[Globale](#global-features)**. Una funzionalità globale si applica all'intera app.

### <a name="when-to-use-a-phrase-list"></a>Quando usare un elenco di frasi

Quando è necessario che l'app LUIS sia in grado di generalizzare e identificare nuovi elementi per il concetto, usare un elenco di frasi. Gli elenchi di frasi sono come un vocabolario specifico per dominio che consente di migliorare la qualità della comprensione di entrambe le finalità e le entità.

### <a name="how-to-use-a-phrase-list"></a>Come usare un elenco di frasi

Con un elenco di frasi, LUIS considera il contesto e generalizza per identificare gli elementi simili a, ma non una corrispondenza esatta del testo.

Passaggi per l'utilizzo di un elenco di frasi:
* Inizia con un'entità appresa dal computer
    * Aggiungere espressioni di esempio
    * Etichetta con un'entità appresa dal computer
* Aggiungere un elenco di frasi
    * Aggiungere parole con un significato simile: **non** aggiungere tutte le parole o frasi possibili. Aggiungere invece alcune parole o frasi alla volta, quindi ripetere il training e pubblicare.
    * Esaminare e aggiungere parole suggerite

### <a name="a-typical-scenario-for-a-phrase-list"></a>Uno scenario tipico per un elenco di frasi

Uno scenario tipico per un elenco di frasi è quello di migliorare le parole correlate a un'idea specifica.

Un esempio di parole che potrebbero richiedere un elenco di frasi per migliorare la loro importanza sono le condizioni mediche. I termini possono avere un significato fisico, chimico, terapeutico o astratto specifico. LUIS non saprà che i termini sono importanti per il dominio dell'oggetto senza un elenco di frasi.

Se si desidera estrarre le condizioni mediche:
* Creare prima di tutto espressioni di esempio ed etichettare i termini medici all'interno di tali enunciazioni.
* Creare quindi un elenco di frasi con esempi dei termini all'interno del dominio dell'oggetto. Questo elenco di frasi deve includere il termine effettivo etichettato e altri termini che descrivono lo stesso concetto.
* Aggiungere l'elenco di frasi all'entità o alla sottoentità che estrae il concetto usato nell'elenco di frasi. Lo scenario più comune è un componente (figlio) di un'entità appresa dal computer. Se l'elenco di frasi deve essere applicato a tutti gli Intent o alle entità, contrassegnare l'elenco di frasi come elenco di frasi globali. Il `enabledForAllModels` flag controlla questo ambito del modello nell'API.

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
    * Country (sottoentità)
    * Cap (sottoentità)

## <a name="required-features"></a>Funzionalità necessarie

È necessario trovare una funzionalità obbligatoria affinché il modello venga restituito dall'endpoint di stima. Usare una funzionalità obbligatoria quando si è certi che i dati in ingresso devono corrispondere alla funzionalità.

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
    * Country (sottoentità)
    * Cap (sottoentità)

### <a name="required-feature-using-prebuilt-entities"></a>Funzionalità obbligatoria con le entità predefinite

La città, lo stato e il paese sono in genere un set di elenchi chiuso, vale a dire che non cambiano molto nel tempo. Queste entità potrebbero avere le funzionalità consigliate pertinenti e tali funzionalità potrebbero essere contrassegnate come obbligatorie. Questo significa che non viene restituito l'intero indirizzo di spedizione perché non sono state trovate le entità con funzionalità obbligatorie.

Cosa accade se la città, lo stato o il paese si trovano nell'espressione, ma in una posizione o in uno slang non previsto da LUIS? Se si vuole fornire una fase di post-elaborazione per facilitare la risoluzione dell'entità, a causa di un punteggio di confidenza basso di LUIS, non contrassegnare la funzionalità come richiesto.

Un altro esempio di una funzionalità obbligatoria per l'indirizzo di spedizione è quello di fare in modo che il numero di strada sia un numero [precompilato](luis-reference-prebuilt-entities.md) obbligatorio. Questo consente a un utente di immettere "1 Microsoft Way" o "One Microsoft Way". Entrambe le soluzioni vengono risolte in un numero di "1" per la sottoentità numero civico.

### <a name="required-feature-using-list-entities"></a>Funzionalità obbligatoria con le entità elenco

Un' [entità elenco](reference-entity-list.md) viene utilizzata come elenco di nomi canonici insieme ai relativi sinonimi. Come funzionalità obbligatoria, se l'espressione non include il nome canonico o un sinonimo, l'entità non viene restituita come parte dell'endpoint di stima.

Continuando con l'esempio di indirizzo di spedizione, si supponga che la società venga fornita solo a un set limitato di paesi. È possibile creare un'entità di elenco che includa diversi modi in cui il cliente può fare riferimento al paese. Se LUIS non trova una corrispondenza esatta all'interno del testo dell'espressione, l'entità, che ha la funzionalità richiesta dell'entità list, non viene restituita nella stima.

|Nome canonico|Sinonimi|
|--|--|
|Stati Uniti|U.S.<br>U. S. A<br>US<br>USA<br>0|

L'applicazione client, ad esempio un bot di chat, può porre una domanda di seguito, in modo che il cliente possa capire che la selezione del paese è limitata e _obbligatoria_.

### <a name="required-feature-using-regular-expression-entities"></a>Funzionalità obbligatoria con entità di espressioni regolari

Un' [entità di espressione regolare](reference-entity-regular-expression.md) utilizzata come funzionalità obbligatoria fornisce funzionalità di corrispondenza del testo avanzate.

Continuando con l'indirizzo di spedizione, è possibile creare un'espressione regolare che acquisisca le regole di sintassi dei codici postali dei paesi.

## <a name="global-features"></a>Funzionalità globali

Sebbene l'uso più comune consiste nell'applicare una funzionalità a un modello specifico, è possibile configurare la funzionalità come **funzionalità globale** per applicarla all'intera applicazione.

L'uso più comune di una funzionalità globale è l'aggiunta di un vocabolario aggiuntivo, ad esempio le parole da un'altra lingua, all'app. Se i clienti usano un linguaggio primario, ma si aspettano di poter usare un altro linguaggio all'interno della stessa espressione, è possibile aggiungere una funzionalità che includa parole della lingua secondaria.

Poiché l'utente prevede di usare la seconda lingua per qualsiasi finalità o entità, deve essere aggiunta in un elenco di frasi con l'elenco di frasi configurato come funzionalità globale.

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* [Estendi](schema-change-prediction-runtime.md) i tuoi modelli di app al runtime di stima
* Vedere [Aggiungi funzionalità](luis-how-to-add-features.md) per altre informazioni su come aggiungere funzionalità all'app LUIS.
