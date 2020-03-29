---
title: Procedure consigliate per la creazione dell'app LUIS
titleSuffix: Azure Cognitive Services
description: Scopri le best practice per ottenere i migliori risultati dal modello dell'app LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b4be79338db71ad83204fae971da0b77885a8070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280915"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Procedure consigliate per la creazione di un'app per la comprensione della lingua (LUIS)Best practices for building a language understanding (LUIS) app
Usare il processo di creazione dell'app per compilare l'app LUIS:Use the app authoring process to build your LUIS app: 

* Creare modelli di linguaggio (finalità ed entità)
* Aggiungere alcune espressioni di esempio di training (15-30 per finalità)Add a few training example utterances (15-30 per intent)
* Pubblica nell'endpoint
* Test dall'endpoint 

Dopo aver [pubblicato](luis-how-to-publish-app.md)l'app, usa il ciclo di vita dello sviluppo per aggiungere funzionalità, pubblicare e testare dall'endpoint. Non iniziare il ciclo di creazione successivo aggiungendo altre espressioni di esempio perché ciò non consente a LUIS di apprendere il modello con espressioni utente reali. 

Non espandere le espressioni fino a quando il set corrente di espressioni di esempio ed endpoint restituisce punteggi di stima elevati e sicuri. Migliorare i punteggi utilizzando [l'apprendimento attivo](luis-concept-review-endpoint-utterances.md). 




## <a name="do-and-dont"></a>Procedure consigliate e procedure rischiose
L'elenco seguente include le procedure consigliate per le app LUIS:

|Cosa fare|Cosa non fare|
|--|--|
|[Definire le finalità distinte](#do-define-distinct-intents)<br>[Aggiungere descrittori alle finalità](#do-add-descriptors-to-intents) |[Aggiungere molte espressioni di esempio alle finalità](#dont-add-many-example-utterances-to-intents)<br>[Usare poche o semplici entità](#dont-use-few-or-simple-entities) |
|[Trovare un compromesso tra troppo generico e troppo specifico per ogni finalità](#do-find-sweet-spot-for-intents)|[Usare LUIS come piattaforma di training](#dont-use-luis-as-a-training-platform)|
|[Crea la tua app in modo iterativo con le versioni](#do-build-your-app-iteratively-with-versions)<br>[Creare entità per la scomposizione del modelloBuild entities for model decomposition](#do-build-for-model-decomposition)|[Aggiungere molte espressioni di esempio dello stesso formato, ignorando gli altri](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Aggiungere modelli nelle iterazioni successiveAdd patterns in later iterations](#do-add-patterns-in-later-iterations)|[Combinare la definizione di finalità ed entità](#dont-mix-the-definition-of-intents-and-entities)|
|[Bilanciare le espressioni tra tutte le finalità](#balance-your-utterances-across-all-intents) ad eccezione della finalità None (Nessuna).<br>[Aggiungere espressioni di esempio alla finalità None (Nessuna)](#do-add-example-utterances-to-none-intent)|[Creare descrittori con tutti i valori possibili](#dont-create-descriptors-with-all-the-possible-values)|
|[Usare la funzionalità suggerimento per l'apprendimento attivo](#do-leverage-the-suggest-feature-for-active-learning)|[Aggiungere troppi criteri](#dont-add-many-patterns)|
|[Monitora le prestazioni della tua app con il test in batch](#do-monitor-the-performance-of-your-app)|[Eseguire il training e pubblicare con ogni singola espressione di esempio aggiunta](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Definire finalità distinte
Assicurarsi che il vocabolario per ogni finalità sia specifico di quella finalità e che non si sovrapponga a un'altra. Ad esempio, se si vuole realizzare un'app per gestire i viaggi, ad esempio i voli aerei e gli hotel, è possibile scegliere di definire queste aree di interesse come finalità separate o una sola finalità con entità per dati specifici all'interno dell'espressione.

Se il vocabolario tra due finalità è lo stesso, combinare la finalità e usare le entità. 

Considerare le espressioni di esempio seguenti:

|Espressioni di esempio|
|--|
|Prenota un volo|
|Prenota un hotel|

`Book a flight`e `Book a hotel` utilizzare lo stesso `book a `vocabolario di . Questo formato è lo stesso, quindi dovrebbe essere `flight` lo `hotel` stesso intento con le diverse parole di e come entità estratte. 

## <a name="do-add-descriptors-to-intents"></a>Aggiungere descrittori alle finalità

I descrittori consentono di descrivere le funzionalità per una finalità. Un descrittore può essere un elenco di frasi di parole significative per tale finalità o un'entità significativa per tale finalità. 

## <a name="do-find-sweet-spot-for-intents"></a>Trovare un compromesso per le finalità
Usare i dati di stima LUIS per determinare se le finalità si sovrappongono. Le finalità sovrapposte confondono LUIS. Il risultato è che la finalità con il punteggio più alto è troppo vicina a un'altra finalità. Poiché LUIS non usa ogni volta lo stesso percorso esatto attraverso i dati per il training, una finalità sovrapposta ha la possibilità di essere la prima o la seconda nel training. Si vuole che il punteggio dell'espressione per ogni finalità sia più distante affinché questo non si verifichi. La distinzione ottimale tra le finalità dovrebbe produrre ogni volta la finalità principale prevista. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Crea la tua app in modo iterativo con le versioni

Ogni ciclo di creazione deve essere all'interno di una nuova [versione](luis-concept-version.md), clonato da una versione esistente. 

## <a name="do-build-for-model-decomposition"></a>Eseguire la compilazione per la decomposizione del modelloDo build for model decomposition

La decomposizione del modello ha un tipico processo di:

* creare **un intento** basato sulle intenzioni dell'utente dell'app client
* aggiungere 15-30 espressioni di esempio basate sull'input dell'utente realeAdd 15-30 example utterances based on real-world user input
* etichetta di primo livello dei dati nell'espressione di esempioLabel top-level data concept in example utterance
* rompere il concetto di dati in sottocomponenti
* aggiungere descrittori (funzioni) ai sottocomponenti
* aggiungere descrittori (caratteristiche) alle finalità 

Dopo aver creato la finalità e aggiunto le espressioni di esempio, nell'esempio seguente viene descritta la scomposizione dell'entità. 

Iniziare identificando i concetti di dati completi che si desidera estrarre in un'espressione. Questa è la tua entità appresa dalla macchina. Poi scomporre la frase nelle sue parti. Ciò include l'identificazione dei sottocomponenti (come entità), insieme a descrittori e vincoli. 

Ad esempio, se si desidera estrarre un indirizzo, l'entità appresa dal computer superiore potrebbe essere chiamata `Address`. Durante la creazione dell'indirizzo, identificare alcuni dei suoi sottocomponenti, ad esempio l'indirizzo, la città, lo stato e il codice postale. 

Continuare a scomporre tali elementi **vincolando** il codice postale a un'espressione regolare. Scomporre l'indirizzo in parti di un numero civico (utilizzando un numero predefinito), un nome di via e un tipo di via. Il tipo di strada può essere descritto con un elenco **di descrittori** come viale, cerchio, strada e corsia.

L'API di creazione V3 consente la scomposizione del modello. 

## <a name="do-add-patterns-in-later-iterations"></a>Aggiungere modelli nelle iterazioni successiveDo add patterns in later iterations

È necessario comprendere il funzionamento dell'app prima di aggiungere [modelli](luis-concept-patterns.md) perché i modelli hanno un peso maggiore rispetto alle espressioni di esempio e l'asimmetria di sicurezza. 

Dopo aver compreso il funzionamento dell'app, aggiungi i modelli che si applicano alla tua app. Non è necessario aggiungerli a ogni [iterazione](luis-concept-app-iteration.md). 

Non c'è alcun danno ad aggiungerli all'inizio della progettazione del modello, ma è più facile vedere come ogni modello cambia il modello dopo che il modello viene testato con espressioni. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Bilanciare le espressioni in tutte le finalità

Affinché LUIS possa produrre stime precise, la quantità di espressioni di esempio in ogni finalità (ad eccezione della finalità None), deve essere relativamente equivalente. 

In presenza di una finalità con 100 espressioni di esempio e una finalità con 20 espressioni di esempio, la finalità con 100 espressioni produrrà più stime.  

## <a name="do-add-example-utterances-to-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

Questa finalità è la finalità di fallback, che indica tutti gli elementi esterni all'applicazione. Aggiungere un'espressione di esempio alla finalità None (Nessuna) ogni 10 espressioni di esempio nel resto dell'app LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Usare la funzionalità suggerimento per l'apprendimento attivo

Usare regolarmente [Review endpoint utterances](luis-how-to-review-endpoint-utterances.md) (Rivedi espressioni endpoint) dell'**apprendimento attivo** anziché aggiungere più espressioni di esempio alle finalità. Poiché l'app riceve costantemente espressioni endpoint, questo elenco è destinato a crescere e a cambiare.

## <a name="do-monitor-the-performance-of-your-app"></a>Non monitorare le prestazioni dell'app

Monitorare la precisione della stima usando un set di [test batch](luis-concept-batch-test.md). 

Mantenere un set separato di espressioni che non vengono usate come espressioni di [esempio](luis-concept-utterance.md) o espressioni endpoint. Continuare a migliorare l'app per il set di test. Adattare il set di test per riflettere espressioni utente reali. Usare questo set di test per valutare ogni iterazione o versione dell'app. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Non aggiungere molte espressioni di esempio alle finalità

Dopo la pubblicazione dell'app, aggiungere solo le espressioni dell'apprendimento attivo nel processo del ciclo di vita dello sviluppo. Se le espressioni sono troppo simili, aggiungere un criterio. 

## <a name="dont-use-few-or-simple-entities"></a>Non usare poche o semplici entità

Le entità vengono create per l'estrazione e la stima dei dati. È importante che ogni finalità disponga di entità apprese dal computer che descrivono i dati nella finalità. Ciò consente a LUIS di prevedere l'intento, anche se l'applicazione client non deve usare l'entità estratta. 

## <a name="dont-use-luis-as-a-training-platform"></a>Non usare LUIS come piattaforma di training

LUIS è specifico di un dominio di modello di linguaggio. Non è progettato per l'uso come piattaforma di training in linguaggio naturale generale. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Non aggiungere molte espressioni di esempio dello stesso formato, ignorando gli altri

LUIS prevede variazioni nelle espressioni di una finalità. Le espressioni possono variare mantenendo lo stesso significato generale. Le variazioni possono includere lunghezza dell'espressione, scelta e posizionamento delle parole. 

|Non usare lo stesso formato|Usare formati diversi|
|--|--|
|Acquista un biglietto per Seattle<br>Acquista un biglietto per Parigi<br>Acquista un biglietto per Orlando|Acquista 1 biglietto per Seattle<br>Prenota due posti sul red eye per Parigi lunedì prossimo<br>Desidero prenotare 3 biglietti per Orlando per le vacanze di primavera|

La seconda colonna usa verbi diversi (acquista, prenota, riserva), quantità diverse (1, due, 3) e disposizioni diverse delle parole, ma tutti hanno la stessa intenzione di acquistare biglietti aerei. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Non combinare la definizione di finalità ed entità

Creare una finalità per qualsiasi azione che eseguirà il bot. Usare le entità come parametri che rendono possibile tale azione. 

Per un bot che prenoterà i voli aerei, crea un intento **BookFlight.** Non creare una finalità per ogni compagnia aerea o per ogni destinazione. Usare questi dati come [entità](luis-concept-entity-types.md) e contrassegnarle nelle espressioni di esempio. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Non creare descrittori con tutti i valori possibili

Fornire alcuni esempi negli elenchi di [frasi](luis-concept-feature.md) descrittori, ma non in tutte le parole. LUIS generalizza e tiene conto del contesto. 

## <a name="dont-add-many-patterns"></a>Non aggiungere molti criteri

Non aggiungere troppi [criteri](luis-concept-patterns.md). LUIS apprenderà rapidamente con un minor numero di esempi. Non sovraccaricare il sistema inutilmente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Non eseguire il training né pubblicare con ogni singola espressione di esempio

Aggiungere 10 o 15 espressioni prima di procedere con il training e la pubblicazione. In questo modo, si vedrà l'impatto sulla precisione della stima. L'aggiunta di una singola espressione potrebbe non avere un impatto visibile sul punteggio. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [pianificazione dell'app](luis-how-plan-your-app.md) nell'app LUIS.
