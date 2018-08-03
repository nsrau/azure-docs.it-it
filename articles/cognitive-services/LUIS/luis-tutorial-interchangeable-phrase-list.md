---
title: Esercitazione con uso di un elenco di frasi per migliorare le stime di LUIS - Azure | Microsoft Docs
description: In questa esercitazione, si aggiungerà un elenco di frasi a un'app LUIS e si osserverà il miglioramento del punteggio.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: 2fd53225a455a34d03772487a10f62a94ac86735
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868974"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Esercitazione: aggiungere un elenco di frasi per migliorare le stime
In questa esercitazione si migliorerà l'accuratezza dei punteggi delle finalità e si individueranno le entità per le parole che hanno lo stesso significato (sinonimi) aggiungendo una [funzionalità di elenco di frasi](./luis-concept-feature.md) intercambiabili.

> [!div class="checklist"]
* Importare una nuova app  
* Eseguire query sull'endpoint con un'espressione nota 
* Eseguire query sull'endpoint con un'espressione _sconosciuta_
* Aggiungere un elenco di frasi per migliorare il punteggio di espressioni sconosciute
* Verificare l'entità trovata quando si utilizza l'elenco di frasi

Per questo articolo è necessario un account gratuito di [LUIS][LUIS] per creare la propria applicazione.

## <a name="import-a-new-app"></a>Importare una nuova app
1. Scaricare l'[app LUIS di esempio][LuisSampleApp] progettata per questa esercitazione. che sarà necessario nel passaggio successivo. 

