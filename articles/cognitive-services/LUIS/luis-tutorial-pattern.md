---
title: 'Esercitazione: Criteri - LUIS'
description: In questa esercitazione vengono usati i criteri per migliorare le previsioni in termini di finalità ed entità fornendo poche espressioni di esempio. Il criterio viene fornito tramite un esempio di espressione modello, che include la sintassi per identificare le entità e il testo ignorabile.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 9814304aed4d7a5f307fb2179491b0fa9635fd68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324655"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Esercitazione: Aggiungere formati comuni di espressioni modello basati su criteri per migliorare le previsioni

In questa esercitazione vengono usati i criteri per migliorare le previsioni in termini di finalità ed entità fornendo poche espressioni di esempio. Il criterio è un'espressione modello assegnata a una finalità, contenente la sintassi per identificare le entità e il testo ignorabile.

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare un criterio
> * Verificare i miglioramenti di stima del criterio
> * Contrassegnare il testo ignorabile e annidare in un criterio
> * Verificare l'efficacia del criterio attraverso il pannello per il test

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Espressioni in finalità e criteri

Nell'app LUIS sono archiviati due tipi di espressioni:

* Espressioni di esempio nella finalità
* Espressioni modello nel criterio

L'aggiunta di espressioni modello come criterio consente di fornire un minor numero complessivo di espressioni di esempio in una finalità.

Un criterio viene applicato come combinazione di corrispondenza di testo e Machine Learning.  Con l'espressione modello nel criterio, insieme alle espressioni di esempio nella finalità, LUIS è in grado di riconoscere meglio quali espressioni sono appropriate per la finalità.

## <a name="import-example-app-and-clone-to-new-version"></a>Importare l'app di esempio e clonarla in una nuova versione

