---
title: Procedure consigliate per la creazione dell'app LUIS
description: Informazioni sulle procedure consigliate per ottenere risultati ottimali dal modello dell'app LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: a16267b0bc5d9b550c73dc4e1dafee8304ec0237
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541822"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Procedure consigliate per la creazione di un'app LUIS (Language Understanding)
Usare il processo di creazione di app per compilare l'app LUIS:

* Modelli di linguaggio di compilazione (Intent ed entità)
* Aggiungere alcune espressioni di esempio di training (15-30 per finalità)
* Pubblica in endpoint
* Test dall'endpoint

Dopo la [pubblicazione](luis-how-to-publish-app.md)dell'app, usa il ciclo di vita di sviluppo per aggiungere funzionalità, pubblicare e testare dall'endpoint. Non iniziare il ciclo di creazione successivo aggiungendo più espressioni di esempio perché questo non consente a LUIS di apprendere il modello con espressioni utente reali.

Non espandere gli enunciati fino a quando il set corrente di espressioni di esempio e di endpoint non restituiscono punteggi di stima elevati e sicuri. Migliorare i punteggi usando l' [apprendimento attivo](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Procedure consigliate e procedure rischiose
L'elenco seguente include le procedure consigliate per le app LUIS:

|Cosa fare|Cosa non fare|
|--|--|
|[Pianificare lo schema](#do-plan-your-schema)|[Compilazione e pubblicazione senza piano](#dont-publish-too-quickly)|
|[Definire le finalità distinte](#do-define-distinct-intents)<br>[Aggiungere funzionalità agli Intent](#do-add-features-to-intents)<br>
[Usare le entità apprese dal computer](#do-use-machine-learned-entities) |[Aggiungere molte espressioni di esempio alle finalità](#dont-add-many-example-utterances-to-intents)<br>[Usare poche o entità semplici](#dont-use-few-or-simple-entities) |
|[Trovare un compromesso tra troppo generico e troppo specifico per ogni finalità](#do-find-sweet-spot-for-intents)|[Usare LUIS come piattaforma di training](#dont-use-luis-as-a-training-platform)|
|[Compilare l'app in modo iterativo con le versioni](#do-build-your-app-iteratively-with-versions)<br>[Entità di compilazione per la scomposizione dei modelli](#do-build-for-model-decomposition)|[Aggiungere molte espressioni di esempio dello stesso formato, ignorando gli altri](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Aggiungere modelli nelle iterazioni successive](#do-add-patterns-in-later-iterations)|[Combinare la definizione di finalità ed entità](#dont-mix-the-definition-of-intents-and-entities)|
|[Bilanciare le espressioni tra tutte le finalità](#balance-your-utterances-across-all-intents) ad eccezione della finalità None (Nessuna).<br>[Aggiungere espressioni di esempio alla finalità None (Nessuna)](#do-add-example-utterances-to-none-intent)|[Creare elenchi di frasi con tutti i valori possibili](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Usare la funzionalità suggerimento per l'apprendimento attivo](#do-leverage-the-suggest-feature-for-active-learning)|[Aggiungere troppi criteri](#dont-add-many-patterns)|
|[Monitorare le prestazioni dell'app con test batch](#do-monitor-the-performance-of-your-app)|[Eseguire il training e pubblicare con ogni singola espressione di esempio aggiunta](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Pianificare lo schema

Prima di iniziare a compilare lo schema dell'app, è necessario identificare l'elemento e il percorso in cui si intende usare questa app. La pianificazione più approfondita e specifica, migliore sarà l'app.

* Ricerca di utenti mirati
* Definizione di persone di tipo end-to-end per rappresentare l'App-Voice, l'avatar, la gestione dei problemi (proattivo, reattivo)
* Identificare le interazioni utente (testo, sintesi vocale) attraverso i canali, passando a soluzioni esistenti o creando una nuova soluzione per questa app
* Percorso utente end-to-end
    * Cosa è necessario aspettarsi da questa app? * Quali sono le priorità di quello che dovrebbe fare?
    * Quali sono i casi d'uso principali?
* Raccolta di dati: [informazioni](data-collection.md) sulla raccolta e la preparazione dei dati

## <a name="do-define-distinct-intents"></a>Definire finalità distinte
Assicurarsi che il vocabolario per ogni finalità sia specifico di quella finalità e che non si sovrapponga a un'altra. Ad esempio, se si vuole realizzare un'app per gestire i viaggi, ad esempio i voli aerei e gli hotel, è possibile scegliere di definire queste aree di interesse come finalità separate o una sola finalità con entità per dati specifici all'interno dell'espressione.

Se il vocabolario tra due finalità è lo stesso, combinare la finalità e usare le entità.

Considerare le espressioni di esempio seguenti:

|Espressioni di esempio|
|--|
|Prenota un volo|
|Prenota un hotel|

`Book a flight` e `Book a hotel` usano lo stesso vocabolario di `book a ` . Questo formato è lo stesso, quindi deve essere lo stesso scopo con le diverse parole di `flight` e `hotel` come entità estratte.

## <a name="do-add-features-to-intents"></a>Aggiungere funzionalità agli Intent

Le funzionalità descrivono i concetti per uno scopo. Una funzionalità può essere un elenco di parole che sono significative per tale finalità o un'entità che è significativa per tale finalità.

## <a name="do-find-sweet-spot-for-intents"></a>Trovare un compromesso per le finalità
Usare i dati di stima LUIS per determinare se le finalità si sovrappongono. Le finalità sovrapposte confondono LUIS. Il risultato è che la finalità con il punteggio più alto è troppo vicina a un'altra finalità. Poiché LUIS non usa ogni volta lo stesso percorso esatto attraverso i dati per il training, una finalità sovrapposta ha la possibilità di essere la prima o la seconda nel training. Si vuole che il punteggio dell'espressione per ogni finalità sia più distante affinché questo non si verifichi. La distinzione ottimale tra le finalità dovrebbe produrre ogni volta la finalità principale prevista.

## <a name="do-use-machine-learned-entities"></a>Usare le entità apprese dal computer

Le entità apprese dal computer sono personalizzate per l'app e richiedono l'assegnazione di etichette per avere esito positivo. Se non si utilizzano le entità apprese dal computer, è possibile utilizzare lo strumento errato.

Le entità apprese dal computer possono usare altre entità come funzionalità. Queste altre entità possono essere entità personalizzate, ad esempio entità di espressioni regolari o entità dell'elenco, oppure è possibile utilizzare le entità predefinite come funzionalità.

Informazioni sulle [entità efficaci apprese dal computer](luis-concept-entity-types.md#effective-machine-learned-entities).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Compilare l'app in modo iterativo con le versioni

Ogni ciclo di creazione deve essere all'interno di una nuova [versione](luis-concept-version.md), clonato da una versione esistente.

## <a name="do-build-for-model-decomposition"></a>Eseguire la compilazione per la scomposizione dei modelli

La scomposizione dei modelli ha un processo tipico:

* Crea **finalità** basate sulle intenzioni dell'utente dell'app client
* aggiungere espressioni di esempio 15-30 in base all'input utente reale
* etichettare il concetto di dati di primo livello nell'espressione di esempio
* suddividere il concetto di dati in sottoentità
* aggiungere funzionalità alle sottoentità
* aggiungere funzionalità agli Intent

Dopo aver creato la finalità e aggiunto le espressioni di esempio, nell'esempio seguente viene descritta la scomposizione delle entità.

Per iniziare, è necessario identificare i concetti di dati completi da estrarre in un enunciato. Si tratta dell'entità di machine learning. Quindi scomporre la frase nelle relative parti. Ciò include l'identificazione di sottoentità e funzionalità.

Se ad esempio si vuole estrarre un indirizzo, è possibile chiamare l'entità di Machine Learning di livello superiore `Address` . Durante la creazione dell'indirizzo, identificare alcune delle relative sottoentità, ad esempio via indirizzo, città, stato e codice postale.

Continuare a scomporre gli elementi per:
* Aggiunta di una funzionalità obbligatoria del Cap come entità di espressione regolare.
* Scomposizione del numero civico in parti:
    * **Numero civico** con una funzionalità obbligatoria di un'entità di numero predefinita.
    * **Nome della strada**.
    * **Tipo di strada** con una funzionalità obbligatoria di un'entità di elenco che include parole come Avenue, Circle, Road e Lane.

L'API di creazione di V3 consente la scomposizione dei modelli.

## <a name="do-add-patterns-in-later-iterations"></a>Aggiungere modelli nelle iterazioni successive

È necessario comprendere il comportamento dell'app prima di aggiungere i [modelli](luis-concept-patterns.md) , perché i modelli sono ponderati in modo più significativo rispetto alle espressioni di esempio e presentano un'asimmetria di confidenza.

Quando si comprende il comportamento dell'app, aggiungere i modelli che si applicano all'app. Non è necessario aggiungerli a ogni [iterazione](luis-concept-app-iteration.md).

Non vi sono problemi ad aggiungerli all'inizio della progettazione del modello, ma è più semplice vedere in che modo ogni modello cambia il modello dopo che il modello è stato testato con espressioni.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Bilanciare le espressioni in tutti gli Intent

Affinché LUIS possa produrre stime precise, la quantità di espressioni di esempio in ogni finalità (ad eccezione della finalità None), deve essere relativamente equivalente.

In presenza di una finalità con 100 espressioni di esempio e una finalità con 20 espressioni di esempio, la finalità con 100 espressioni produrrà più stime.

## <a name="do-add-example-utterances-to-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna)

Questa finalità è la finalità del fallback, che indica tutti gli elementi all'esterno dell'applicazione. Aggiungere un'espressione di esempio alla finalità None (Nessuna) ogni 10 espressioni di esempio nel resto dell'app LUIS.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Usare la funzionalità suggerimento per l'apprendimento attivo

Usare regolarmente [Review endpoint utterances](luis-how-to-review-endpoint-utterances.md) (Rivedi espressioni endpoint) dell'**apprendimento attivo** anziché aggiungere più espressioni di esempio alle finalità. Poiché l'app riceve costantemente espressioni endpoint, questo elenco è destinato a crescere e a cambiare.

## <a name="do-monitor-the-performance-of-your-app"></a>Non monitorare le prestazioni dell'app

Monitorare la precisione della stima usando un set di [test batch](luis-concept-batch-test.md).

Mantieni un set separato di espressioni che non vengono usate come [espressioni di esempio](luis-concept-utterance.md) o espressioni di endpoint. Continuare a migliorare l'app per il set di test. Adattare il set di test per riflettere espressioni utente reali. Usare questo set di test per valutare ogni iterazione o versione dell'app.

## <a name="dont-publish-too-quickly"></a>Non pubblicare troppo rapidamente

La pubblicazione dell'app troppo rapidamente, senza una [pianificazione corretta](#do-plan-your-schema), può causare diversi problemi, ad esempio:

* L'app non funzionerà nello scenario effettivo a un livello di prestazioni accettabile.
* Lo schema (Intent ed entità) non è appropriato e, se è stata sviluppata la logica dell'app client dopo lo schema, potrebbe essere necessario riscriverla da zero. Ciò provocherebbe ritardi imprevisti e un costo aggiuntivo per il progetto su cui si sta lavorando.
* Le espressioni che si aggiungono al modello possono causare la distorsione verso il set di espressioni di esempio difficili da eseguire il debug e l'identificazione. La rimozione dell'ambiguità sarà anche difficile dopo aver eseguito il commit in un determinato schema.

## <a name="dont-add-many-example-utterances-to-intents"></a>Non aggiungere molte espressioni di esempio alle finalità

Dopo la pubblicazione dell'app, aggiungere solo espressioni di apprendimento attivo nel processo del ciclo di vita di sviluppo. Se le espressioni sono troppo simili, aggiungere un criterio.

## <a name="dont-use-few-or-simple-entities"></a>Non usare poche o entità semplici

Le entità sono compilate per l'estrazione e la stima dei dati. È importante che ogni finalità includa entità di Machine Learning che descrivano i dati nella finalità. Questo consente a LUIS di prevedere lo scopo, anche se l'applicazione client non deve usare l'entità estratta.

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

Per un bot che scriverà i voli Airline, creare una finalità **BookFlight** . Non creare una finalità per ogni compagnia aerea o per ogni destinazione. Usare questi dati come [entità](luis-concept-entity-types.md) e contrassegnarle nelle espressioni di esempio.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Non creare elenchi di frasi con tutti i valori possibili

Fornire alcuni esempi negli elenchi di [frasi](luis-concept-feature.md) ma non in tutte le parole o frasi. LUIS generalizza e tiene conto del contesto.

## <a name="dont-add-many-patterns"></a>Non aggiungere molti criteri

Non aggiungere troppi [criteri](luis-concept-patterns.md). LUIS apprenderà rapidamente con un minor numero di esempi. Non sovraccaricare il sistema inutilmente.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Non eseguire il training né pubblicare con ogni singola espressione di esempio

Aggiungere 10 o 15 espressioni prima di procedere con il training e la pubblicazione. In questo modo, si vedrà l'impatto sulla precisione della stima. L'aggiunta di una singola espressione potrebbe non avere un impatto visibile sul punteggio.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla [pianificazione dell'app](luis-how-plan-your-app.md) nell'app LUIS.