2. Come descritto in [Creare un'app](Create-new-app.md#import-new-app), importare il file scaricato nel sito Web [LUIS][LUIS] come nuova app. Il nome dell'app è "Mia esercitazione elenco di frasi". Ha finalità, entità ed espressioni. 

3. [Eseguire il training](luis-how-to-train.md) dell'app. Finché non viene eseguito il training, non è possibile [testare in modo interattivo](interactive-test.md#interactive-testing) l'app nel sito Web [LUIS][LUIS]. 

4. Nella pagina [Publish](luis-how-to-publish-app.md) (Pubblica) selezionare la casella di controllo **Include all predicted intent scores** (Includi tutti i punteggi di finalità stimati). Se la casella di controllo è selezionata, vengono restituite tutte le finalità. Se la casella di controllo è deselezionata, viene restituita solo la finalità in cima all'elenco. 

5. [Pubblicare](luis-how-to-publish-app.md) l'app. La pubblicazione dell'app consente di testarla utilizzando l'endpoint HTTPS. 

## <a name="test-a-trained-utterance"></a>Testare un'espressione con training
Utilizzare l'endpoint pubblicato per eseguire una query di un'espressione già nota all'app. Poiché LUIS conosce già l'espressione, il punteggio è elevato e l'entità viene rilevata.

1. Nel sito Web [Language Understanding (LUIS)][LUIS], selezionare l'URL dell'endpoint nella sezione **Resources and Keys** (Risorse e chiavi) della pagina **Publish** (Pubblica) per la nuova app. 

    ![Pubblicare l'URL dell'endpoint](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. Nel browser, alla fine dell'URL, aggiungere la query seguente dopo `q=`.

    `I want a computer replacement`

    L'endpoint risponde con il seguente JSON:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    Il punteggio di finalità di 0,973 e il punteggio di rilevamento entità di 0,846 è elevato poiché è stato eseguito il training dell'app con questa espressione. L'espressione è nell'app LUIS nella pagina della finalità per **GetHardware**. Il testo dell'espressione, `computer`, è etichettato come entità **Hardware**. 
    
    |Status|Word| Punteggio di finalità | Punteggio di entità |
    |--|--|--|--|
    |Con training| want | 0,973 | 0,846 |
    
    
## <a name="test-an-untrained-utterance"></a>Testare un'espressione senza training
Nel browser utilizzare lo stesso endpoint pubblicato per eseguire la query con un'espressione che l'app non conosce:

`I require a computer replacement`

Questa espressione utilizza un sinonimo dell'espressione precedente:

| Parola con training | Sinonimo senza training |
|--|--|
| want | require |

La risposta dell'endpoint è:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Status | Word | Punteggio di finalità | Punteggio di entità |
|--|--|--|--|
| Con training| want | 0,973 | 0,846 |
| Senza training| require | 0,840 | - |

Il punteggio di finalità dell'espressione senza training è inferiore a quello dell'espressione con etichetta perché LUIS sa che la frase grammaticamente è uguale. LUIS tuttavia non sa che le espressioni hanno lo stesso significato. Inoltre, senza l'elenco di frasi l'entità **Hardware** non viene trovata.

È necessario insegnare a LUIS che *want* e *require* hanno lo stesso significato nel dominio dell'app perché una parola può avere più significati. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Migliorare il punteggio dell'espressione senza training con un elenco di frasi 
1. Aggiungere una funzionalità di [elenco di frasi](luis-how-to-add-features.md) denominata **want** con valore `want`, quindi selezionare **Invio**.

    > [!TIP]
    > Dopo ogni parola o frase, selezionare il tasto **Invio**. La parola o la frase viene aggiunta alla casella **Phrase list values** (Valori elenco frasi) mentre il cursore è nella casella **Value** (Valore). È possibile immettere molti valori rapidamente grazie a questa funzionalità.

2. Per visualizzare le parole consigliate da LUIS, selezionare **Recommend** (Consiglia). 

    ![Valori consigliati](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Aggiungere tutte le parole. Se `require` non è presente nell'elenco dei valori consigliati, aggiungerlo come valore obbligatorio. 

4. Poiché queste parole sono sinonimi, mantenere l'impostazione *interchangeable* (intercambiabile) e selezionare **Save** (Salva).

    ![Valori dell'elenco di frasi](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. Nella barra di spostamento superiore selezionare **Train** (Esegui training) per eseguire il training dell'app, senza pubblicarla. Ora sono disponibili due modelli. È possibile confrontare i valori nei due modelli.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Confrontare il modello dell'elenco di frasi al modello pubblicato
In questa app non è stato eseguito il training del modello pubblicato con i sinonimi. Solo il modello attualmente modificato include l'elenco di frasi dei sinonimi. Per confrontare i modelli, utilizzare i [test interattivi](interactive-test.md#interactive-testing). 

1. Aprire il riquadro **Test** e immettere l'espressione seguente:

    `I require a computer replacement`

2. Per aprire il pannello di ispezione, selezionare **Inspect** (Ispeziona). 

    ![Selezionare l'ispezione](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Per confrontare il modello pubblicato al nuovo modello dell'elenco di frasi, selezionare **Compare with published** (Confronta con pubblicato).

    ![Ispezionare il modello pubblicato e quello corrente](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

Dopo aver aggiunto l'elenco di frasi, l'accuratezza dell'espressione aumenta e viene trovata l'entità **Hardware**. 

|Status | Elenco di frasi| Punteggio di finalità | Punteggio di entità |
|--|--|--|--|
| Pubblicato | - | 0,84 | - |
| Attualmente in corso di modifica |✔| 0,92 | Entità hardware identificata |

> [!TIP]
> * Utilizzando i [test interattivi](interactive-test.md#interactive-testing), è possibile confrontare il modello pubblicato alle modifiche con training apportate dopo la pubblicazione. 
> * Utilizzando i [test di endpoint](luis-how-to-publish-app.md#test-your-published-endpoint-in-a-browser), è possibile visualizzare il valore JSON esatto della risposta di LUIS. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Ottenere il punteggio di entità con il test di endpoint
Per visualizzare il punteggio di entità, [pubblicare il modello](luis-how-to-publish-app.md) ed eseguire una query dell'endpoint. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

L'entità **Hardware** mostra un punteggio pari a 0,595 con l'elenco di frasi. Prima dell'esistenza dell'elenco di frasi, l'entità non era stata rilevata. 

|Status | Elenco di frasi| Punteggio di finalità | Punteggio di entità |
|--|--|--|--|
| Pubblicato | - | 0,84 | - |
| Attualmente in corso di modifica |✔| 0,92 | 0.595 |


## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS. A tale scopo, selezionare i puntini di sospensione (***...***) a destra del nome dell'app nell'elenco di app e quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Ottenere la stima dell'espressione con la query di endpoint](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