Eseguire la procedura descritta di seguito:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Importare il file JSON in una nuova app nel [portale LUIS](https://www.luis.ai). Nella pagina **My Apps** (App personali) selezionare **+ New app for conversation** (Nuova app di conversazione), quindi **Import as JSON** (Importa come JSON). Selezionare il file scaricato nel passaggio precedente e assegnare all'app il nome `Patterns tutorial`.

## <a name="create-new-intents-and-their-utterances"></a>Creare nuove finalità ed espressioni

Le due finalità trovano il manager o i suoi dipendenti diretti, in base al testo dell'espressione. La difficoltà è che le due finalità hanno _significati_ diversi, ma la maggior parte delle parole sono identiche. Quello che cambia è solo l'ordine delle parole. Per prevedere correttamente la finalità, è necessario avere molti esempi.

1. Selezionare **Build** (Compila) sulla barra di spostamento.

1. Nella pagina **Intents** (Finalità) selezionare **+ Create** (Crea) per creare una nuova finalità.

1. Immettere `OrgChart-Manager` nella finestra di dialogo popup, quindi selezionare **Done** (Fine).

    ![Creare una nuova finestra popup di messaggi](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Aggiungere espressioni di esempio alla finalità. Queste espressioni non sono _esattamente_ uguali, ma hanno un criterio che è possibile estrarre.

    |Espressioni di esempio|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Selezionare **Intents** (Finalità) nel pannello di navigazione a sinistra.

1. Selezionare **+ Create** (Crea) per creare una nuova finalità. Immettere `OrgChart-Reports` nella finestra di dialogo popup, quindi selezionare **Done** (Fine).

1. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Prestare attenzione alla quantità di espressioni di esempio

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Eseguire il training dell'app prima del test o della pubblicazione

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Pubblicare l'app per eseguire query dall'endpoint

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e sostituire _YOUR_QUERY_HERE_ con `Who is the boss of Jill Jones?`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

È stata stimata la finalità principale corretta, ovvero `OrgChart-Manager`, ma il punteggio non è superiore al 70% e non è sufficientemente elevato rispetto alla successiva finalità più elevata. Usare i criteri per rendere il punteggio della finalità significativamente superiore in percentuale e a una distanza maggiore dal punteggio più alto successivo.

Lasciare aperta la seconda finestra del browser. Verrà usata più avanti in questa esercitazione.

## <a name="template-utterances"></a>Espressioni modello
Data la natura del dominio di risorse umane, esistono alcuni modi comuni di porre domande sulle relazioni dei dipendenti all'interno delle organizzazioni. Ad esempio:

|Espressioni|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Queste espressioni sono troppo simili per determinare l'univocità contestuale di ciascuna senza fornire _molte_ espressioni di esempio. Aggiungendo un criterio per una finalità, LUIS apprende criteri di espressioni comuni per una finalità senza dover fornire molte altre espressioni di esempio.

Gli esempi di espressioni modello per questa finalità includono:

|Esempi di espressioni modello|significato della sintassi|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|interchangeable `{EmployeeListEntity}`<br>ignore `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|interchangeable `{EmployeeListEntity}`<br>ignore `[?]`|

La sintassi `{EmployeeListEntity}` contrassegna la posizione dell'entità nell'espressione modello e specifica di quale entità si tratta. La sintassi facoltativa `[?]` contrassegna le parole o la [punteggiatura](luis-reference-application-settings.md#punctuation-normalization) facoltativa. LUIS mette in corrispondenza le espressioni ignorando il testo facoltativo tra parentesi.

Anche se la sintassi sembra un'espressione regolare, in realtà non lo è. Solo le sintassi della parentesi graffa, `{}`, e della parentesi quadra, `[]`, sono supportate. Possono essere nidificate fino a due livelli.

Affinché un criterio possa essere messo in corrispondenza con un'espressione, le entità all'interno dell'espressione devono corrispondere _prima di tutto_ alle entità nell'espressione modello. Questo significa che le entità devono avere un numero di espressioni di esempio sufficienti con un livello elevato di previsione perché funzionino con i criteri. Tuttavia, il modello non consente di stimare le entità, solo le finalità.

**Mentre i criteri consentono di fornire un numero inferiore espressioni di esempio, se le entità non vengono rilevate, il criterio non corrisponde.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Aggiungere i criteri per la finalità OrgChart-Manager

1. Selezionare **Build** (Compila) nel menu in alto.

1. Nel riquadro di spostamento sinistro, sotto **Improve app performance** (Migliora le prestazioni dell'app), selezionare **Patterns** (Criteri).

1. Selezionare la finalità **OrgChart-Manager**, quindi inserire le espressioni modello seguenti:

    |Espressioni modello|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Queste espressioni modello includono l'entità **EmployeeListEntity** con la notazione tra parentesi graffe.

1. Sempre nella pagina Patterns (Criteri) selezionare la finalità **OrgChart-Reports** e quindi immettere le espressioni modello seguenti:

    |Espressioni modello|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Eseguire query sull'endpoint quando vengono usati i criteri

Ora che sono stati aggiunti i criteri, eseguire il training dell'app, pubblicarla e inviare query all'app nell'endpoint di runtime di stima.

1. Selezionare il pulsante **Train** (Esegui il training). Al termine del training, selezionare **Publish** (Pubblica), lo slot **Production** (Produzione) e quindi **Done** (Fatto).

1. Al termine della pubblicazione, tornare nella scheda del browser dell'URL dell'endpoint.

1. Passare alla fine dell'URL nella barra degli indirizzi e verificare che la query sia ancora `Who is the boss of Jill Jones?` prima di inviare l'URL per una nuova stima.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

La stima della finalità ha ora un livello di attendibilità significativamente più alto e il punteggio della finalità immediatamente successiva è molto basso. Queste due finalità non verranno invertite durante il training.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Uso di testo facoltativo e di entità predefinite

Le espressioni modello di criterio precedenti mostrate in questa esercitazione contengono alcuni esempi di testo facoltativo come l'uso del genitivo sassone, `'s`, e del punto interrogativo, `?`. Si supponga di dover includere date attuali e future nel testo dell'espressione.

Le espressioni di esempio sono:

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Ognuno di questi esempi usa un tempo verbale, `was`, `is`, `will be`, insieme a una data, `March 3`, `now` e `in a month`, che LUIS deve essere in grado di stimare correttamente. Si noti che gli ultimi due esempi della tabella usano quasi lo stesso testo, ad eccezione di `in` e `on`.

Espressioni modello di esempio che consentono queste informazioni facoltative:

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


L'uso della sintassi facoltativa con parentesi quadre `[]` semplifica l'aggiunta di questo testo facoltativo all'espressione modello, può essere annidata fino a un secondo livello, `[[]]`, e includere entità o testo.


**Domanda: perché tutte le lettere `w`, ovvero la prima lettera in ogni espressione modello, sono minuscole? Non dovrebbero essere facoltativamente maiuscole o minuscole?** L'espressione inviata all'endpoint della query dall'applicazione client viene convertita in minuscola. L'espressione modello può essere maiuscola o minuscola, come l'espressione dell'endpoint. Il confronto viene eseguito sempre dopo la conversione in minuscole.

**Domanda: perché non è presente la parte del numero predefinita dell'espressione modello se March 3 è stimato sia come numero `3` sia come data `March 3`?** L'espressione modello usa contestualmente una data, letteralmente come in `March 3` o in modo astratto come `in a month`. Una data può contenere un numero, ma un numero può non necessariamente essere interpretato come data. Usare sempre l'entità che rappresenta al meglio il tipo che si vuole venga restituito nei risultati JSON della stima.

**Domanda: che cosa succede in caso di espressioni formulate in modo inesatto come `Who will {EmployeeListEntity}['s] manager be on March 3?`.** Tempi verbali grammaticamente diversi come in questo esempio, in cui `will` e `be` sono separati, devono essere una nuova espressione modello. L'espressione modello esistente non verrà restituita come corrispondente a questo esempio. Anche se la finalità dell'espressione non è cambiata, è cambiata la posizione delle parole nell'espressione. Questa modifica influisce sulla stima in LUIS. È possibile [applicare la sintassi di raggruppamento e l'operatore OR](#use-the-or-operator-and-groups) ai tempi verbali per combinare queste espressioni.

> [!CAUTION]
> **Ricordare che vengono trovate le entità per prime e quindi il criterio.**

### <a name="add-new-pattern-template-utterances"></a>Aggiungere nuove espressioni modello di criterio

1. Sempre nella sezione **Criteri** di **Compila** aggiungere nuove espressioni modello di criterio. Scegliere **OrgChart-Manager** dal menu a discesa Finalità e immettere ognuna delle espressioni modello seguenti:

    |Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Selezionare **Train** (Esegui il training) sulla barra di spostamento per eseguire il training dell'app.

3. Al termine del training, selezionare **Test** nella parte superiore del pannello per aprire il pannello di test.

4. Immettere diverse espressioni di test per verificare che venga trovata la corrispondenza con il criterio e che il punteggio della finalità sia significativamente alto.

    Dopo aver immesso la prima espressione, selezionare **Ispeziona** sotto il risultato per poter visualizzare tutti i risultati delle stime. Ogni espressione deve avere la finalità **OrgChart-Manager** e deve estrarre i valori per le entità `EmployeeListEntity` e `datetimeV2`.

    |Espressione|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Tutte queste espressioni hanno individuato le entità all'interno e di conseguenza corrisponderanno allo stesso criterio e avranno un punteggio di stima elevato. Sono stati aggiunti alcuni criteri che corrisponderanno a tra molte varianti di espressioni. Non è stato necessario aggiungere espressioni di esempio nella finalità per il corretto funzionamento dell'espressione modello nel criterio.

Questo uso dei criteri ha garantito:
* Punteggi di previsione più alti
* Con le stesse espressioni di esempio nella finalità
* Con alcune espressioni modello ben costruite nel criterio

### <a name="use-the-or-operator-and-groups"></a>Usare l'operatore OR e i gruppi

Diverse espressioni modello precedenti sono molto simili. Usare la sintassi di **raggruppamento** `()` e l'operatore **OR** `|` per ridurre le espressioni modello.

I due criteri seguenti possono essere combinati in un singolo criterio usando la sintassi di raggruppamento `()` e l'operatore OR `|`.

|Finalità|Espressioni di esempio con testo facoltativo ed entità predefinite|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

La nuova espressione modello sarà:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Nel modello vengono usati i caratteri di **raggruppamento** attorno al tempo verbale richiesto e le preposizioni facoltative `in` e `on` separate da un carattere pipe, **OR**.

> [!NOTE]
> Quando si usa il simbolo _OR_, `|` (barra verticale), assicurarsi di aggiungere uno spazio prima e dopo la barra verticale nel modello di esempio.

1. Nella pagina **Patterns** (Criteri) selezionare il filtro **OrgChart-Manager**. Limitare l'elenco eseguendo la ricerca di `manager`.

1. Mantenere una sola versione dell'espressione modello (da modificare nel passaggio successivo) ed eliminare le altre varianti.

1. Modificare l'espressione modello nel modo seguente:

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selezionare **Train** (Esegui il training) sulla barra di spostamento per eseguire il training dell'app.

3. Al termine del training, selezionare **Test** nella parte superiore del pannello per aprire il pannello di test.

    Usare il riquadro Test per testare le versioni dell'espressione:

    |Espressioni da immettere nel riquadro Test|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Usando più sintassi dei criteri, è possibile ridurre il numero di espressioni modello che è necessario mantenere nell'app, pur avendo comunque un punteggio di previsione elevato.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Usare gli ancoraggi di inizio e fine delle espressioni

La sintassi dei criteri include ancoraggi di inizio e fine delle espressioni rappresentati da un accento circonflesso, `^`. Gli ancoraggi di inizio e fine delle espressioni possono essere usati insieme per identificare un'espressione molto specifica e possibilmente letterale e oppure possono usati separatamente per identificare finalità.

## <a name="using-patternany-entity"></a>Uso dell'entità pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Aggiungere espressioni di esempio con Pattern.any

1. Selezionare **Build** (Compila) dalla barra di spostamento in alto, quindi selezionare **Intents** (Finalità) dalla barra di spostamento a sinistra.

1. Selezionare **FindForm** dall'elenco delle finalità.

1. Aggiungere alcune espressioni di esempio. Il testo da prevedere come Pattern.any è in **grassetto**. Il nome del modulo è difficile da determinare in base alle altre parole che lo circondano nell'espressione. Pattern.any risulta utile contrassegnando i limiti dell'entità.

    |Espressione di esempio|Nome del modulo|
    |--|--|
    |Dove è il modulo **Cosa fare quando scoppia un incendio nel laboratorio** e chi deve firmare dopo la lettura?|Cosa fare quando scoppia un incendio nel laboratorio
    |Dove si trova la **Richiesta di trasferimento del nuovo dipendente dell'azienda**?|Richiesta di trasferimento del nuovo dipendente dell'azienda|
    |Chi ha creato "**Richieste per la salute e il benessere nel campus principale**" e qual è la versione più recente?|Richieste per la salute e il benessere nel campus principale|
    |Sto cercando il modulo denominato "**Richiesta di spostamento di uffici incluse le risorse fisiche**". |Richiesta di spostamento di uffici incluse le risorse fisiche|

    Senza un'entità Pattern.any, sarebbe difficile per LUIS capire dove finisce il titolo del modulo a causa delle numerose variazioni dei nomi di modulo.

### <a name="create-a-patternany-entity"></a>Creare un'entità pattern.any
L'entità Pattern.any estrae entità di lunghezza variabile. Funziona solo in un criterio perché il criterio contrassegna l'inizio e la fine dell'entità con la sintassi.

1. Selezionare **Entities** (Entità) nel riquadro di spostamento sinistro.

1. Selezionare **+ Create** (Crea), immettere il nome `FormName` e selezionare **Pattern.any** come tipo. Selezionare **Crea**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Aggiungere un criterio che usa Pattern.any

1. Selezionare **Patterns** (Criteri) dal riquadro di spostamento sinistro.

1. Selezionare la finalità **FindForm**.

1. Immettere le espressioni modello seguenti che usano la nuova entità:

    |Espressioni modello|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Eseguire il training dell'app.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testare il nuovo criterio per l'estrazione dei dati in formato libero
1. Selezionare **Test** nella barra superiore per aprire il pannello test.

1. Immettere l'espressione seguente:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Selezionare **Inspect** (Ispeziona) sotto il risultato per visualizzare i risultati del test per entità e finalità.

    Prima viene trovata l'entità `FormName`, poi il criterio a determinare la finalità. Se nel risultato di un test le entità non vengono rilevate e di conseguenza il criterio non viene trovato, è necessario aggiungere un maggior numero di espressioni di esempio sulla finalità (non il criterio).

1. Chiudere il pannello test selezionando il pulsante **Test** nella barra di spostamento superiore.

### <a name="using-an-explicit-list"></a>Uso di un elenco esplicito

Se si rileva che il criterio, quando include Pattern.any, estrae le entità in modo errato, usare un [elenco esplicito](reference-pattern-syntax.md#explicit-lists) per risolvere il problema.

## <a name="what-did-this-tutorial-accomplish"></a>Risultati dell'esercitazione

In questa esercitazione sono stati aggiunti criteri per consentire a LUIS di prevedere la finalità con un punteggio significativamente più alto senza la necessità di aggiungere più espressioni di esempio. Contrassegnare le entità e il testo che può essere ignorato consente a LUIS di applicare il criterio a un'ampia gamma di espressioni.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi


> [!div class="nextstepaction"]
> [Informazioni su come usare i ruoli con un criterio](luis-tutorial-pattern.md)
