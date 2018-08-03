---
title: Esercitazione usando i criteri per migliorare le stime LUIS - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: In questa esercitazione usare i criteri per le finalità per migliorare le stime di finalità ed entità LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238455"
---
# <a name="tutorial-improve-app-with-patterns"></a>Esercitazione: migliorare le app con i criteri

In questa esercitazione usare i criteri per le finalità per migliorare le stime di finalità ed entità LUIS.  

> [!div class="checklist"]
* Come capire che un criterio potrebbe essere utile per l'app
* Come creare un criterio 
* Come verificare i miglioramenti di stima del criterio

Per questo articolo è necessario un account [LUIS](luis-reference-regions.md) gratuito per creare un'applicazione LUIS personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha l'app relativa alle risorse umane dell'esercitazione sul [test in batch](luis-tutorial-batch-testing.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json).

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `patterns`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>I criteri insegnano a LUIS espressioni comuni con un minor numero di esempi
Data la natura del dominio risorse umane, esistono alcuni modi comuni di porre domande sulle relazioni dei dipendenti all'interno delle organizzazioni. Ad esempio: 

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

Queste espressioni sono troppo simili per determinare l'univocità contestuale di ciascuna senza fornire molte espressioni di esempio. Aggiungendo un criterio per una finalità, LUIS apprende criteri di espressioni comuni per una finalità senza fornire molte espressioni di esempio. 

Le espressioni modello di esempio per questa finalità includono:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Il criterio viene fornito tramite un esempio di espressione modello, che include la sintassi per identificare le entità e il testo che può essere ignorato. Un criterio è una combinazione di corrispondenza a espressioni regolari e apprendimento automatico.  L'esempio di espressione modello, insieme alle espressioni della finalità, offrono a LUIS una migliore comprensione delle espressioni appropriate per la finalità.

Affinché un criterio possa essere messo in corrispondenza con un'espressione, le entità all'interno dell'espressione devono corrispondere per prima cosa alle entità nell'espressione modello. Tuttavia, il modello non consente di stimare le entità, solo le finalità. 

**Mentre i criteri consentono di fornire un numero inferiore espressioni di esempio, se le entità non vengono rilevate, il criterio non corrisponde.**

Tenere presente che i dipendenti siano stati creati nell'[esercitazione delle entità elenco](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Creare nuove finalità ed espressioni
Aggiungere due nuove finalità: `OrgChart-Manager` e `OrgChart-Reports`. Una volta che LUIS ha restituito una stima per l'app client, il nome della finalità può essere usato come un nome di funzione nell'app client e l'entità Employee può essere usata come parametro di tale funzione.

```
OrgChart-Manager(employee){
    ///
}
```

1. Assicurarsi che l'app relativa alle risorse umane sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** (Compila) nella barra dei menu in alto a destra. 

2. Nella pagina **Intents** (Finalità) selezionare **Create new intent** (Crea nuova finalità). 

