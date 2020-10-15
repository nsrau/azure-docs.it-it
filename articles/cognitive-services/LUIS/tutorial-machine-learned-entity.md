---
title: "Esercitazione: Estrarre dati strutturati con un'entità basata su Machine Learning - LUIS"
description: Estrarre dati strutturati da un'espressione usando l'entità basata su Machine Learning. Per aumentare l'accuratezza dell'estrazione, aggiungere sottoentità con funzionalità.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: 01262ee0271849793c4393b1ea8e18c4179ad4e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334728"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Esercitazione: Estrarre dati strutturati da un'espressione dell'utente con entità basate su Machine Learning in Language Understanding (LUIS)

In questa esercitazione vengono estratti dati strutturati da un'espressione usando l'entità basata su Machine Learning.

L'entità basata su Machine Learning supporta il [concetto di scomposizione del modello](luis-concept-model.md#v3-authoring-model-decomposition) fornendo entità di sottoentità con [funzionalità](luis-concept-feature.md).

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Importare l'app di esempio
> * Aggiungere un'entità basata su Machine Learning
> * Aggiungere sottoentità e funzionalità
> * Eseguire il training, il test e la pubblicazione dell'app
> * Ottenere la stima dell'entità dall'endpoint

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Perché usare un'entità basata su Machine Learning?

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

## <a name="create-machine-learned-entity"></a>Creare un'entità basata su Machine Learning

Per estrarre i dettagli relativi a un ordine di pizza, creare un'entità `Order` basata su Machine Learning di primo livello.

1. Nella pagina **Intents** (Finalità) selezionare la finalità **OrderPizza**.

