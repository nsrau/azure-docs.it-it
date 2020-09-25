---
title: Raccolta dati
description: Informazioni sui dati di esempio da raccogliere durante lo sviluppo dell'app
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: bb00595a5d5be130bf6c1177004bf3042ef8f9f0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327188"
---
# <a name="data-collection-for-your-app"></a>Raccolta dei dati per l'app

Un'app Language Understanding (LUIS) necessita di dati come parte dello sviluppo di app.

## <a name="data-used-in-luis"></a>Dati usati in LUIS

LUIS USA il testo come dati per eseguire il training e il test dell'app LUIS per la classificazione per gli [Intent](luis-concept-intent.md) e per l'estrazione di [entità](luis-concept-entity-types.md). È necessario disporre di un set di dati sufficientemente ampio da disporre di dati sufficienti per creare set di dati separati per il training e il test con la diversità e la distribuzione denominate in modo specifico di seguito.  I dati in ognuno di questi set non devono sovrapporsi.

## <a name="training-data-selection-for-example-utterances"></a>Selezione dei dati di training per espressioni di esempio

Selezionare le espressioni per il set di training in base ai criteri seguenti:

* **I dati reali sono migliori**:
    * **Dati reali dall'applicazione client**: selezionare le espressioni che sono dati reali dall'applicazione client.  Se il cliente invia un modulo Web con la propria richiesta e si sta creando un bot, è possibile iniziare usando i dati del modulo Web.
    * **Dati crowd-sourced**: se non sono presenti dati esistenti, considerare gli enunciati di crowd sourcing.  Provare a ammucchiare le espressioni di origine dalla popolazione di utenti effettiva per lo scenario in uso per ottenere la migliore approssimazione dei dati reali che l'applicazione visualizzerà. Gli enunciati umani di massa sono migliori rispetto alle espressioni generate dal computer.  Quando si compila un set di dati di espressioni sintetiche generate in base a modelli specifici, la variazione naturale che si vedrà con le persone che creano le espressioni non finirà generalizzando correttamente nell'ambiente di produzione.
* **Diversità dei dati**:
    * **Diversità delle aree**: assicurarsi che i dati per ogni finalità siano più diversi possibile, _tra cui_ la formulazione (scelta di parole) e la _grammatica_.  Se si sta insegnando una finalità per i criteri HR sui giorni feriali, verificare che siano presenti espressioni che rappresentano i termini usati per tutte le aree che si servono.  In Europa, ad esempio, è possibile che gli utenti chiedano informazioni su `taking a holiday` e negli Stati Uniti `taking vacation days` .
    * **Diversità della lingua**: se sono presenti utenti con varie lingue native che comunicano in una seconda lingua, assicurarsi di disporre di espressioni che rappresentino altoparlanti non nativi.
    * **Diversità di input**: considerare il percorso di input dei dati. Se si raccolgono dati da una persona, da un reparto o da un dispositivo di input (microfono), probabilmente manca la diversità che sarà importante per l'app per ottenere informazioni su tutti i percorsi di input.
    * **Diversità di punteggiatura**: tenere presente che le persone usano diversi livelli di punteggiatura nelle applicazioni di testo e assicurano una diversità di come viene usata la punteggiatura. Se si usano dati provenienti da sintesi vocale, non saranno presenti punteggiatura, quindi i dati non devono essere.
* **Distribuzione dei dati**: assicurarsi che i dati distribuiti tra gli Intent rappresenti la stessa diffusione dei dati ricevuti dall'applicazione client. Se l'app LUIS classifica le espressioni che sono richieste di pianificazione di un congedo (50%), ma visualizzerà anche le espressioni relative alla domanda sui giorni rimanenti (20%), le foglie di approvazione (20%) e alcuni fuori ambito e Chit Chat (10%) il set di dati dovrebbe quindi includere le percentuali di campionamento di ogni tipo di espressione.
* **Usare tutti i formati di dati**: se l'app Luis prenderà dati in più forme, assicurarsi di includere tali moduli nelle espressioni di training. Se, ad esempio, l'applicazione client accetta sia il riconoscimento vocale che l'input di testo tipizzato, è necessario disporre di espressioni generate da riconoscimento vocale, nonché di espressioni tipizzate.  Verranno visualizzate diverse varianti nel modo in cui le persone parlano dal modo in cui digitano, oltre a diversi errori di riconoscimento vocale e digitazioni.  Tutte queste variazioni dovrebbero essere rappresentate nei dati di training.
* **Esempi positivi e negativi**: per insegnare un'app Luis, è necessario sapere quali sono gli Intent (positivi) e cosa non è (negativo). In LUIS, le espressioni possono essere positive solo per un singolo scopo. Quando un enunciato viene aggiunto a un preventivo, LUIS crea automaticamente lo stesso esempio come espressione di un esempio negativo per tutti gli altri Intent.
* **Dati al di fuori dell'ambito dell'applicazione**: se l'applicazione visualizzerà espressioni che non rientrano negli intenti definiti, assicurarsi di specificarle. Gli esempi non assegnati a un particolare scopo definito verranno contrassegnati con l'intento **None** .  È importante avere esempi realistici per l'intento di **nessuno** di prevedere correttamente le espressioni che esulano dall'ambito degli intenti definiti.

    Ad esempio, se si sta creando un bot HR incentrato sul tempo di congedo e si hanno tre finalità:
    * pianificare o modificare un'uscita
    * Richiedi informazioni sui giorni di uscita disponibili
    * approva/disapprova uscita

    Si desidera assicurarsi di disporre di espressioni che coprano entrambi gli Intent, ma anche che coprono potenziali enunciati al di fuori dell'ambito che l'applicazione deve soddisfare come segue:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Esempi rari**: l'app deve avere esempi rari, oltre ad esempi comuni.  Se l'app non ha mai visto esempi rari, non sarà in grado di identificarli nell'ambiente di produzione. Se si usano dati reali, sarà possibile prevedere in modo più accurato come l'app LUIS funzionerà in produzione.

