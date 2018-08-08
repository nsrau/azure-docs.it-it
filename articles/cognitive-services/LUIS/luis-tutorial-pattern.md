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
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 355c1edd4fa7433e68a9c0e903f4f782203326fe
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365879"
---
# <a name="tutorial-improve-app-with-patterns"></a>Esercitazione: migliorare le app con i criteri

In questa esercitazione usare i criteri per le finalità per migliorare le stime di finalità ed entità LUIS.  

> [!div class="checklist"]
* Come capire che un criterio potrebbe essere utile per l'app
* Come creare un criterio
* Come verificare i miglioramenti di stima del criterio

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Prima di iniziare

Se non si ha l'app relativa alle risorse umane dell'esercitazione sul [test in batch](luis-tutorial-batch-testing.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json).

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `patterns`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>I criteri insegnano a LUIS espressioni comuni con un minor numero di esempi

Data la natura del dominio risorse umane, esistono alcuni modi comuni di porre domande sulle relazioni dei dipendenti all'interno delle organizzazioni. Ad esempio: 

|Espressioni|
|--|
|A chi riferisce Jill Jones?|
|Chi riferisce a Jill Jones?|

Queste espressioni sono troppo simili per determinare l'univocità contestuale di ciascuna senza fornire molte espressioni di esempio. Aggiungendo un criterio per una finalità, LUIS apprende criteri di espressioni comuni per una finalità senza fornire molte espressioni di esempio. 

Le espressioni modello di esempio per questa finalità includono:

|Espressioni modello di esempio|
|--|
|A chi riferisce {Employee}?|
|Chi riferisce a {Employee}?|

Il criterio viene fornito tramite un esempio di espressione modello, che include la sintassi per identificare le entità e il testo che può essere ignorato. Un criterio è una combinazione di corrispondenza a espressioni regolari e apprendimento automatico.  L'esempio di espressione modello, insieme alle espressioni della finalità, offrono a LUIS una migliore comprensione delle espressioni appropriate per la finalità.

Affinché un criterio possa essere messo in corrispondenza con un'espressione, le entità all'interno dell'espressione devono corrispondere per prima cosa alle entità nell'espressione modello. Tuttavia, il modello non consente di stimare le entità, solo le finalità. 

**Mentre i criteri consentono di fornire un numero inferiore espressioni di esempio, se le entità non vengono rilevate, il criterio non corrisponde.**

Tenere presente che i dipendenti siano stati creati nell'[esercitazione delle entità elenco](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Creare nuove finalità ed espressioni

Aggiungere due nuove finalità: `OrgChart-Manager` e `OrgChart-Reports`. Una volta che LUIS ha restituito una stima per l'app client, il nome della finalità può essere usato come un nome di funzione nell'app client e l'entità Employee può essere usata come parametro di tale funzione.