1. Nell'elenco delle espressioni di esempio selezionare l'espressione seguente.

    |Espressione di esempio dell'ordine|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Iniziare effettuando la selezione immediatamente prima del testo più a sinistra `pickup` (#1) quindi immediatamente dopo il testo più a destra `anchovies` (#2 - fine del processo di etichettatura). Viene visualizzato un menu a comparsa. Nella casella popup immettere il nome dell'entità `Order` (#3). Quindi selezionare `Order Create new entity` dall'elenco (#4).

    ![Assegnare un'etichetta all'inizio e alla fine del testo per l'ordine completo](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Un'entità non sarà sempre l'intera espressione. In questo caso specifico, `pickup` indica la modalità di ricezione dell'ordine. Dal punto di vista concettuale, `pickup` deve far parte dell'entità etichettata per l'ordine.

1. Nella casella **Choose an entity type** (Scegli un tipo di entità) selezionare **Add Structure** (Aggiungi struttura), quindi selezionare **Next** (Avanti). La struttura è necessaria per aggiungere le sottoentità, ad esempio dimensione e quantità.

    ![Screenshot che mostra la finestra Choose an entity type (Scegli un tipo di entità) con l'opzione Add structure (Aggiungi struttura) selezionata.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Nella casella **Add subentities (optional)** (Aggiungi sottoentità - facoltativo) selezionare **+** sulla riga `Order`, aggiungere `Size` e `Quantity` come sottoentità e quindi selezionare **Create** (Crea).

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra la finestra Add subentities (optional) (Aggiungi sottoentità - facoltativo) con sottoentità evidenziate.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Modificare le sottoentità per migliorare l'estrazione

I passaggi precedenti creano l'entità e la sottoentità. Per migliorare l'estrazione, aggiungere funzionalità alle sottoentità.

### <a name="improve-size-extraction-with-phrase-list"></a>Migliorare l'estrazione delle dimensioni con l'elenco di frasi

1. Selezionare **Entities** (Entità) nel menu a sinistra e quindi selezionare l'entità **Order** (Ordine).

1. Nella scheda **Schema and features** (Schema e funzionalità) selezionare la sottoentità **Size** (Dimensioni) e quindi selezionare **+ Add feature** (Aggiungi funzionalità).

1. Selezionare **Create new phrase list** (Crea nuovo elenco frasi) nel menu a discesa.

1. Nella casella **Create new phrase list** (Crea nuovo elenco frasi) immettere il nome `SizePhraselist` e quindi i valori `small`, `medium` e `large`. Quando viene compilata la casella **Suggestions** (Suggerimenti), selezionare `extra large` e `xl`. Selezionare **Crea** per creare il nuovo elenco di frasi.

    Questa funzionalità dell'elenco frasi consente alla sottoentità `Size` di individuare le parole correlate alla dimensione fornendo parole di esempio. Questo elenco di frasi non deve includere ogni parola dimensione ma deve includere parole che dovrebbero indicare le dimensioni.

### <a name="add-sizelist-entity"></a>Aggiungere l'entità SizeList

L'aggiunta di un elenco di dimensioni note riconosciute dall'applicazione client consente di semplificare l'estrazione.

1. Selezionare **Entities** (Entità) nel menu a sinistra e quindi selezionare **+ Create** (Crea).

1. Impostare il nome dell'entità su `SizeListentity` e il tipo su **List** in modo che sia facile da identificare rispetto all'entità `SizePhraselist` creata nella sezione precedente.

1. Aggiungere le dimensioni previste dall'applicazione client, ovvero `Small`, `Medium`, `Large` e `XLarge` e quindi aggiungere sinonimi per ciascuna. I sinonimi devono essere i termini che un utente immette nel chatbot. L'entità viene estratta con un'entità elenco quando corrisponde esattamente al valore normalizzato oppure ai sinonimi.

    |Valore normalizzato|Sinonimi|
    |--|--|
    |Piccolo|sm, sml, tiny, smallest|
    |Media|md, mdm, regular, average, middle|
    |large|lg, lrg, big|
    |XLarge|xl, biggest, giant|


    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra la finestra the SizeList e gli elementi dell'elenco con l'opzione XLarge selezionata.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Aggiungere la funzionalità dell'entità SizeList

1. Selezionare **Entities** (Entità) nel menu a sinistra per tornare all'elenco di entità.

1. Selezionare **Order** (Ordine) nell'elenco di entità.

1. Nella scheda **Schema and features** (Schema e funzionalità) selezionare l'entità **Size** (Dimensioni) e quindi selezionare **+ Add feature** (Aggiungi funzionalità).

1. Selezionare **@ SizeListentity** nell'elenco a discesa.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Aggiungere un'entità predefinita numero

L'aggiunta di un'entità predefinita numero consente di semplificare l'estrazione.

1. Selezionare **Entities** (Entità) nel menu a sinistra, quindi selezionare **+ Add prebuilt entity** (+ Aggiungi entità predefinita).

1. Selezionare **Number** (Numero) nell'elenco e quindi selezionare **Done** (Fine).

1. Selezionare **Entities** (Entità) nel menu a sinistra per tornare all'elenco di entità.

### <a name="add-feature-of-prebuilt-number-entity"></a>Aggiungere la funzionalità dell'entità numero predefinita

1. Selezionare **Order** (Ordine) nell'elenco di entità.

1. Nella scheda **Schema and features** (Schema e funzionalità) selezionare l'entità **Quantity** (Quantità) e quindi selezionare **+ Add feature** (Aggiungi funzionalità).

1. Selezionare **@ number** nell'elenco a discesa.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Configurare le funzionalità necessarie

Nella pagina dei dettagli per l'entità **Ordine** selezionare l'asterisco, `*`, per entrambe le funzionalità **@ SizeList** e **@ number**. L'asterisco viene visualizzato nella stessa etichetta del nome della funzionalità.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra la funzionalità @SizeList con l'asterisco e la richiesta di avviso.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Etichettare le espressioni di esempio

Viene creata l'entità basata su machine Learning e le sottoentità dispongono di funzionalità. Per completare il miglioramento dell'estrazione, le espressioni di esempio devono essere etichettate con le sottoentità.

1. Selezionare **Intents** (Finalità) nel riquadro di spostamento a sinistra, quindi selezionare la finalità **OrderPizza**.

1. Per aprire l'**Entity Palette** (Tavolozza entità) selezionare il simbolo **@** sulla barra degli strumenti contestuale.

1. Selezionare ogni riga di entità nella tavolozza, quindi usare il cursore della tavolozza per selezionare l'entità in ogni espressione di esempio. Al termine, l'elenco di entità avrà un aspetto simile all'immagine seguente.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parziale della configurazione della funzionalità richiesta](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Eseguire il training dell'app

Per eseguire il training dell'app, selezionare **Train** (Esegui il training). Il training applica le modifiche, ad esempio le nuove entità e le espressioni con etichetta, al modello attivo.

## <a name="add-a-new-example-utterance"></a>Aggiungere una nuova espressione di esempio

1. Dopo aver eseguito il training, aggiungere una nuova espressione di esempio alla finalità `OrderPizza` per comprendere in che modo LUIS riconosce l'entità basata su Machine Learning.

    |Espressione di esempio dell'ordine|
    |--|
    |`I need a large pepperoni pizza`|

    L'entità di primo livello `Order` ha un'etichetta e anche la sottoentità `Size` ha un'etichetta con linee tratteggiate.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parziale della nuova espressione di esempio stimata con l'entità](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    La linea tratteggiata indica la stima basata sull'app attualmente sottoposta a training.

1. Per modificare la stima in un'entità etichettata, selezionare il segno di spunta sulla stessa riga.

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra un'espressione di esempio con segno di spunta evidenziato.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    A questo punto, l'entità basata su Machine Learning funziona perché è in grado di trovare l'entità all'interno di una nuova espressione di esempio. Quando si aggiungono espressioni di esempio, se l'entità non è stimata correttamente, assegnare un'etichetta all'entità e ai sottoentità. Se l'entità è stimata correttamente, assicurarsi di confermare le stime.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Eseguire il training dell'app per applicare le modifiche alle entità all'app

Selezionare **Train** (Esegui training) per eseguire il training dell'app con questa nuova espressione.

A questo punto, l'ordine ha alcuni dettagli che possono essere estratti (dimensione, quantità e testo dell'ordine totale). È stata definita ulteriormente l'entità `Order`, ad esempio con le guarniture della pizza, il tipo di impasto e contorni. Ognuno di questi dettagli deve essere creato come sottoentità dell'entità `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Testare l'app per convalidare le modifiche

Testare l'app usando il pannello interattivo **Test**. Questo processo consente di immettere una nuova espressione e quindi di visualizzare i risultati della stima per verificare il funzionamento dell'app attiva e sottoposta a training. La stima della finalità dovrebbe essere abbastanza attendibile (oltre il 60%) e deve essere eseguita l'estrazione almeno dell'entità `Order`. I dettagli dell'entità ordine potrebbero essere mancanti poiché queste poche espressioni non sono sufficienti per gestire tutti i casi.

1. Selezionare **Test** nella barra di spostamento superiore.
1. Immettere l'espressione `2 small cheese pizzas for pickup` e premere INVIO. Il modello attivo ha stimato la finalità corretta con un'attendibilità superiore al 60%.


1. Selezionare **Inspect** (Ispeziona) per visualizzare le stime delle entità.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parziale della visualizzazione delle stime di entità nel pannello di test interattivo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Pubblicare l'app per eseguire l'accesso dall'endpoint HTTP

Per ottenere una previsione di Language Understanding Intelligent Service (LUIS) in un chatbot o in un'altra applicazione client, è necessario pubblicare l'app nell'endpoint.

1. Selezionare **Pubblica** nel riquadro di spostamento in alto a destra.

    ![Screenshot del pulsante per la pubblicazione nell'endpoint LUIS nel menu in alto a destra](./media/howto-publish/publish-button.png)

1. Selezionare lo slot **Production** (Produzione), selezionare **Change settings** (Modifica impostazioni), **Sentiment Analysis** (Analisi del sentiment) e quindi selezionare **Done** (Fine).

    > [!div class="mx-imgBorder"]
    > ![Screenshot del pulsante per la pubblicazione nell'endpoint LUIS](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Selezionare il collegamento **Access your endpoint URLs** (Accedere agli URL dell'endpoint) nella notifica per accedere alla pagina **Risorse di Azure**. Gli URL dell'endpoint sono indicati come **Example Query**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Ottenere previsione per finalità ed entità dall'endpoint HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e sostituire _YOUR_QUERY_HERE_ con la stessa query immessa nel pannello di test interattivo.

    `2 small cheese pizzas for pickup`

    L'ultimo parametro querystring è `query`, la **query** dell'espressione.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
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
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
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