### <a name="quality-instead-of-quantity"></a>Qualità invece della quantità

Prima di aggiungere altri dati, valutare la qualità dei dati esistenti.  Con LUIS, stai usando l'insegnamento del computer.  La combinazione delle etichette e delle funzionalità di Machine Learning definite è l'uso dell'app LUIS.  Non si basa semplicemente sulla quantità di etichette per eseguire la stima migliore.  La diversità degli esempi e la relativa rappresentazione di ciò che l'app LUIS vedrà nell'ambiente di produzione è la parte più importante.

### <a name="preprocessing-data"></a>Pre-elaborazione dei dati

I seguenti passaggi di pre-elaborazione consentiranno di creare un'app LUIS migliore:

* **Rimuovere i duplicati**: le espressioni duplicate non vengono danneggiate, ma non sono utili, quindi la rimozione consentirà di risparmiare tempo per l'etichettatura.
* **Applicare la stessa pre-elaborazione dell'app client**: se l'applicazione client, che chiama l'endpoint di stima Luis, applica l'elaborazione dei dati in fase di esecuzione prima di inviare il testo a Luis, è necessario eseguire il training dell'app Luis sui dati elaborati nello stesso modo. Ad esempio, se l'applicazione client usa [controllo ortografico Bing](../bing-spell-check/overview.md) per correggere l'ortografia degli input in Luis, correggere le espressioni di training e di test prima di aggiungere a Luis.
* **Non applicare nuovi processi di pulizia che l'app client non usa**: se l'app client accetta direttamente il testo generato dalla sintesi vocale senza alcuna pulizia, ad esempio la grammatica o la punteggiatura, le espressioni devono riflettere lo stesso, inclusi eventuali punteggiatura mancanti e eventuali altri riconoscimenti che è necessario tenere in considerazione.
* **Non pulire i dati**: non eliminare l'input in formato non corretto che si potrebbe ottenere da un riconoscimento vocale confuso, un tasto di pressione accidentale o un testo digitato in modo errato. Se l'app visualizzerà input come questi, è importante che venga sottoposto a training e testato su di essi. Aggiungere un intento di _input in formato non valido_ se non si prevede che l'app la capisca. Etichettare questi dati per consentire all'app LUIS di prevedere la risposta corretta in fase di esecuzione. L'applicazione client può scegliere una risposta appropriata a espressioni non comprensibili, ad esempio `Please try again` .

### <a name="labeling-data"></a>Assegnazione di etichette ai dati

* **Testo etichetta come se fosse corretto**: le espressioni di esempio dovrebbero avere tutte le forme di un'entità etichettata. Questo include testo non digitato correttamente, digitato erroneamente e tradotto in errore.

### <a name="data-review-after-luis-app-is-in-production"></a>Revisione dei dati dopo l'applicazione LUIS in produzione

[Esaminare le espressioni dell'endpoint](luis-concept-review-endpoint-utterances.md) per monitorare il traffico di espressioni reali dopo aver distribuito un'app nell'ambiente di produzione.  In questo modo è possibile aggiornare le espressioni di training con dati reali, migliorando così l'app. Qualsiasi app compilata con i dati di uno scenario non più affollati o non reali deve essere migliorata in base al suo reale uso.

## <a name="test-data-selection-for-batch-testing"></a>Selezione dei dati di test per test batch

Tutti i principi elencati sopra per le espressioni di formazione si applicano a espressioni che è necessario usare per il [set di test](luis-concept-batch-test.md). Assicurarsi che la distribuzione tra gli Intent e le entità rispecchi la distribuzione reale il più possibile.

Non riutilizzare le espressioni del set di training nel set di test. Questa operazione polarizza in modo errato i risultati e non fornisce l'indicazione corretta del modo in cui l'app LUIS verrà eseguita nell'ambiente di produzione.

Una volta pubblicata la prima versione dell'app, è necessario aggiornare il set di test con le espressioni dal traffico reale per garantire che il set di test rispecchi la distribuzione di produzione ed è possibile monitorare le prestazioni realistiche nel tempo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scopri in che modo LUIS modifica i dati prima della stima](luis-concept-data-alteration.md)
