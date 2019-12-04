---
title: "Esercitazione: Estrarre dati strutturati con un'entità basata su Machine Learning - LUIS"
titleSuffix: Azure Cognitive Services
description: Estrarre dati strutturati da un'espressione usando l'entità basata su Machine Learning. Per aumentare l'accuratezza dell'estrazione, aggiungere sottocomponenti con descrittori e vincoli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 913fa3c846ea00649a584be02975fdde449dc7cf
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383298"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Esercitazione: Estrarre dati strutturati da un'espressione dell'utente con entità basate su Machine Learning in Language Understanding (LUIS)

In questa esercitazione vengono estratti dati strutturati da un'espressione usando l'entità basata su Machine Learning. 

L'entità basata su Machine Learning supporta il [concetto di scomposizione del modello](luis-concept-model.md#v3-authoring-model-decomposition) fornendo entità sottocomponenti con i relativi descrittori e vincoli. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Importare l'app di esempio
> * Aggiungere un'entità basata su Machine Learning 
> * Aggiungere un sottocomponente
> * Aggiungere il descrittore del sottocomponente
> * Aggiungere il vincolo del sottocomponente
> * Eseguire il training dell'app
> * Testare l'app
> * Pubblicare l'app
> * Ottenere la stima dell'entità dall'endpoint

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Perché usare un'entità basata su Machine Learning?

Questa esercitazione aggiunge un'entità basata su Machine Learning per estrarre dati da un'espressione. 

Lo scopo di un'entità è definire i dati da estrarre. Ciò include la specifica di un nome e di un tipo (se possibile) per i dati, di una risoluzione dei dati in caso di ambiguità e del testo esatto che compone i dati. 

Per definire l'entità, è necessario creare l'entità e quindi assegnare un'etichetta al testo che rappresenta l'entità nell'espressione di esempio. Questi esempi con etichetta insegnano a LUIS qual è l'entità e dove può trovarsi in un'espressione. 

## <a name="entity-decomposability-is-important"></a>La scomponibilità delle entità è importante

La scomponibilità delle entità è importante sia per la stima delle finalità che per l'estrazione dei dati. 

Iniziare con un'entità basata su Machine Learning, che rappresenta l'entità iniziale e di primo livello per l'estrazione dei dati. Quindi scomporre l'entità nelle parti richieste dall'applicazione client. 

Sebbene non sia possibile sapere quanto dettagliata debba essere l'entità quando si inizia a creare l'app, è consigliabile iniziare con un'entità basata su Machine Learning e quindi scomporre con i sottocomponenti man mano che l'app cresce.

In pratica, si creerà un'entità basata su Machine Learning per rappresentare un ordine per un'app per la pizza. L'ordine deve contenere tutte le parti necessarie per compilare l'ordine. Per iniziare, l'entità estrae il testo correlato all'ordine, insieme a dimensione e quantità. 

Un'espressione per `Please deliver one large cheese pizza to me` dovrebbe estrarre `one large cheese pizza` sotto forma di ordine e quindi estrarre anche `1` e `large`. 

È possibile aggiungere altre operazioni di scomposizione, ad esempio la creazione di sottocomponenti per condimenti o impasto. Dopo questa esercitazione, si sarà in grado di aggiungere questi sottocomponenti all'entità `Order` esistente.

## <a name="import-example-json-to-begin-app"></a>Importare il file con estensione json per iniziare l'app

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

