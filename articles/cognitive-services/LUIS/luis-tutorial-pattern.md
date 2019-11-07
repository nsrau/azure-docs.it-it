---
title: 'Esercitazione: Criteri - LUIS'
titleSuffix: Azure Cognitive Services
description: Usare criteri per migliorare le stime in termini di finalità ed entità fornendo poche espressioni di esempio. Il criterio viene fornito tramite un esempio di espressione modello, che include la sintassi per identificare le entità e il testo che può essere ignorato.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 4e4f1787db86378eaeff9df196cc061c42d0ab1e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498999"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Esercitazione: Aggiungere formati comuni di espressione modello di criterio

In questa esercitazione, i criteri vengono usati per migliorare le stime in termini di finalità ed entità fornendo poche espressioni di esempio. Il criterio viene fornito tramite un esempio di espressione modello, che include la sintassi per identificare le entità e il testo che può essere ignorato. Un criterio è una combinazione di corrispondenza a espressioni e apprendimento automatico.  L'esempio di espressione modello, insieme alle espressioni della finalità, offrono a LUIS una migliore comprensione delle espressioni appropriate per la finalità. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Importare l'app di esempio 
> * Creare finalità
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint
> * Creare un criterio
> * Verificare i miglioramenti di stima del criterio
> * Contrassegnare il testo ignorabile e annidare in un criterio
> * Verificare l'efficacia del criterio attraverso il pannello per il test

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importare l'app di esempio

Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Seguire questa procedura:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `patterns`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="create-new-intents-and-their-utterances"></a>Creare nuove finalità ed espressioni

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

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

    [![Screenshot di LUIS in cui si aggiungono nuove espressioni alla finalità](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Screenshot di LUIS in cui si aggiungono nuove espressioni alla finalità")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `Who is the boss of Jill Jones?`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

    ```json
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

## <a name="template-utterances"></a>Espressioni modello
Data la natura del dominio risorse umane, esistono alcuni modi comuni di porre domande sulle relazioni dei dipendenti all'interno delle organizzazioni. Ad esempio:

|Espressioni|
|--|
|A chi riferisce Jill Jones?|
|Chi riferisce a Jill Jones?|

Queste espressioni sono troppo simili per determinare l'univocità contestuale di ciascuna senza fornire molte espressioni di esempio. Aggiungendo un criterio per una finalità, LUIS apprende criteri di espressioni comuni per una finalità senza fornire molte espressioni di esempio. 

Gli esempi di espressioni modello per questa finalità includono:

|Esempi di espressioni modello|significato della sintassi|
|--|--|
|A chi fa riferimento {Employee}[?]|interscambiabile {Employee}, ignorare [?]}|
|Chi fa riferimento a {Employee}[?]|interscambiabile {Employee}, ignorare [?]}|

La sintassi `{Employee}` contrassegna la posizione dell'entità nell'espressione modello e specifica di quale entità si tratta. La sintassi facoltativa, `[?]`, contrassegna le parole o i segni di punteggiatura che sono opzionali. LUIS mette in corrispondenza le espressioni ignorando il testo facoltativo tra parentesi.

La sintassi non ricade tra le espressioni regolari nonostante la somiglianza. Solo le sintassi della parentesi graffa, `{}`, e della parentesi quadra, `[]`, sono supportate. Possono essere nidificate fino a due livelli.

Affinché un criterio possa essere messo in corrispondenza con un'espressione, le entità all'interno dell'espressione devono corrispondere per prima cosa alle entità nell'espressione modello. Tuttavia, il modello non consente di stimare le entità, solo le finalità. 

**Mentre i criteri consentono di fornire un numero inferiore espressioni di esempio, se le entità non vengono rilevate, il criterio non corrisponde.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Aggiungere i criteri per la finalità OrgChart-Manager

1. Selezionare **Build** (Compila) nel menu in alto.

2. Nel riquadro di spostamento sinistro, sotto **Improve app performance** (Migliora le prestazioni dell'app), selezionare **Patterns** (Criteri).

3. Selezionare la finalità **OrgChart-Manager**, quindi inserire le espressioni modello seguenti:

    |Espressioni modello|
    |:--|
    |Chi è il superiore di {Employee}[?]|
    |A chi fa riferimento {Employee}[?]|
    |Chi è il manager di {Employee}[?]|
    |A chi fa riferimento direttamente {Employee}[?]|
    |Chi è supervisore di {Employee}[?]|
    |Chi è il capo di {Employee}[?]|

    Le entità con i ruoli usano la sintassi che include il nome del ruolo e vengono presentate in un'[esercitazione separata per i ruoli](luis-tutorial-pattern-roles.md). 

    Se si digita l'espressione modello, LUIS consente di compilare l'entità quando si immette la parentesi graffa sinistra, `{`.

    [ ![Screenshot dell'immissione di espressioni modello per una finalità](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Sempre nella pagina Patterns (Criteri) selezionare la finalità **OrgChart-Reports** e quindi immettere le espressioni modello seguenti:

    |Espressioni modello|
    |:--|
    |Chi sono i subalterni di {Employee}[?]|
    |Chi fa riferimento a {Employee}[?]|
    |Che cosa gestisce {Employee}[?]|
    |Chi sono i dipendenti diretti di {Employee}[?]|
    |Che cosa supervisiona {Employee}[?]|
    |Che cosa fa il capo di {Employee}[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Eseguire query sull'endpoint quando vengono usati i criteri

Ora che sono stati aggiunti i criteri, eseguire il training dell'app, pubblicarla e inviare query all'app nell'endpoint di runtime di stima.

1. Eseguire il training dell'app e ripubblicarla.

1. Tornare alla scheda del browser dell'URL dell'endpoint.

1. Andare alla fine dell'URL nell'indirizzo e immettere `Who is the boss of Jill Jones?` come espressione. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

    ```json
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

A questo punto, la stima della finalità è significativamente più attendibile.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Uso di testo facoltativo e di entità predefinite

Le espressioni modello di criterio precedenti mostrate in questa esercitazione contengono alcuni esempi di testo facoltativo come l'uso del genitivo sassone, `'s`, e del punto interrogativo, `?`. Si supponga di dover includere date attuali e future nel testo dell'espressione.

Le espressioni di esempio sono:

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Ognuno di questi esempi usa un tempo verbale, `was`, `is`, `will be`, insieme a una data, `March 3`, `now` e `in a month`, che LUIS deve essere in grado di stimare correttamente. Si noti che gli ultimi due esempi usano praticamente lo stesso testo, ad eccezione di `in` e `on`.

Espressioni modello di esempio che consentono queste informazioni facoltative: 

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


L'uso della sintassi facoltativa con parentesi quadre `[]` semplifica l'aggiunta di questo testo facoltativo all'espressione modello, può essere annidata fino a un secondo livello, `[[]]`, e includere entità o testo.


**Domanda: perché tutte le lettere `w`, ovvero la prima lettera in ogni espressione modello, sono minuscole? Non dovrebbero essere facoltativamente maiuscole o minuscole?** L'espressione inviata all'endpoint della query dall'applicazione client viene convertita in minuscola. L'espressione modello può essere maiuscola o minuscola, come l'espressione dell'endpoint. Il confronto viene eseguito sempre dopo la conversione in minuscole.

**Domanda: perché non è presente la parte del numero predefinita dell'espressione modello se March 3 è stimato sia come numero `3` sia come data `March 3`?** L'espressione modello usa contestualmente una data, letteralmente come in `March 3` o in modo astratto come `in a month`. Una data può contenere un numero, ma un numero può non necessariamente essere interpretato come data. Usare sempre l'entità che rappresenta al meglio il tipo che si vuole venga restituito nei risultati JSON della stima.  

**Domanda: che cosa succede in caso di espressioni formulate in modo inesatto come `Who will {Employee}['s] manager be on March 3?`.** Tempi verbali grammaticamente diversi come in questo esempio, in cui `will` e `be` sono separati, devono essere una nuova espressione modello. L'espressione modello esistente non verrà restituita come corrispondente a questo esempio. Anche se la finalità dell'espressione non è cambiata, è cambiata la posizione delle parole nell'espressione. Questa modifica influisce sulla stima in LUIS. È possibile [applicare la sintassi di raggruppamento e l'operatore OR](#use-the-or-operator-and-groups) ai tempi verbali per combinare queste espressioni. 

**Ricordare che vengono trovate le entità per prime e quindi il criterio.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Modificare l'espressione modello di criterio esistente

1. Nel sito Web LUIS selezionare **Compila** nel menu in alto e quindi selezionare **Criteri** nel menu a sinistra. 

1. Cercare l'espressione modello esistente, `Who is {Employee}['s] manager[?]`, selezionare i puntini di sospensione (***...***) a destra e quindi selezionare **Edit** (Modifica) dal menu a comparsa. 

1. Modificare l'espressione modello in `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Aggiungere nuove espressioni modello di criterio

1. Sempre nella sezione **Criteri** di **Compila** aggiungere nuove espressioni modello di criterio. Scegliere **OrgChart-Manager** dal menu a discesa Finalità e immettere ognuna delle espressioni modello seguenti:

    |Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Eseguire il training dell'app.

3. Selezionare **Test** nella parte superiore del pannello per aprire il pannello di test. 

4. Immettere diverse espressioni di test per verificare che venga trovata la corrispondenza con il criterio e che il punteggio della finalità sia significativamente alto. 

    Dopo aver immesso la prima espressione, selezionare **Ispeziona** sotto il risultato per poter visualizzare tutti i risultati delle stime. Ogni espressione deve avere la finalità **OrgChart-Manager** e deve estrarre i valori per le entità Employee e datetimeV2.

    |Espressione|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

Tutte queste espressioni hanno individuato le entità all'interno e di conseguenza corrisponderanno allo stesso criterio e avranno un punteggio di stima elevato.

## <a name="use-the-or-operator-and-groups"></a>Usare l'operatore OR e i gruppi

Diverse espressioni modello precedenti sono molto simili. Usare la sintassi di **raggruppamento** `()` e l'operatore **OR** `|` per ridurre le espressioni modello. 

I due criteri seguenti possono essere combinati in un singolo criterio usando la sintassi di raggruppamento `()` e l'operatore OR `|`.

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|--|--|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

La nuova espressione modello sarà: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Nel modello vengono usati i caratteri di **raggruppamento** attorno al tempo verbale richiesto e le preposizioni facoltative `in` e `on` separate da un carattere pipe, **OR**. 

1. Nella pagina **Patterns** (Criteri) selezionare il filtro **OrgChart-Manager**. Limitare l'elenco eseguendo la ricerca di `manager`. 

    ![Nei criteri della finalità OrgChart-Manager cercare il termine "manager"](./media/luis-tutorial-pattern/search-patterns.png)

1. Mantenere una sola versione dell'espressione modello (da modificare nel passaggio successivo) ed eliminare le altre varianti. 

1. Modificare l'espressione modello nel modo seguente: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Eseguire il training dell'app.

1. Usare il riquadro Test per testare le versioni dell'espressione:

    |Espressioni da immettere nel riquadro Test|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Usare gli ancoraggi di inizio e fine delle espressioni

La sintassi dei criteri include ancoraggi di inizio e fine delle espressioni rappresentati da un accento circonflesso, `^`. Gli ancoraggi di inizio e fine delle espressioni possono essere usati insieme per identificare un'espressione molto specifica e possibilmente letterale e oppure possono usati separatamente per identificare finalità. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione aggiunge due finalità per espressioni per le quali risultava difficile fare una stima accurata senza disporre di molte espressioni di esempio. L'aggiunta di criteri ha consentito a LUIS di stimare meglio la finalità ottenendo un punteggio decisamente più alto. Contrassegnare le entità e il testo che può essere ignorato consente a LUIS di applicare il criterio a un'ampia gamma di espressioni.

> [!div class="nextstepaction"]
> [Informazioni su come usare i ruoli con un criterio](luis-tutorial-pattern-roles.md)
