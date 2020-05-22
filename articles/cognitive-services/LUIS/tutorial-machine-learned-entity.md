---
title: "Esercitazione: Estrarre dati strutturati con un'entità basata su Machine Learning - LUIS"
description: Estrarre dati strutturati da un'espressione usando l'entità basata su Machine Learning. Per aumentare l'accuratezza dell'estrazione, aggiungere sottoentità con funzionalità.
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: d1bc8fc6aac52e264cb4352ca05f9df45ccfc50e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588871"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>Esercitazione: Estrarre dati strutturati da un'espressione dell'utente con entità basate su Machine Learning in Language Understanding (LUIS)

In questa esercitazione vengono estratti dati strutturati da un'espressione usando l'entità basata su Machine Learning.

L'entità basata su Machine Learning supporta il [concetto di scomposizione del modello](luis-concept-model.md#v3-authoring-model-decomposition) fornendo entità di sottoentità [funzionalità](luis-concept-feature.md).

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Importare l'app di esempio
> * Aggiungere un'entità basata su Machine Learning
> * Aggiungere sottoentità e funzionalità
> * Eseguire il training, il test e la pubblicazione dell'app
> * Ottenere la stima dell'entità dall'endpoint

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>Perché usare un'entità basata su Machine Learning?

Questa esercitazione aggiunge un'entità basata su Machine Learning per estrarre dati da un'espressione dell'utente.

L'entità definisce i dati da estrarre dall'espressione. Ciò include la specifica di un nome e di un tipo (se possibile) per i dati, di una risoluzione dei dati in caso di ambiguità e del testo esatto che compone i dati.

Per definire i dati, è necessario:
* Creare l'entità
* Etichettare il testo che rappresenta l'entità all'interno di espressioni di esempio. Questi esempi con etichetta insegnano a LUIS qual è l'entità e dove può trovarsi in un'espressione.

## <a name="entity-decomposability-is-important"></a>La scomponibilità delle entità è importante

La scomponibilità delle entità è importante sia per la previsione delle finalità che per l'estrazione dei dati con l'entità.

Iniziare con un'entità basata su Machine Learning, che rappresenta l'entità iniziale e di primo livello per l'estrazione dei dati. Scomporre quindi l'entità in sottoentità.

Anche se non è possibile sapere quanto debba essere dettagliata l'entità quando si inizia a creare l'app, è consigliabile iniziare con un'entità basata su Machine Learning e quindi scomporla con sottoentità man mano che l'app cresce.

In questa esercitazione si creerà un'entità basata su Machine Learning per rappresentare un ordine per un'app per la pizza. L'entità estrarrà il testo correlato all'ordine, insieme a quantità e dimensione.

In un'espressione `Please deliver one large cheese pizza to me` si dovrebbero estrarre `one large cheese pizza` come ordine e quindi `1` come quantità e `large` come dimensione.

## <a name="download-json-file-for-app"></a>Scaricare il file JSON per l'app