```Javascript
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

    [![Screenshot di LUIS in cui vengono aggiunte nuove espressioni a una finalità](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Screenshot di LUIS in cui vengono aggiunte nuove espressioni a una finalità")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query nell'endpoint con un'espressione diversa

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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

Lasciare aperta la seconda finestra del browser. Verrà usata più avanti in questa esercitazione. 

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

    Le entità con i ruoli usano la sintassi che include il nome del ruolo e vengono presentate in un'[esercitazione separata per i ruoli](luis-tutorial-pattern-roles.md). 

    La sintassi facoltativa, `[]`, contrassegna le parole o i caratteri di punteggiatura che sono facoltativi. LUIS mette in corrispondenza le espressioni ignorando il testo facoltativo tra parentesi.

    Se si digita l'espressione modello, LUIS consente di compilare l'entità quando si immette la parentesi graffa sinistra, `{`.

    [ ![Screenshot dell'immissione di espressioni modello per una finalità](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

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

2. Tornare alla scheda del browser dell'URL dell'endpoint.

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

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Uso di testo facoltativo e di entità predefinite

Le espressioni modello di criterio precedenti mostrate in questa esercitazione contengono alcuni esempi di testo facoltativo come l'uso del genitivo sassone, `'s`, e del punto interrogativo, `?`. Si supponga che le espressioni dell'endpoint indichino che i responsabili e i rappresentati delle risorse umane cercano dati cronologici e spostamenti pianificati dei datori di lavoro all'interno della società previsti in una data futura.

Le espressioni di esempio sono:

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Ognuno di questi esempi usa un tempo verbale, `was`, `is`, `will be`, insieme a una data, `March 3`, `now` e `in a month`, che LUIS deve essere in grado di stimare correttamente. Si noti che gli ultimi due esempi usano praticamente lo stesso testo, ad eccezione di `in` e `on`.

Espressioni modello di esempio:
|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

L'uso della sintassi facoltativa con parentesi quadre `[]` semplifica l'aggiunta di questo testo facoltativo all'espressione modello, può essere annidata fino a un secondo livello, `[[]]`, e includere entità o testo.

**Domanda: Perché non è possibile combinare le ultime due espressioni di esempio in una singola espressione modello?** Il modello non supporta la sintassi OR. Per recuperare sia la versione `in` sia la versione `on`, ognuna deve essere un'espressione modello separata.

**Domanda: Perché tutte le lettere `w`, ovvero la prima lettera in ogni espressione modello, sono minuscole? Non dovrebbero essere facoltativamente maiuscole o minuscole?** L'espressione inviata all'endpoint della query dall'applicazione client viene convertita in minuscola. L'espressione modello può essere maiuscola o minuscola, come l'espressione dell'endpoint. Il confronto viene eseguito sempre dopo la conversione in minuscole.

**Domanda: Perché non è presente la parte del numero predefinita dell'espressione modello se March 3 è stimato sia come numero `3` sia come data `March 3`?** L'espressione modello usa contestualmente una data, letteralmente come in `March 3` o in modo astratto come `in a month`. Una data può contenere un numero, ma un numero può non necessariamente essere interpretato come data. Usare sempre l'entità che rappresenta al meglio il tipo che si vuole venga restituito nei risultati JSON della stima.  

**Domanda: Che cosa succede in caso di espressioni formulate in modo inesatto come `Who will {Employee}['s] manager be on March 3?`.** Tempi verbali grammaticamente diversi come in questo esempio, in cui `will` e `be` sono separati, devono essere una nuova espressione modello. L'espressione modello esistente non verrà restituita come corrispondente a questo esempio. Anche se la finalità dell'espressione non è cambiata, è cambiata la posizione delle parole nell'espressione. Questa modifica influisce sulla stima in LUIS.

**Ricordare che vengono trovate le entità per prime e quindi il criterio.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Modificare l'espressione modello di criterio esistente

1. Nel sito Web LUIS selezionare **Compila** nel menu in alto e quindi selezionare **Criteri** nel menu a sinistra. 

2. Trovare l'espressione modello esistente, ovvero `Who is {Employee}['s] manager[?]`, e selezionare i puntini di sospensione (***...***) a destra. 

3. Scegliere **Modifica** dal menu popup. 

4. Modificare l'espressione modello in `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Aggiungere nuove espressioni modello di criterio

1. Sempre nella sezione **Criteri** di **Compila** aggiungere nuove espressioni modello di criterio. Scegliere **OrgChart-Manager** dal menu a discesa Finalità e immettere ognuna delle espressioni modello seguenti:

    |Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Eseguire il training dell'app.

3. Selezionare **Test** nella parte superiore del pannello per aprire il pannello di test. 

4. Immettere diverse espressioni di test per verificare che venga trovata la corrispondenza con il criterio e che il punteggio della finalità sia significativamente alto. 

    Dopo aver immesso la prima espressione, selezionare **Ispeziona** sotto il risultato per poter visualizzare tutti i risultati delle stime.

    |Espressione|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

Tutte queste espressioni hanno individuato le entità all'interno e di conseguenza corrisponderanno allo stesso criterio e avranno un punteggio di stima elevato.

## <a name="clean-up-resources"></a>Pulire le risorse

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come usare i ruoli con un criterio](luis-tutorial-pattern-roles.md)