1. Nel [portale LUIS di anteprima](https://preview.luis.ai) nella pagina **My apps** (App personali) selezionare **Import** (Importa), quindi **Import as JSON** (Importa come JSON). Trovare il file JSON salvato nel passaggio precedente. Non è necessario modificare il nome dell'app. Selezionare **Operazione completata**

1. Nella scheda **Versions** (Versioni) della sezione **Manage** (Gestisci) selezionare la versione, quindi selezionare **Clone** (Clona) per clonare la versione e denominarla `mach-learn` e infine selezionare **Done** (Fine) per completare il processo di clonazione. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

    > [!TIP] 
    > La clonazione di una versione in una nuova versione è una procedura consigliata prima di modificare l'app. Dopo aver completato una versione, esportare la versione, come file con estensione json o lu, e archiviare il file nel sistema di controllo del codice sorgente.

1. Selezionare **Build** (Compila) e quindi **Intents** (Finalità) per visualizzare le finalità, ovvero i blocchi principali di un'app LUIS.

    ![Passare dalla pagina Versions (Versioni) alla pagina Intents (Finalità).](media/tutorial-machine-learned-entity/new-version-imported-app.png)

## <a name="label-text-as-entities-in-example-utterances"></a>Contrassegnare il testo come entità nelle espressioni di esempio

Per estrarre i dettagli relativi a un ordine di pizza, creare un'entità `Order` basata su Machine Learning di primo livello.

1. Nella pagina **Intents** (Finalità) selezionare la finalità **OrderPizza**. 

1. Nell'elenco delle espressioni di esempio selezionare l'espressione seguente. 

    |Espressione di esempio dell'ordine|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Iniziare effettuando la selezione immediatamente prima del testo più a sinistra `pickup` (#1) quindi immediatamente dopo il testo più a destra `anchovies` (#2 - fine del processo di etichettatura). Viene visualizzato un menu a comparsa. Nella casella popup immettere il nome dell'entità `Order` (#3). Quindi selezionare `Order - Create new entity` dall'elenco (#4).

    ![Assegnare un'etichetta all'inizio e alla fine del testo per l'ordine completo](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Un'entità non sarà sempre l'intera espressione. In questo caso specifico, `pickup` indica la modalità di ricezione dell'ordine. Dal punto di vista concettuale, `pickup` deve far parte dell'entità etichettata per l'ordine. 

1. Nella casella **Choose an entity type** (Scegli un tipo di entità) selezionare **Add Structure** (Aggiungi struttura), quindi selezionare **Next** (Avanti). La struttura è necessaria per aggiungere i sottocomponenti, ad esempio dimensione e quantità.

    ![Aggiungere la struttura all'entità](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Nella casella **Create a machine learned entity** (Crea entità basata su Machine Learning) nella casella **Structure** (Struttura) aggiungere `Size` quindi premere INVIO. 
1. Per aggiungere un **descrittore**, selezionare `+` nell'area **Descriptors for Size** (Descrittori per dimensione), quindi selezionare **Create new phrase list** (Crea nuovo elenco frasi).

1. Nella casella **Create new phrase list descriptor** (Crea descrittore nuovo elenco frasi) immettere il nome `SizeDescriptor` quindi immettere i valori `small`, `medium` e `large`. Quando viene compilata la casella **Suggestions** (Suggerimenti), selezionare `extra large` e `xl`. Selezionare **Done** (Fine) per creare il nuovo elenco frasi. 

    Questo descrittore dell'elenco frasi consente al sottocomponente `Size` di individuare le parole correlate alla dimensione fornendo parole di esempio. Questo elenco non deve includere tutte le parole dimensione ma deve includere parole che dovrebbero indicare le dimensioni. 

    ![Creare un descrittore per il sottocomponente dimensione](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Nella finestra **Create a machine learned entity** (Crea entità basata su Machine Learning) selezionare **Create** (Crea) per completare la creazione del sottocomponente `Size`.  

    Viene creata l'entità `Order` con un componente `Size` ma viene applicata all'espressione solo l'entità `Order`. È necessario assegnare un'etichetta al testo dell'entità `Size` nell'espressione di esempio. 

1. Nella stessa espressione di esempio assegnare un'etichetta al sottocomponente **Size** (Dimensione) di `large` selezionando la parola e quindi selezionando l'entità **Size** (Dimensione) dall'elenco a discesa. 

    ![Assegnare un'etichetta all'entità della dimensione per il testo dell'espressione.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    La linea sotto il testo è continua poiché l'etichetta e la stima corrispondono poiché è stata assegnata un'etichetta in modo esplicito al testo.

1. Assegnare un'etichetta all'entità `Order` nelle espressioni rimanenti insieme all'entità della dimensione. Le parentesi quadre nel testo indicano l'entità `Order` con etichetta e l'entità `Size` all'interno.

    |Espressioni di esempio dell'ordine|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Creare entità e sottocomponenti in tutte le espressioni di esempio rimanenti.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Come trattare i dati impliciti, ad esempio la lettera `a` che indica una sola pizza? Oppure l'assenza di `pickup` e `delivery` per indicare la posizione in cui è prevista la pizza? Oppure l'assenza di una dimensione per indicare la dimensione predefinita piccola o grande? Considerare la gestione del trattamento dei dati impliciti come parte delle regole business nell'applicazione client al posto di LUIS o in aggiunta a LUIS. 

1. Per eseguire il training dell'app, selezionare **Train** (Esegui il training). Il training applica le modifiche, ad esempio le nuove entità e le espressioni con etichetta, al modello attivo.

1. Dopo aver eseguito il training, aggiungere una nuova espressione di esempio alla finalità per vedere in che modo LUIS riconosce l'entità basata su Machine Learning. 

    |Espressione di esempio dell'ordine|
    |--|
    |`pickup XL meat lovers pizza`|

    L'entità di primo livello `Order` ha un'etichetta e anche il sottocomponente `Size` ha un'etichetta con linee tratteggiate. La stima è corretta. 

    ![Nuova espressione di esempio stimata con l'entità](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    La linea tratteggiata indica la stima. 

1. Per modificare la stima in un'entità etichettata, selezionare la riga e quindi selezionare **Confirm entity predictions** (Conferma stime entità).

    ![Accettare la stima selezionando Confirm entity prediction (Conferma stima entità).](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    A questo punto, l'entità basata su Machine Learning funziona poiché è in grado di trovare l'entità all'interno di una nuova espressione di esempio. Quando si aggiungono espressioni di esempio, se l'entità non è stimata correttamente, assegnare un'etichetta all'entità e ai sottocomponenti. Se l'entità è stimata correttamente, assicurarsi di confermare le stime. 

## <a name="add-prebuilt-number-to-help-extract-data"></a>Aggiungere un numero predefinito per facilitare l'estrazione dei dati

Le informazioni sull'ordine devono includere anche il numero di unità di un elemento nell'ordine, ad esempio il numero di pizze. Per estrarre questi dati, è necessario aggiungere un nuovo sottocomponente basato su Machine Learning a `Order` e specificare un vincolo di un numero predefinito per il componente. Vincolando l'entità a un numero predefinito, l'entità trova ed estrae numeri quando il testo è una cifra, `2` oppure un testo, `two`.

Iniziare aggiungendo l'entità numero predefinito all'app. 

1. Selezionare **Entities** (Entità) dal menu di sinistra, quindi selezionare **+ Add prebuilt entity** (+ Aggiungi entità predefinita). 

1. Nella casella **Add prebuilt entities** (Aggiungi entità predefinite) cercare e selezionare **number** (numero) quindi selezionare **Done** (Fine). 

    ![Aggiungere entità predefinite](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    L'entità predefinita viene aggiunta all'app, ma non è ancora un vincolo. 

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>Creare un'entità sottocomponente con vincolo per facilitare l'estrazione dei dati

L'entità `Order` deve avere un sottocomponente `Quantity` per determinare il numero di unità di un elemento nell'ordine. La quantità deve essere vincolata a un numero in modo che i dati estratti siano immediatamente utilizzabili dall'applicazione client. 

Un vincolo viene applicato come corrispondenza di testo, con corrispondenza esatta (ad esempio un'entità dell'elenco) o tramite espressioni regolari (ad esempio un'entità espressione regolare o un'entità predefinita). 

Usando un vincolo, viene estratto solo il testo che corrisponde a tale vincolo. 

1. Selezionare **Entities** (Entità) quindi selezionare l'entità `Order`. 
1. Selezionare **+ Add Component** (+ Aggiungi componente) quindi immettere il nome `Quantity` e premere INVIO per aggiungere la nuova entità all'app.
1. Dopo la notifica di esito positivo, selezionare il sottocomponente `Quantity` quindi selezionare la matita del vincolo.
1. Nell’elenco a discesa selezionare il numero predefinito. 

    ![Creare un'entità quantità con un numero predefinito come vincolo.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    L'entità `Quantity` viene applicata solo se viene trovato un testo corrispondente all'entità numero predefinito.

    L'entità con il vincolo è stata creata ma non ancora applicata alle espressioni di esempio.

    > [!NOTE]
    > Un sottocomponente può essere annidato all'interno di un sottocomponente fino a 5 livelli. Sebbene non sia illustrato in questo articolo, è disponibile dal portale e dall'API.  

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Etichettare un'espressione di esempio per inviare a LUIS dati sull'entità

1. Selezionare **Intents** (Finalità) nel riquadro di spostamento a sinistra, quindi selezionare la finalità **OrderPizza**. I tre numeri nelle espressioni seguenti hanno un'etichetta ma sono visualizzati sotto la riga dell'entità `Order`. Questo livello inferiore indica che le entità vengono rilevate, ma non sono considerate separate dall'entità `Order`.

    ![Il numero predefinito è stato trovato ma non è ancora considerato separato dall'entità Order (Ordine).](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Assegnare un'etichetta ai numeri con l'entità `Quantity` selezionando `2` nell'espressione di esempio, quindi selezionando `Quantity` dall'elenco. Assegnare un'etichetta a `6` e `1` nella stessa espressione di esempio.

    ![Assegnare un'etichetta al testo con l'entità quantità.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)  

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Eseguire il training dell'app per applicare le modifiche alle entità all'app

Selezionare **Train** (Esegui training) per eseguire il training dell'app con le nuove espressioni.

![Eseguire il training dell'app, quindi rivedere le espressioni di esempio.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

A questo punto, l'ordine ha alcuni dettagli che possono essere estratti (dimensione, quantità e testo dell'ordine totale). È stata definita ulteriormente l'entità `Order`, ad esempio con le guarniture della pizza, il tipo di impasto e contorni. Ognuno di questi dettagli deve essere creato come sottocomponente dell'entità `Order`. 

## <a name="test-the-app-to-validate-the-changes"></a>Testare l'app per convalidare le modifiche

Testare l'app usando il pannello interattivo **Test**. Questo processo consente di immettere una nuova espressione e quindi di visualizzare i risultati della stima per verificare il funzionamento dell'app attiva e sottoposta a training. La stima della finalità dovrebbe essere abbastanza attendibile (oltre il 70%) e l'estrazione delle entità deve estrarre almeno l'entità `Order`. I dettagli dell'entità ordine potrebbero essere mancanti poiché 5 espressioni non sono sufficienti per gestire tutti i casi.

1. Selezionare **Test** nella barra di spostamento superiore.
1. Immettere l'espressione `deliver a medium veggie pizza` e premere INVIO. Il modello attivo ha stimato la finalità corretta con un'attendibilità superiore al 70%. 

    ![Immettere una nuova espressione per testare la finalità.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selezionare **Inspect** (Ispeziona) per visualizzare le stime delle entità.

    ![Visualizzare le stime delle entità nel pannello del test interattivo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    La dimensione è stata identificata correttamente. Tenere presente che le espressioni di esempio nella finalità `OrderPizza` non hanno un esempio di `medium` come dimensione ma usano un descrittore di un elenco frasi `SizeDescriptor` che include il supporto.

    La quantità non è stimata correttamente. Per risolvere questo problema, è possibile aggiungere più espressioni di esempio usando la parola per indicare la quantità e assegnare un'etichetta alla parola come entità `Quantity`. 

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Pubblicare l'app per eseguire l'accesso dall'endpoint HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction--from-http-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nell'indirizzo e immettere la stessa query immessa nel pannello di test interattivo. 

    `deliver a medium veggie pizza`

    L'ultimo parametro querystring è `query`, la **query** dell'espressione. 

    ```json
    {
        "query": "deliver a medium veggie pizza",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }    
    ```
    

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Esercitazione - Finalità](luis-quickstart-intents-only.md)
* Informazioni concettuali su [Concetto - entità](luis-concept-entity-types.md)
* Informazioni concettuali su [Concetto - funzionalità](luis-concept-feature.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione l'app usa un'entità basata su Machine Learning per trovare la finalità dell'espressione di un utente ed estrarre i dettagli dall'espressione. L'uso dell'entità basata su Machine Learning consente di scomporre i dettagli dell'entità.  

> [!div class="nextstepaction"]
> [Aggiungere un'entità KeyPhrase predefinita](luis-quickstart-intent-and-key-phrase.md)