3. Immettere `OrgChart-Manager` nella finestra di dialogo popup, quindi selezionare **Done** (Fine).

    ![Creare una nuova finestra popup di messaggi](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |Chi è il superiore di John W. Smith?|
    |A chi fa riferimento John W. Smith?|
    |Chi è il manager di John W. Smith?|
    |A chi fa riferimento direttamente Jill Jones?|
    |Chi è il supervisore di Jill Jones?|

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Screenshot di LUIS in cui si aggiungono nuove espressioni alla finalità")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Non occorre preoccuparsi se l'entità keyPhrase è etichettata nelle espressioni della finalità invece che nell'entità employee. Entrambe sono stimate correttamente nel riquadro di Test e in corrispondenza dell'endpoint. 

5. Selezionare **Intents** (Finalità) nel pannello di navigazione a sinistra.

6. Selezionare **Create new intent** (Crea nuova finalità). 

7. Immettere `OrgChart-Reports` nella finestra di dialogo popup, quindi selezionare **Done** (Fine).

8. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |Chi sono i subalterni di John W. Smith?|
    |Chi fa riferimento a John W. Smith?|
    |Che cosa gestisce John W. Smith?|
    |Chi sono i dipendenti diretti di Jill Jones?|
    |Che cosa supervisiona Jill Jones?|

## <a name="caution-about-example-utterance-quantity"></a>Prestare attenzione alla quantità di espressioni di esempio
La quantità di espressioni di esempio in queste finalità non è sufficiente per eseguire il training di LUIS in modo corretto. In un'app reale, ogni finalità deve avere un minimo di 15 espressioni con una varietà di scelta di parole e lunghezza di espressione. Queste poche espressioni vengono selezionate in modo specifico per evidenziare i criteri. 

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Le nuove finalità e le espressioni richiedono un training. 

1. Nella parte superiore destra del sito Web LUIS selezionare il pulsante **Train** (Esegui il training).

    ![Immagine del pulsante di training](./media/luis-tutorial-pattern/hr-train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Immagine della barra di notifica del completamento](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere una previsione di Language Understanding in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web LUIS selezionare il pulsante **Publish** (Pubblica). 

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

    [![Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. La pubblicazione è completata quando la barra di stato verde nella parte superiore del sito Web conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query nell'endpoint con un'espressione diversa
1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    [ ![Schermata della pagina Publish (Pubblica) con l'URL endpoint evidenziato](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


2. Andare alla fine dell'URL nell'indirizzo e immettere `Who is the boss of Jill Jones?`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Questa query è riuscita? Per questo ciclo di corsi di formazione è riuscita. I punteggi delle due finalità principali sono simili. Poiché il training LUIS non è esattamente lo stesso ogni volta, è presente una certa variazione: questi due punteggi potrebbero invertirsi al successivo ciclo di training. Il risultato è che potrebbe essere restituita la finalità non corretta. 

Usare i criteri per rendere il punteggio della finalità significativamente superiore in percentuale e a una distanza maggiore dal punteggio più alto successivo. 

## <a name="add-the-template-utterances"></a>Aggiungere espressioni modello

1. Selezionare **Build** (Compila) nel menu in alto.

2. Nel riquadro di spostamento sinistro, sotto **Improve app performance** (Migliora le prestazioni dell'app), selezionare **Patterns** (Criteri).

3. Selezionare la finalità **OrgChart-Manager**, quindi immettere le espressioni modello seguenti, una alla volta, selezionando invio dopo ognuna:

    |Espressioni modello|
    |:--|
    |Chi è il superiore di {Employee}[?]|
    |A chi fa riferimento {Employee}[?]|
    |Chi è il manager di {Employee}[?]|
    |A chi fa riferimento direttamente {Employee}[?]|
    |Chi è supervisore di {Employee}[?]|
    |Chi è il capo di {Employee}[?]|

    La sintassi `{Employee}` contrassegna la posizione dell'entità nell'espressione modello e specifica di quale entità si tratta. 
    
    Le entità con i ruoli usano la sintassi che include il nome del ruolo,   , e sono trattate in un'[esercitazione separata per i ruoli](luis-tutorial-pattern-roles.md). 

    La sintassi facoltativa, `[]`, contrassegna le parole o i caratteri di punteggiatura che sono facoltativi. LUIS mette in corrispondenza le espressioni ignorando il testo facoltativo tra parentesi.

    Se si digita l'espressione modello, LUIS consente di compilare l'entità quando si immette la parentesi graffa sinistra, `{`.

    [ ![Screenshot dell'immissione delle espressioni modello per la finalità](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



4. Selezionare la finalità **OrgChart-Reports**, quindi immettere le espressioni modello seguenti, una alla volta, selezionando invio dopo ognuna di esse:

    |Espressioni modello|
    |:--|
    |Chi sono i subalterni di {Employee}[?]|
    |Chi fa riferimento a {Employee}[?]|
    |Che cosa gestisce {Employee}[?]|
    |Chi sono i dipendenti diretti di {Employee}[?]|
    |Che cosa supervisiona {Employee}[?]|
    |Che cosa fa il capo di {Employee}[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Eseguire query sull'endpoint quando vengono usati i criteri

1. Eseguire il training dell'app e ripubblicarla.

2. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

3. Andare alla fine dell'URL nell'indirizzo e immettere `Who is the boss of Jill Jones?` come espressione. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

La stima della finalità a questo punto è significativamente più elevata. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS. A tale scopo, selezionare i puntini di sospensione (***...***) a destra del nome dell'app nell'elenco di app e quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare i ruoli con un criterio](luis-tutorial-pattern-roles.md)