Scaricare e salvare il [file JSON dell'app](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>Importare il file JSON per l'app

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

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

1. Nella casella **Choose an entity type** (Scegli un tipo di entità) selezionare **Add Structure** (Aggiungi struttura), quindi selezionare **Next** (Avanti). La struttura è necessaria per aggiungere le sottoentità, ad esempio dimensione e quantità.

    ![Aggiungere la struttura all'entità](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Nella casella **Create a machine learned entity** (Crea entità basata su Machine Learning) nella casella **Structure** (Struttura) aggiungere `Size` quindi premere INVIO.
1. Per aggiungere una **funzionalità**, selezionare `+` nell'area **Features** (Funzionalità) e quindi selezionare **Create new phrase list** (Crea nuovo elenco frasi).

1. Nella casella **Create new phrase list** (Crea nuovo elenco frasi) immettere il nome `SizeFeature` e quindi i valori `small`, `medium` e `large`. Quando viene compilata la casella **Suggestions** (Suggerimenti), selezionare `extra large` e `xl`. Selezionare **Done** (Fine) per creare il nuovo elenco frasi.

    Questa funzionalità dell'elenco frasi consente alla sottoentità `Size` di individuare le parole correlate alla dimensione fornendo parole di esempio. Questo elenco non deve includere tutte le parole dimensione ma deve includere parole che dovrebbero indicare le dimensioni.

    ![Creare una funzionalità per la sottoentità size](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. Nella finestra **Create a machine learned entity** (Crea entità basata su Machine Learning) selezionare **Create** (Crea) per completare la creazione della sottoentità `Size`.

    Viene creata l'entità `Order` con un'entità `Size`, ma all'espressione viene applicata solo l'entità `Order`. È necessario assegnare un'etichetta al testo dell'entità `Size` nell'espressione di esempio.

1. Nella stessa espressione di esempio assegnare un'etichetta alla sottoentità **Size** (Dimensione) di `large` selezionando la parola e quindi selezionando l'entità **Size** (Dimensione) dall'elenco a discesa.

    ![Assegnare un'etichetta all'entità della dimensione per il testo dell'espressione.](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    La linea sotto il testo è continua poiché l'etichetta e la previsione corrispondono, in quanto il testo è stato etichettato _esplicitamente_.

1. Assegnare un'etichetta all'entità `Order` nelle espressioni rimanenti insieme all'entità della dimensione. Le parentesi quadre nel testo indicano l'entità `Order` con etichetta e l'entità `Size` all'interno.

    |Espressioni di esempio dell'ordine|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![Creare entità e sottoentità in tutte le espressioni di esempio rimanenti.](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > Come trattare i dati impliciti, ad esempio la lettera `a` che indica una sola pizza? Oppure l'assenza di `pickup` e `delivery` per indicare la posizione in cui è prevista la pizza? Oppure l'assenza di una dimensione per indicare la dimensione predefinita piccola o grande? Considerare la gestione del trattamento dei dati impliciti come parte delle regole business nell'applicazione client al posto di LUIS o in aggiunta a LUIS.

1. Per eseguire il training dell'app, selezionare **Train** (Esegui il training). Il training applica le modifiche, ad esempio le nuove entità e le espressioni con etichetta, al modello attivo.

1. Dopo aver eseguito il training, aggiungere una nuova espressione di esempio alla finalità per vedere in che modo LUIS riconosce l'entità basata su Machine Learning.

    |Espressione di esempio dell'ordine|
    |--|
    |`pickup XL meat lovers pizza`|

    L'entità di primo livello `Order` ha un'etichetta e anche la sottoentità `Size` ha un'etichetta con linee tratteggiate.

    ![Nuova espressione di esempio stimata con l'entità](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    La linea tratteggiata indica la stima.

1. Per modificare la stima in un'entità etichettata, selezionare la riga e quindi selezionare **Confirm entity predictions** (Conferma stime entità).

    ![Accettare la stima selezionando Confirm entity prediction (Conferma stima entità).](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    A questo punto, l'entità basata su Machine Learning funziona poiché è in grado di trovare l'entità all'interno di una nuova espressione di esempio. Quando si aggiungono espressioni di esempio, se l'entità non è stimata correttamente, assegnare un'etichetta all'entità e ai sottoentità. Se l'entità è stimata correttamente, assicurarsi di confermare le stime.


<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="add-subentity-with-feature-of-prebuilt-entity"></a>Aggiungi sottoentità con funzionalità di entità predefinita

Le informazioni sull'ordine devono includere anche il numero di unità di un elemento nell'ordine, ad esempio il numero di pizze. Per estrarre questi dati, è necessario aggiungere una nuova sottoentità basata su Machine Learning a `Order` e specificare che tale sottoentità richiede una funzionalità obbligatoria di un numero predefinito. Usando una funzionalità di un numero predefinito, l'entità trova ed estrae numeri quando il testo è una cifra, `2` oppure un testo, `two`.

## <a name="add-prebuilt-number-entity-to-app"></a>Aggiungere un'entità numero predefinita all'app
Le informazioni sull'ordine devono includere anche il numero di articoli inclusi nell'ordine, ad esempio il numero di pizze. Per estrarre questi dati, è necessario aggiungere un nuovo sottocomponente basato su Machine Learning a `Order` e specificare che tale componente richiede una funzionalità obbligatoria di un numero predefinito. Vincolando l'entità a un numero predefinito, l'entità trova ed estrae numeri quando il testo è una cifra, `2` oppure un testo, `two`.

Iniziare aggiungendo l'entità numero predefinito all'app.

1. Selezionare **Entities** (Entità) dal menu di sinistra, quindi selezionare **+ Add prebuilt entity** (+ Aggiungi entità predefinita).

1. Nella casella **Add prebuilt entities** (Aggiungi entità predefinite) cercare e selezionare **number** (numero) quindi selezionare **Done** (Fine).

    ![Aggiungere entità predefinite](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    L'entità predefinita viene aggiunta all'app, ma non è ancora una funzionalità.

## <a name="create-subentity-entity-with-required-feature-to-help-extract-data"></a>Creare un'entità di sottoentità con la funzionalità obbligatoria per facilitare l'estrazione dei dati

L'entità `Order` deve include una sottoentità `Quantity` per determinare il numero di articoli inclusi nell'ordine. La quantità deve usare una funzionalità obbligatoria di un numero predefinito in modo che i dati estratti siano immediatamente utilizzabili dall'applicazione client in base al nome.

Una funzionalità obbligatoria viene applicata come corrispondenza di testo, con corrispondenza esatta (ad esempio un'entità dell'elenco) o tramite espressioni regolari (ad esempio un'entità espressione regolare o un'entità predefinita).

Usando come funzionalità un'entità non basata su Machine Learning, viene estratto solo il testo corrispondente.

1. Selezionare **Entities** (Entità) quindi selezionare l'entità `Order`.
1. Selezionare **+ Add entity** (+ Aggiungi entità), immettere il nome `Quantity` e quindi premere INVIO per aggiungere la nuova sottoentità all'entità `Order`.
1. Dopo la notifica di esito positivo, selezionare la matita del vincolo in **Advanced Options** (Opzioni avanzate).
1. Nell’elenco a discesa selezionare il numero predefinito.

    ![Creare un'entità quantità con un numero predefinito come vincolo.](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    L'entità `Quantity` viene applicata quando il testo corrisponde all'entità con il numero predefinito.

    L'entità con la funzionalità obbligatoria è stata creata ma non ancora applicata alle espressioni di esempio.

    > [!NOTE]
    > Una sottoentità può supportare fino a cinque livelli di annidamento all'interno di una sottoentità. Sebbene non sia illustrato in questo articolo, è disponibile dal portale e dall'API.

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>Etichettare un'espressione di esempio per inviare a LUIS dati sull'entità

1. Selezionare **Intents** (Finalità) nel riquadro di spostamento a sinistra, quindi selezionare la finalità **OrderPizza**. I tre numeri nelle espressioni seguenti hanno un'etichetta ma sono visualizzati sotto la riga dell'entità `Order`. Questo livello inferiore indica che le entità vengono rilevate, ma non sono considerate una parte dall'entità `Order`.

    ![Il numero predefinito è stato trovato ma non è ancora considerato separato dall'entità Order (Ordine).](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. Assegnare un'etichetta ai numeri con l'entità `Quantity` selezionando `2` nell'espressione di esempio, quindi selezionando `Quantity` dall'elenco. Assegnare un'etichetta a `6` e `1` nella stessa espressione di esempio.

    ![Assegnare un'etichetta al testo con l'entità quantità.](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Eseguire il training dell'app per applicare le modifiche alle entità all'app

Selezionare **Train** (Esegui training) per eseguire il training dell'app con le nuove espressioni. Dopo il training, la sottoentità `Quantity` viene stimata correttamente nell'entità `Order`. Questa previsione corretta è indicata con una linea continua.

![Eseguire il training dell'app, quindi rivedere le espressioni di esempio.](media/tutorial-machine-learned-entity/trained-example-utterances.png)

A questo punto, l'ordine ha alcuni dettagli che possono essere estratti (dimensione, quantità e testo dell'ordine totale). È stata definita ulteriormente l'entità `Order`, ad esempio con le guarniture della pizza, il tipo di impasto e contorni. Ognuno di questi dettagli deve essere creato come sottoentità dell'entità `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Testare l'app per convalidare le modifiche

Testare l'app usando il pannello interattivo **Test**. Questo processo consente di immettere una nuova espressione e quindi di visualizzare i risultati della stima per verificare il funzionamento dell'app attiva e sottoposta a training. La stima della finalità dovrebbe essere abbastanza attendibile (oltre il 70%) e l'estrazione delle entità deve estrarre almeno l'entità `Order`. I dettagli dell'entità ordine potrebbero essere mancanti poiché 5 espressioni non sono sufficienti per gestire tutti i casi.

1. Selezionare **Test** nella barra di spostamento superiore.
1. Immettere l'espressione `deliver a medium veggie pizza` e premere INVIO. Il modello attivo ha stimato la finalità corretta con un'attendibilità superiore al 70%.

    ![Immettere una nuova espressione per testare la finalità.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. Selezionare **Inspect** (Ispeziona) per visualizzare le stime delle entità.

    ![Visualizzare le stime delle entità nel pannello del test interattivo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    La dimensione è stata identificata correttamente. Tenere presente che le espressioni di esempio nella finalità `OrderPizza` non includono un esempio di `medium` come dimensione ma usano una funzionalità di un elenco di frasi `SizeFeature` che include medium.

    La quantità non è stimata correttamente. È possibile risolvere questo problema nell'applicazione client specificando il valore di dimensione predefinito uno (1) se nella previsione di LUIS non viene restituita alcuna dimensione.

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Pubblicare l'app per eseguire l'accesso dall'endpoint HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Ottenere previsione per finalità ed entità dall'endpoint HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e sostituire _YOUR_QUERY_HERE_ con la stessa query immessa nel pannello di test interattivo.

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
