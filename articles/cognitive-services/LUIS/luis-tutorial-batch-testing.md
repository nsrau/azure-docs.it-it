---
title: Usare un test in batch per migliorare le previsioni LUIS | Microsoft Docs
titleSuffix: Azure
description: Caricare un test in batch, esaminare i risultati e migliorare le previsioni LUIS tramite modifiche.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266397"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>Usare un test in batch per individuare i problemi di accuratezza delle previsioni

Questa esercitazione illustra come usare un test in batch per individuare i problemi di previsione delle espressioni.  

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
* Creare un file di test in batch 
* Eseguire un test in batch
* Esaminare i risultati del test
* Correggere gli errori relativi alle finalità
* Eseguire nuovamente il test in batch

## <a name="prerequisites"></a>prerequisiti

> [!div class="checklist"]
> * Per questo articolo è anche necessario un account [LUIS][LUIS] per creare un'applicazione LUIS personalizzata.

> [!Tip]
> Se non si ha già una sottoscrizione, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/).

## <a name="create-new-app"></a>Creare una nuova app
Questo articolo usa il dominio predefinito HomeAutomation. Il dominio predefinito include le finalità, le entità e le espressioni per il controllo dei dispositivi HomeAutomation, come le luci. Creare l'app, aggiungere il dominio, eseguire il training e quindi pubblicarla.

1. Nel sito Web [LUIS] creare una nuova app selezionando **Create new app** (Crea nuova app) nella pagina **MyApps** (App personali). 

    ![Creare una nuova app](./media/luis-tutorial-batch-testing/create-app-1.png)

