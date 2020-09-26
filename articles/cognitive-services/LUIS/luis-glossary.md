---
title: Glossario-LUIS
description: Il glossario contiene termini che è probabile trovare durante l'uso del servizio API LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: 0f17e489cabb047ca2e9f0ad7406c34cd292e556
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309490"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossario per la comprensione del linguaggio con vocabolario e concetti comuni
Il Glossario di Language Understanding (LUIS) illustra i termini che possono verificarsi durante l'utilizzo del servizio LUIS.

## <a name="active-version"></a>Versione attiva

La versione attiva è la [versione](luis-how-to-manage-versions.md) dell'app che viene aggiornata quando si apportano modifiche al modello tramite il portale Luis. Nel portale LUIS, se si desidera apportare modifiche a una versione che non è la versione attiva, è necessario prima impostare tale versione come attiva.

## <a name="active-learning"></a>Apprendimento attivo

L'apprendimento attivo è una tecnica di apprendimento automatico in cui viene usato il modello di apprendimento automatico per identificare nuovi esempi informativi da etichettare. In LUIS, l'apprendimento attivo si riferisce all'aggiunta di espressioni dal traffico dell'endpoint le cui stime correnti non sono chiare per migliorare il modello. Fare clic su "verifica espressioni endpoint" per visualizzare le espressioni da etichettare.

Vedere anche la pagina relativa alla
* [Informazioni concettuali](luis-concept-review-endpoint-utterances.md)
* [Esercitazione per la revisione di espressioni endpoint](luis-tutorial-review-endpoint-utterances.md)
* Come migliorare l'app LUIS esaminando gli [enunciati degli endpoint](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Applicazione (app)

In LUIS, l'applicazione, o l'app, è una raccolta di modelli di Machine Learn, basata sullo stesso set di dati, che interagisce per stimare le finalità e le entità per uno scenario specifico. Ogni applicazione dispone di un endpoint di stima separato.

Se si sta compilando un bot HR, è possibile che si disponga di un set di Intent, ad esempio "tempo di congedo pianificazione", "Richiedi i vantaggi" e "Aggiorna informazioni personali" ed entità per ognuno di questi Intent raggruppati in un'unica applicazione.

## <a name="authoring"></a>Creazione

La creazione è la possibilità di creare, gestire e distribuire un'app LUIS, usando il portale LUIS o le API di creazione.

### <a name="authoring-key"></a>Chiave di creazione

La [chiave di creazione](luis-how-to-azure-subscription.md) viene usata per creare l'app. Non utilizzata per le query di endpoint a livello di produzione. Per ulteriori informazioni, vedere [Limiti della chiave](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Creazione di una risorsa

La [risorsa Luis authoring](luis-how-to-azure-subscription.md#azure-resources-for-luis) è un elemento gestibile disponibile tramite Azure. La risorsa è l'accesso alle funzionalità di creazione, formazione e pubblicazione associate del servizio di Azure. La risorsa include le informazioni di autenticazione, autorizzazione e sicurezza necessarie per accedere al servizio di Azure associato.

La risorsa di creazione ha una "tipologia" di Azure `LUIS-Authoring` .

## <a name="batch-test"></a>Test in batch

Il test batch è la possibilità di convalidare i modelli di un'app LUIS corrente con un set di test coerente e noto di espressioni utente. Il test batch viene definito in un [file in formato JSON](luis-concept-batch-test.md#batch-file-format).

Vedere anche la pagina relativa alla
* [Concetti](luis-concept-batch-test.md)
* [Procedura:](luis-how-to-batch-test.md) eseguire un test batch
* [Esercitazione](luis-tutorial-batch-testing.md) : creare ed eseguire un test batch

### <a name="f-measure"></a>Misura F

Nei test batch è una misura dell'accuratezza del test.

### <a name="false-negative-fn"></a>Falso negativo (FN)

Nei test in batch i punti dati rappresentano espressioni in cui l'app ha erroneamente stimato l'assenza della finalità/entità di destinazione.

### <a name="false-positive-fp"></a>Falso positivo (FP)

Nei test in batch i punti dati rappresentano espressioni in cui l'app ha erroneamente stimato la presenza della finalità/entità di destinazione.

### <a name="precision"></a>Precision
Nei test in batch, la precisione (denominata anche valore predittivo positivo) è la frazione delle espressioni rilevanti tra le espressioni recuperate.

Un esempio di test batch di animali è il numero di pecore stimate divise per il numero totale di animali (pecore e non pecore).

### <a name="recall"></a>Recall

Nei test in batch il richiamo (anche detto sensibilità) è la capacità di LUIS di generalizzare.

Un esempio di test batch di animali è il numero di pecore stimate divise per il numero totale di pecore disponibili.

### <a name="true-negative-tn"></a>Falso negativo (TN)

Un vero negativo è quando l'app stima correttamente nessuna corrispondenza. Nei test batch, un vero negativo si verifica quando l'app stima uno scopo o un'entità per un esempio che non è stato etichettato con tale finalità o entità.

### <a name="true-positive-tp"></a>Falso positivo (TP)

Vero positivo (TP) un vero positivo è quando l'app stima correttamente una corrispondenza. Nei test batch, un vero positivo si verifica quando l'app stima uno scopo o un'entità per un esempio che è stato etichettato con tale finalità o entità.

## <a name="classifier"></a>Classificatore

Un classificatore è un modello di apprendimento automatico che prevede la categoria o la classe in cui si inserisce un input.

Uno [scopo](#intent) è un esempio di classificatore.

## <a name="collaborator"></a>Collaboratore

Un collaboratore è concettualmente [uguale a quello](#contributor)di un collaboratore. Un collaboratore può accedere quando un proprietario aggiunge l'indirizzo di posta elettronica del collaboratore a un'app che non è controllata con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure). Se si stanno ancora usando collaboratori, è necessario eseguire la migrazione dell'account LUIS e usare le risorse di authoring LUIS per gestire i collaboratori con RBAC.

## <a name="contributor"></a>Collaboratore

Un collaboratore non è il [proprietario](#owner) dell'app, ma ha le stesse autorizzazioni per aggiungere, modificare ed eliminare gli Intent, le entità, le espressioni. Un collaboratore fornisce il controllo degli accessi in base al ruolo di Azure (RBAC) a un'app LUIS.

Vedere anche la pagina relativa alla
* [Procedura:](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) aggiungere collaboratori

## <a name="descriptor"></a>Descrittore

Un descrittore è il termine usato in precedenza per una [funzionalità](#features)di machine learning.

## <a name="domain"></a>Dominio

Nel contesto di LUIS, un dominio è un'area di conoscenza. Il dominio è specifico per lo scenario. Domini diversi usano linguaggi e terminologia specifici che hanno un significato nel contesto del dominio. Se, ad esempio, si sta compilando un'applicazione per la riproduzione di musica, l'applicazione avrà termini e linguaggi specifici per la musica: parole come "Song, Track, album, lyrics, b-side, Artist". Per esempi di domini, vedere [domini predefiniti](#prebuilt-domain).

## <a name="endpoint"></a>Endpoint

### <a name="authoring-endpoint"></a>Endpoint di creazione

L'URL dell'endpoint di authoring LUIS è il punto in cui si crea, si esegue il training e si pubblica l'app. L'URL dell'endpoint contiene l'area o il sottodominio personalizzato dell'app pubblicata e l'ID app.

Altre informazioni sulla creazione di un'app a livello di codice dal riferimento per gli [sviluppatori](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Endpoint di stima

L'URL dell'endpoint di stima LUIS è il punto in cui si inviano le query LUIS dopo che l' [app Luis](#application-app) è stata creata e pubblicata. L'URL dell'endpoint contiene l'area o il sottodominio personalizzato dell'app pubblicata e l'ID app. È possibile trovare l'endpoint nella pagina delle **[risorse di Azure](luis-how-to-azure-subscription.md)** dell'app oppure è possibile ottenere l'URL dell'endpoint dall'API [Get app info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

L'accesso all'endpoint di stima è autorizzato con la chiave di stima LUIS.

## <a name="entity"></a>Entità

Le [entità](luis-concept-entity-types.md) sono parole in espressioni che descrivono le informazioni usate per soddisfare o identificare uno scopo. Se l'entità è complessa e si desidera che il modello identifichi parti specifiche, è possibile suddividere il modello in sottoentità. È possibile, ad esempio, che si desideri modellare per stimare un indirizzo, ma anche le sottoentità di via, città, stato e Cap. Le entità possono essere utilizzate anche come funzionalità per i modelli. La risposta dell'app LUIS includerà sia gli Intent previsti che tutte le entità.

### <a name="entity-extractor"></a>Estrattore entità

Un estrattore di entità talvolta noto solo come estrattore è il tipo di modello di computer imparato che LUIS USA per stimare le entità.

### <a name="entity-schema"></a>Schema dell'entità

Lo schema dell'entità è la struttura definita per le entità apprese dal computer con sottoentità. L'endpoint di stima restituisce tutte le entità estratte e le sottoentità definite nello schema.

### <a name="entitys-subentity"></a>Sottoentità dell'entità

Una sottoentità è un'entità figlio di un'entità di machine learning.

### <a name="non-machine-learning-entity"></a>Entità non Machine Learning

Entità che utilizza la corrispondenza del testo per estrarre i dati:
* Entità elenco
* Entità di espressione regolare

### <a name="list-entity"></a>Entità elenco

Un' [entità list](reference-entity-list.md) rappresenta un set fisso e chiuso di parole correlate insieme ai relativi sinonimi. Le entità dell'elenco sono corrispondenze esatte, a differenza delle entità apprese dal computer.

L'entità verrà stimata se una parola nell'entità elenco è inclusa nell'elenco. Se, ad esempio, si dispone di un'entità list denominata "size" e si hanno le parole "Small, medium, large" nell'elenco, l'entità Size verrà stimata per tutte le espressioni in cui vengono usate le parole "Small", "medium" o "large", indipendentemente dal contesto.

### <a name="regular-expression"></a>Espressione regolare‏

Un' [entità di espressione regolare](reference-entity-regular-expression.md) rappresenta un'espressione regolare. Le entità delle espressioni regolari sono corrispondenze esatte, a differenza delle entità apprese dal computer.
### <a name="prebuilt-entity"></a>Entità predefinita

Vedere la voce del modello predefinito per l' [entità](#prebuilt-entity) precompilata

## <a name="features"></a>Funzionalità

In Machine Learning, una caratteristica è una caratteristica che consente al modello di riconoscere un particolare concetto. È un suggerimento che LUIS può usare, ma non una regola rigida.

Questo termine viene anche definito **[funzionalità di Machine Learning](luis-concept-feature.md)**.

Questi hint vengono usati insieme alle etichette per apprendere come prevedere i nuovi dati. LUIS supporta entrambi gli elenchi di frasi e l'uso di altri modelli come funzionalità.

### <a name="required-feature"></a>Funzionalità obbligatoria

Una funzionalità obbligatoria è un modo per vincolare l'output di un modello LUIS. Quando una funzionalità per un'entità è contrassegnata come obbligatoria, è necessario che la funzionalità sia presente nell'esempio per l'entità da stimare, indipendentemente dalle stime del modello appreso dal computer.

Si consideri un esempio in cui è presente una funzionalità di numero predefinito contrassegnata come richiesta nell'entità Quantity per un bot per l'ordine dei menu. Quando il bot vede `I want a bajillion large pizzas?` , bajillion non verrà stimato come quantità indipendentemente dal contesto in cui viene visualizzato. Bajillion non è un numero valido e non verrà stimato dal numero di entità precompilate.

## <a name="intent"></a>Finalità

Uno [scopo](luis-concept-intent.md) rappresenta un'attività o un'azione che l'utente desidera eseguire. Si tratta di uno scopo o obiettivo espresso nell'input di un utente, ad esempio la prenotazione di un volo o il pagamento di una fattura. In LUIS, un enunciato nel suo complesso è classificato come scopo, ma le parti dell'espressione vengono estratte come entità

## <a name="labeling-examples"></a>Esempi di etichette

L'assegnazione di etichette o il contrassegno è il processo di associazione di un esempio positivo o negativo a un modello.

### <a name="labeling-for-intents"></a>Assegnazione di etichette per gli Intent
In LUIS, gli Intent all'interno di un'app si escludono a vicenda. Ciò significa che quando si aggiunge un enunciato a un preventivo, viene considerato un esempio _positivo_ per tale finalità e un esempio _negativo_ per tutti gli altri scopi. Gli esempi negativi non devono essere confusi con lo scopo "None", che rappresenta espressioni che esulano dall'ambito dell'app.

### <a name="labeling-for-entities"></a>Assegnazione di etichette per le entità
In LUIS si [etichetta](label-entity-example-utterance.md) una parola o una frase nell'espressione di esempio di un Intent con un'entità come esempio _positivo_ . L'assegnazione di etichette Mostra lo scopo che deve prevedere per tale espressione. Le espressioni con etichetta vengono usate per eseguire il training dello scopo.

## <a name="luis-app"></a>App LUIS

Vedere la definizione dell' [applicazione (app)](#application-app).

## <a name="model"></a>Modello

Un modello (Machine Learn) è una funzione che esegue una stima sui dati di input. In LUIS, si fa riferimento ai classificatori intenzionali e agli estrattori di entità in modo generico come "modelli" e si fa riferimento a una raccolta di modelli sottoposti a training, pubblicati e sottoposti a query insieme come "app".

## <a name="normalized-value"></a>Valore normalizzato

È possibile aggiungere valori alle entità dell' [elenco](#list-entity) . Ognuno di questi valori può avere un elenco di uno o più sinonimi. Nella risposta viene restituito solo il valore normalizzato.

## <a name="overfitting"></a>Overfitting

L'overfitting si verifica quando il modello è fissato agli esempi specifici e non è in grado di generalizzare correttamente.

## <a name="owner"></a>Proprietario

Ogni app ha un proprietario, ossia la persona che ha creato l'app. Il proprietario gestisce le autorizzazioni per l'applicazione nella portale di Azure.

## <a name="phrase-list"></a>Elenco di frasi

Un [elenco di frasi](luis-concept-feature.md) è un tipo specifico di funzionalità di Machine Learning che include un gruppo di valori (parole o frasi) che appartengono alla stessa classe e che devono essere trattati in modo analogo (ad esempio, nomi di città o prodotti).

## <a name="prebuilt-model"></a>Modello predefinito

Un [modello predefinito](luis-concept-prebuilt-model.md) è un Intent, un'entità o una raccolta di entrambi, insieme ad esempi con etichetta. Questi modelli predefiniti comuni possono essere aggiunti all'app per ridurre il lavoro di sviluppo del modello necessario per l'app.

### <a name="prebuilt-domain"></a>Dominio predefinito

Un dominio predefinito è un'app LUIS configurata per un dominio specifico, ad esempio di domotica (HomeAutomation) o di prenotazione di ristoranti (RestaurantReservation). Le finalità, le espressioni e le entità sono configurate per questo dominio.

### <a name="prebuilt-entity"></a>Entità predefinita

Un'entità predefinita è un'entità fornita da LUIS per tipi di informazioni comuni quali numeri, URL e posta elettronica. Questi vengono creati in base ai dati pubblici. È possibile scegliere di aggiungere un'entità predefinita come entità autonoma o come funzionalità a un'entità

### <a name="prebuilt-intent"></a>Finalità precompilata

Una finalità precompilata è costituita da una finalità che LUIS fornisce per i tipi di informazioni comuni e con le proprie espressioni di esempio con etichetta.

## <a name="prediction"></a>Stima

Una stima è una richiesta REST al servizio di stima di Azure LUIS che accetta i nuovi dati (enunciato dall'utente) e applica l'applicazione sottoposta a training e pubblicata a tali dati per determinare quali Intent ed entità vengono trovati.

### <a name="prediction-key"></a>Chiave di stima

La [chiave di stima](luis-how-to-azure-subscription.md) , nota in precedenza come chiave di sottoscrizione, è la chiave associata al servizio Luis creato in Azure che autorizza l'utilizzo dell'endpoint di stima.

Questa chiave non è la chiave di crezione. Se si dispone di una chiave dell'endpoint di stima, è consigliabile utilizzarla per tutte le richieste endpoint anziché per la chiave di creazione. È possibile visualizzare la chiave di stima corrente all'interno dell'URL dell'endpoint nella parte inferiore della pagina delle risorse di Azure nel sito Web LUIS. Si tratta del valore della coppia nome/valore della chiave di sottoscrizione.

### <a name="prediction-resource"></a>Risorsa di stima

La risorsa LUIS Prediction è un elemento gestibile disponibile tramite Azure. La risorsa è l'accesso alla stima associata del servizio di Azure. La risorsa include stime.

La risorsa di stima ha una "tipologia" di Azure `LUIS` .

### <a name="prediction-score"></a>Punteggio di previsione

Il [Punteggio](luis-concept-prediction-score.md) è un numero compreso tra 0 e 1 che rappresenta una misura della fiducia del sistema nel fatto che una particolare espressione di input corrisponde a un particolare scopo. Un punteggio più vicino a 1 indica che il sistema è molto sicuro sull'output e un punteggio più vicino a 0 indica che il sistema è fiducioso che l'input non corrisponda a un determinato output. I punteggi in media indicano che il sistema non è sicuro di come prendere la decisione.

Si prenda, ad esempio, un modello usato per identificare se il testo del cliente include un ordine di cibo. Potrebbe dare un punteggio pari a 1 per "Vorrei ordinare un caffè" (il sistema è molto sicuro che si tratta di un ordine) e un punteggio pari a 0 per "il team ha vinto il gioco la notte scorsa" (il sistema è molto fiducioso che non si tratta di un ordine). E potrebbe avere un punteggio di 0,5 per "Let ' s have some tea" (non è certo che si tratti di un ordine o meno).

## <a name="programmatic-key"></a>Chiave programmatica

Rinominata [chiave di creazione](#authoring-key).

## <a name="publish"></a>Pubblica

Per [pubblicazione](luis-how-to-publish-app.md) si intende rendere disponibile una versione di Luis Active nell' [endpoint](#endpoint)di gestione temporanea o di produzione.

## <a name="quota"></a>Quota

La quota LUIS è la limitazione del livello di sottoscrizione di Azure. La quota LUIS può essere limitata sia dalle richieste al secondo (stato HTTP 429) che dalle richieste totali in un mese (stato HTTP 403).

## <a name="schema"></a>SCHEMA

Lo schema include gli Intent e le entità insieme alle sottoentità. Lo schema viene inizialmente pianificato per l'iterazione nel tempo. Lo schema non include le impostazioni dell'app, le funzionalità o le espressioni di esempio.

## <a name="sentiment-analysis"></a>Analisi del sentiment
L'analisi del sentiment fornisce valori positivi o negativi delle espressioni fornite da [Analisi del testo](../text-analytics/overview.md).

## <a name="speech-priming"></a>Priming del riconoscimento vocale

L'innesco vocale migliora il riconoscimento di parole e frasi pronunciate comunemente usate nello scenario con servizi di [riconoscimento vocale](../speech-service/overview.md). Per le applicazioni abilitate per l'attivazione del riconoscimento vocale, tutti gli esempi etichettati di LUIS vengono usati per migliorare l'accuratezza del riconoscimento vocale creando un modello di riconoscimento vocale personalizzato per questa applicazione specifica. Ad esempio, in un gioco di scacchi è necessario assicurarsi che, quando l'utente dice "Move Knight", non venga interpretato come "Move Night". L'app LUIS deve includere esempi in cui "knight" è etichettato come entità.

## <a name="starter-key"></a>Chiave di avvio

Chiave gratuita da usare per il primo avvio con LUIS.

## <a name="synonyms"></a>Sinonimi

Nelle entità di LUIS [List](reference-entity-list.md)è possibile creare un valore normalizzato, che può avere un elenco di sinonimi. Se, ad esempio, si crea un'entità size con valori normalizzati di Small, medium, large e extra large. È possibile creare sinonimi per ogni valore analogo al seguente:

|Valore Nomalized| Sinonimi|
|--|--|
|Piccolo| il piccolo, 8 once|
|Media| normale, 12 once|
|Grande| grande, 16 once|
|Xtra Large| quello più grande, 24 once|

Il modello restituirà il valore normalizzato per l'entità quando uno dei sinonimi viene visualizzato nell'input.

## <a name="test"></a>Test

Il [test](luis-concept-test.md) di un'app Luis significa visualizzare le stime del modello.

## <a name="timezone-offset"></a>Differenza fuso orario

L'endpoint include [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). È il numero di minuti da aggiungere o rimuovere dall'entità predefinita datetimeV2. Ad esempio, se l'espressione è "che ore sono?", il valore datetimeV2 restituito è l'ora corrente per la richiesta del client. Se la richiesta client proviene da un bot o un'altra applicazione che non corrisponde all'utente del bot, si deve passare la differenza tra il bot e l'utente.

Vedere [Cambiare il fuso orario dell'entità datetimeV2 predefinita](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>token
Un [token](luis-language-support.md#tokenization) è la più piccola unità di testo che Luis può riconoscere. Questo comportamento differisce leggermente tra le lingue.

Per l' **inglese**, un token è un intervallo continuo (senza spazi o punteggiatura) di lettere e numeri. Uno spazio non è un token.

|Frase|Conteggio token|Spiegazione|
|--|--|--|
|`Dog`|1|Una singola parola senza segni di punteggiatura o spazi.|
|`RMT33W`|1|Numero del localizzatore di record. Può contenere numeri e lettere, ma non presenta segni di punteggiatura.|
|`425-555-5555`|5|Numero di telefono. Ogni segno di punteggiatura è  `425-555-5555` costituito da un singolo token, quindi da 5 token:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Eseguire il training

Il [Training](luis-how-to-train.md) è il processo che consente di insegnare a Luis le modifiche apportate alla versione attiva dopo l'ultimo training.

### <a name="training-data"></a>Dati di training

I dati di training sono il set di informazioni necessarie per eseguire il training di un modello. Sono inclusi lo schema, le espressioni con etichetta, le funzionalità e le impostazioni dell'applicazione.

### <a name="training-errors"></a>Errori di training

Gli errori di training sono stime dei dati di training che non corrispondono alle rispettive etichette.

## <a name="utterance"></a>Espressione

Un [enunciato](luis-concept-utterance.md) è un input utente che è un testo breve rappresentativo di una frase in una conversazione. Si tratta di una frase in linguaggio naturale, ad esempio "Book 2 Tickets to Seattle Next Tuesday". Vengono aggiunte espressioni di esempio per eseguire il training del modello e il modello esegue la stima in un nuovo enunciato in fase di esecuzione

## <a name="version"></a>Versione

Una [versione](luis-how-to-manage-versions.md) Luis è un'istanza specifica di un'applicazione Luis associata a un ID app Luis e all'endpoint pubblicato. Ogni app LUIS ha almeno una versione.