2. Immettere il nome `Batchtest-HomeAutomation` nella finestra di dialogo.

    ![Immettere il nome dell'app](./media/luis-tutorial-batch-testing/create-app-2.png)

3. Selezionare **Prebuilt Domains** (Domini predefiniti) nell'angolo in basso a sinistra. 

    ![Selezionare Prebuilt Domains (Domini predefiniti)](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. Selezionare **Add domain** (Aggiungi dominio) per HomeAutomation.

    ![Aggiungere il dominio HomeAutomation](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. Selezionare **Train** (Esegui il training) nella barra di spostamento in alto a destra.

    ![Selezionare il pulsante Train (Esegui il training)](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>Criteri del test in batch
È possibile eseguire il test in batch su massimo 1000 espressioni alla volta. Il batch non deve contenere duplicati. [Esportare](create-new-app.md#export-app) l'app per visualizzare l'elenco delle espressioni correnti.  

La strategia di test per LUIS usa tre set di dati separati: espressioni di modello, espressioni del test in batch ed espressioni di endpoint. Per questa esercitazione, assicurarsi di non usare le espressioni di modello (aggiunte a una finalità) o le espressioni di endpoint. 

Non usare un'espressione già presente nell'app per il test in batch:

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>Creare un batch per testare l'accuratezza della previsione della finalità
1. Creare `homeauto-batch-1.json` in un editor di testo come [VSCode](https://code.visualstudio.com/). 

2. Aggiungere le espressioni con la **finalità** che si desidera sia prevista nel test. In questa esercitazione, per semplicità, prendere le espressioni in `HomeAutomation.TurnOn` e `HomeAutomation.TurnOff` e modificare il testo `on` e `off` alternandolo nelle espressioni. Per la finalità `None` aggiungere un paio di espressioni che non fanno parte dell'area del [dominio](luis-glossary.md#domain) (oggetto). 

    Per comprendere in che modo i risultati del test in batch sono correlati al JSON del batch, aggiungere solo sei finalità.

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Eseguire il batch
1. Selezionare **Test** nella barra di spostamento in alto. 

    ![Selezionare Test nella barra di spostamento](./media/luis-tutorial-batch-testing/test-1.png)

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra. 

    ![Selezionare Batch testing panel (Pannello test in batch)](./media/luis-tutorial-batch-testing/test-2.png)

3. Selezionare **Import dataset** (Importa set di dati).

    ![Selezionare Import dataset (Importa set di dati)](./media/luis-tutorial-batch-testing/test-3.png)

4. Scegliere il percorso del file system del file `homeauto-batch-1.json`.

5. Denominare il set di dati `set 1`.

    ![Selezionare il file](./media/luis-tutorial-batch-testing/test-4.png)

6. Selezionare il pulsante **Run** (Esegui). Attendere fino a quando il test non viene completato.

    ![Selezionare Run (Esegui)](./media/luis-tutorial-batch-testing/test-5.png)

7. Selezionare **See results** (Visualizza risultati).

    ![See results (Visualizza risultati)](./media/luis-tutorial-batch-testing/test-6.png)

8. Esaminare i risultati nel grafico e nella legenda.

    ![Risultati del batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>Esaminare i risultati del batch
I risultati del batch sono suddivisi in due sezioni. La sezione superiore contiene il grafico e la legenda. La sezione inferiore mostra le espressioni quando si seleziona il nome di un'area del grafico.

Gli eventuali errori sono indicati con il colore rosso. Il grafico contiene quattro sezioni, di cui due visualizzate in rosso. **Queste sono le sezioni a cui prestare attenzione**. 

La sezione in alto a destra indica che il test ha erroneamente previsto l'esistenza di una finalità o di un'entità. La sezione in alto a sinistra indica che il test ha erroneamente previsto l'assenza di una finalità o di un'entità.

### <a name="homeautomationturnoff-test-results"></a>Risultati del test di HomeAutomation.TurnOff
Nella legenda selezionare la finalità `HomeAutomation.TurnOff`. A sinistra del nome nella legenda è presente un'icona verde di esito positivo. Non ci sono errori per questa finalità. 

![Risultati del batch](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>Le finalità HomeAutomation.TurnOn e None presentano errori
Le altre due finalità presentano errori, ovvero le previsioni del test non corrispondono alle aspettative del file batch. Selezionare la finalità `None` nella legenda per esaminare il primo errore. 

![Finalità None](./media/luis-tutorial-batch-testing/none-intent-failures.png)

Gli errori vengono visualizzati nelle sezioni rosse del grafico **False Positive** (Falso positivo) e **False Negative** (Falso negativo). Selezionare il nome della sezione **False Negative** (Falso negativo) del grafico per visualizzare l'espressione con esito negativo sotto il grafico. 

![Errori falsi negativi](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

L'espressione in errore `help` era prevista come finalità `None`, ma il test ha previsto la finalità `HomeAutomation.TurnOn`.  

Ci sono due errori: uno in HomeAutomation.TurnOn e uno in None. Entrambi sono causati dall'espressione `help`, perché non ha soddisfatto l'aspettativa in None e ha generato una corrispondenza imprevista per la finalità HomeAutomation.TurnOn. 

Per determinare i motivi per cui le espressioni `None` hanno esito negativo, esaminare le espressioni attualmente in `None`. 

## <a name="review-none-intents-utterances"></a>Esaminare le espressioni della finalità None

1. Chiudere il pannello **Test** selezionando il pulsante **Test** nella barra di spostamento in alto. 

2. Selezionare **Build** (Compila) nel pannello di navigazione in alto. 

3. Selezionare **None** nell'elenco delle finalità.

4. Selezionare CTRL+E per passare alla visualizzazione token delle espressioni 
    
    |Espressioni della finalità None|Punteggio di previsione|
    |--|--|
    |"decrease temperature for me please"|0.44|
    |"dim kitchen lights to 25."|0.43|
    |"lower your volume"|0,46|
    |"turn on the internet in my bedroom please"|0.28|

## <a name="fix-none-intents-utterances"></a>Correggere le espressioni della finalità None
    
Si suppone che ogni espressione in `None` si trovi all'esterno del dominio dell'app. Poiché queste espressioni sono correlate a HomeAutomation, si trovano nella finalità errata. 

LUIS assegna alle espressioni anche un punteggio di previsione inferiore al 50% (<0,50). Se si osservano le espressioni nelle altre due finalità, si noterà che i punteggi di previsione sono molto più elevati. Quando LUIS presenta punteggi bassi per le espressioni di esempio, vuol dire che le espressioni generano confusione per LUIS tra la finalità corrente e le altre finalità. 

Per correggere l'app, è necessario spostare le espressioni attualmente nella finalità `None` nella finalità corretta e aggiungere nuove finalità appropriate alla finalità `None`. 

Tre espressioni nella finalità `None` sono dirette a ridurre le impostazioni dei dispositivi di automazione e usano parole come `dim`, `lower` o `decrease`. La quarta espressione chiede di attivare Internet. Poiché tutti le quattro espressioni riguardano l'attivazione o la modifica del livello di potenza di un dispositivo, dovrebbero essere spostate nella finalità `HomeAutomation.TurnOn`. 

Questa è solo una delle soluzioni. Si potrebbe anche creare una nuova finalità `ChangeSetting` e spostare le espressioni utilizzando dim, lower e decrease in questa nuova finalità. 

## <a name="fix-the-app-based-on-batch-results"></a>Correggere l'app in base ai risultati del batch
Spostare le quattro espressioni nella finalità `HomeAutomation.TurnOn`. 

1. Selezionare la casella di controllo sopra l'elenco delle espressioni in modo da selezionare tutte le espressioni. 

2. Nell'elenco a discesa **Reassign intent** (Riassegna finalità) selezionare `HomeAutomation.TurnOn`. 

    ![Spostare le espressioni](./media/luis-tutorial-batch-testing/move-utterances.png)

    Dopo aver riassegnato le quattro espressioni, l'elenco delle espressioni per la finalità `None` è vuoto.

3. Aggiungere quattro nuove finalità per la finalità None:

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    Queste espressioni sono sicuramente all'esterno del dominio di HomeAutomation. Quando si immette ciascuna espressione, controllarne il punteggio. Il punteggio potrebbe essere basso o anche molto basso (circondato da un riquadro rosso). Dopo aver eseguito il training dell'app, nel passaggio 8, il punteggio sarà molto più elevato. 

7. Rimuovere eventuali etichette selezionando l'etichetta blu nell'espressione, quindi fare clic su **Remove label** (Rimuovi etichetta).

8. Selezionare **Train** (Esegui il training) nella barra di spostamento in alto a destra. Il punteggio di ogni espressione è molto più elevato. Tutti i punteggi per la finalità `None` dovrebbero essere ora superiori a 0,80. 

## <a name="verify-the-fix-worked"></a>Verificare il funzionamento delle correzioni
Per verificare che le espressioni del test in batch siano previste correttamente per la finalità **None**, eseguire nuovamente il test in batch.

1. Selezionare **Test** nella barra di spostamento in alto. 

2. Selezionare **Batch testing panel** (Pannello test in batch) nel pannello di destra. 

3. Selezionare i tre punti di sospensione (...) a destra del nome del batch e scegliere **Run Dataset** (Esegui set di dati). Attendere fino a quando il test in batch non viene completato.

    ![Run dataset (Esegui set di dati)](./media/luis-tutorial-batch-testing/run-dataset.png)

4. Selezionare **See results** (Visualizza risultati). Per tutte le finalità, le icone a sinistra del nome devono essere verdi. Con il filtro a destra impostato sulla finalità `HomeAutomation.Turnoff`, selezionare il punto verde nel pannello superiore destra più vicino al centro del grafico. Il nome dell'espressione viene visualizzato nella tabella sotto il grafico. Il punteggio di `breezeway off please` è molto basso. Come attività facoltativa, è possibile aggiungere più espressioni alla finalità per aumentare questo punteggio. 

    ![Run dataset (Esegui set di dati)](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle espressioni di esempio](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions