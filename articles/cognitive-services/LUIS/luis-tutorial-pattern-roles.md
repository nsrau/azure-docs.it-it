---
title: Esercitazione che usa i ruoli di criteri per migliorare le stime LUIS - Azure | Microsoft Docs
titleSuffix: Cognitive Services
description: In questa esercitazione usare i ruoli di criteri per le entità correlate in base al contesto per migliorare le stime di LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 08/03/2018
ms.author: diberry
ms.openlocfilehash: 6f3e7c9db7bbdb6bc24d123208355fc7a1d8e7e8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161935"
---
# <a name="tutorial-improve-app-with-pattern-roles"></a>Esercitazione: Migliorare le app con i ruoli di criteri

In questa esercitazione usare un'entità semplice con i ruoli combinati con i criteri per aumentare la stima di finalità ed entità.  Quando si usano i criteri, per la finalità sono necessarie meno espressioni di esempio.

> [!div class="checklist"]
* Comprendere i ruoli di criteri
* Usare un'entità semplice con i ruoli 
* Crea criteri per le espressioni tramite entità semplici con i ruoli
* Come verificare i miglioramenti di stima del criterio

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Se non si dispone dell'app relativa alle risorse umane dell'esercitazione relativa ai [criteri](luis-tutorial-pattern.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-patterns-HumanResources-v2.json).

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `roles`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="the-purpose-of-roles"></a>Scopo dei ruoli
Lo scopo dei ruoli è quello di estrarre le entità correlate in base al contesto in un'espressione. Nell'espressione `Move new employee Robert Williams from Sacramento and San Francisco` i valori della città di origine e di destinazione sono correlati tra loro e usano il linguaggio comune per denotare ogni luogo. 

Quando si usano i criteri, tutte le entità nel criterio devono essere rilevate _prima_ che il criterio corrisponda all'espressione. 

Quando si crea un criterio, il primo passo è quello di selezionare una finalità per il criterio. Se si seleziona la finalità e se il criterio corrisponde, la finalità corretta viene sempre restituita con un punteggio elevato (in genere 99-100%). 

### <a name="compare-hierarchical-entity-to-simple-entity-with-roles"></a>Confrontare entità gerarchiche a entità semplici con i ruoli

Nell'[esercitazione relativa alle entità gerarchiche](luis-quickstart-intent-and-hier-entity.md) la finalità **MoveEmployee** rilevava il momento in cui si spostava un dipendente esistente da un edificio o da un ufficio a un altro. Per le espressioni di esempio erano presenti luoghi di origine e di destinazione, ma non venivano usati i ruoli. L'origine e la destinazione erano invece elementi figlio dell'entità gerarchica. 

In questa esercitazione, l'app relativa alle risorse umane rileva espressioni sullo spostamento di nuovi dipendenti da una città a un'altra. Questi due tipi di espressioni sono simili, ma realizzati con funzionalità di LUIS diverse.

|Esercitazione|Espressione di esempio|Luoghi di origine e destinazione|
|--|--|--|
|[Entità gerarchiche (senza ruoli)](luis-quickstart-intent-and-hier-entity.md)|mv Jill Jones from **a-2349** to **b-1298**|a-2349, b-1298|
|Questa esercitazione (con i ruoli)|Move Billy Patterson from **Yuma** to **Denver**.|Yuma, Denver|

Non è possibile usare l'entità gerarchica nel criterio perché solo gli elementi gerarchici padre vengono usati nei criteri. Per restituire i luoghi di origine e destinazione, è necessario usare un criterio.

### <a name="simple-entity-for-new-employee-name"></a>Entità semplice per il nuovo nome del dipendente
Il nome del nuovo dipendente, Billy Patterson, non fa ancora parte dell'elenco di entità **Employee**. Il nome del nuovo dipendente viene estratto prima in modo da inviarlo a un sistema esterno per creare le credenziali aziendali. Dopo che le credenziali aziendali sono state create, le credenziali del dipendente vengono aggiunte all'entità elenco **Employee**.

L'elenco **Employee** è stato creato nell'[esercitazione relativa agli elenchi](luis-quickstart-intent-and-list-entity.md).

La nuova entità **NewEmployee** è un'entità semplice senza alcun ruolo. 

### <a name="simple-entity-with-roles-for-relocation-cities"></a>Entità semplice con i ruoli per le città di trasferimento
Il nuovo dipendente e la famiglia devono essere spostati dalla città corrente a una città in cui si trova la società fittizia. Poiché un nuovo dipendente può provenire da qualsiasi città,è necessario individuare i luoghi. Un elenco impostato, ad esempio un'entità elenco, non funzionerebbe perché verrebbero estratte solo le città presenti nell'elenco.

I nomi dei ruoli associati alle città di origine e destinazione devono essere univoci per tutte le entità. Un modo semplice per verificare che i ruoli siano univoci è quello di collegarli all'entità che li contiene tramite una strategia di denominazione. L'entità **NewEmployeeRelocation** è un'entità semplice con due ruoli: **NewEmployeeReloOrigin** e **NewEmployeeReloDestination**.

### <a name="simple-entities-need-enough-examples-to-be-detected"></a>Le entità semplici devono disporre di esempi sufficienti per essere rilevate
Poiché l'espressione di esempio `Move new employee Robert Williams from Sacramento and San Francisco` dispone solo di entità apprese in modo automatico, è importante indicare alla finalità espressioni di esempio sufficienti in modo che le entità vengano rilevate.  

**Mentre i criteri consentono di fornire un numero inferiore espressioni di esempio, se le entità non vengono rilevate, il criterio non corrisponde.**

Se si hanno difficoltà con il rilevamento di entità semplici perché l'entità è un nome, ad esempio una città, prendere in considerazione l'aggiunta di un elenco di frasi con valori simili. In questo modo il rilevamento del nome della città viene semplificato perché si indica a LUIS un'informazione aggiuntiva sul tipo di parola o frase specifica. Gli elenchi di frasi sono utili al criterio solo perché facilitano il rilevamento di entità, operazione necessaria per la corrispondenza del criterio. 

## <a name="create-new-entities"></a>Creare nuove entità
1. Selezionare **Build** (Compila) nel menu in alto.

2. Selezionare **Entities** (Entità) nel riquadro di spostamento a sinistra. 

3. Selezionare **Create new entity** (Crea nuova entità).

4. Nella finestra popup immettere `NewEmployee` come un'entità **Simple** (Semplice).

5. Selezionare **Create new entity** (Crea nuova entità).

6. Nella finestra popup immettere `NewEmployeeRelocation` come un'entità **Simple** (Semplice).

7. Selezionare **NewEmployeeRelocation** nell'elenco di entità. 

8. Immettere il primo ruolo come `NewEmployeeReloOrigin` e quindi premere INVIO.

9. Immettere il secondo ruolo come `NewEmployeeReloDestination` e quindi premere INVIO.

## <a name="create-new-intent"></a>Creare una nuova finalità
L'assegnazione di etichette alle entità in questa procedura può risultare più semplice se l'entità keyPhrase predefinita viene rimossa prima di iniziare e quindi aggiunta nuovamente dopo aver completato la procedura descritta in questa sezione. 

1. Selezionare **Intents** (Finalità) nel pannello di spostamento sinistro.

2. Selezionare **Create new intent** (Crea nuova finalità). 

3. Immettere `NewEmployeeRelocationProcess` come nome di finalità nella finestra di dialogo popup.

4. Immettere le espressioni di esempio seguenti, assegnando etichette alle nuove entità. I valori di entità e di ruolo sono in grassetto. Ricordare di passare a **Tokens View** (Visualizzazione token) se si ritiene più semplice etichettare il testo. 

    Non specificare il ruolo dell'entità quando la si etichetta nella finalità. Questa operazione viene eseguita in un momento successivo quando si crea il criterio. 

    |Espressione|NewEmployee|NewEmployeeRelocation|
    |--|--|--|
    |Move **Bob Jones** from **Seattle** to **Los Colinas**|Bob Jones|Seattle, Los Colinas|
    |Move **Dave C. Cooper** from **Redmond** to **New York City**|Dave C. Cooper|Redmond, New York City|
    |Move **Jim Paul Smith** from **Toronto** to **West Vancouver**|Jim Paul Smith|Toronto, West Vancouver|
    |Move **J. Benson** from **Boston** to **Staines-upon-Thames**|J. Benson|Boston, Staines-upon-Thames|
    |Move **Travis "Trav" Hinton** from **Castelo Branco** to **Orlando**|Travis "Trav" Hinton|Castelo Branco, Orlando|
    |Move **Trevor Nottington III** from **Aranda de Duero** to **Boise**|Trevor Nottington III|Aranda de Duero, Boise|
    |Move **Dr. Greg Williams** from **Orlando** to **Ellicott City**|Dr. Greg Williams|Orlando, Ellicott City|
    |Move **Robert "Bobby" Gregson** from **Kansas City** to **San Juan Capistrano**|Robert "Bobby" Gregson|Kansas City, San Juan Capistrano|
    |Move **Patti Owens** from **Bellevue** to **Rockford**|Patti Owens|Bellevue, Rockford|
    |Move **Janet Bartlet** from **Tuscan** to **Santa Fe**|Janet Bartlet|Tuscan, Santa Fe|

    Il nome del dipendente è costituito da un'ampia gamma di prefissi, numeri di parole, sintassi e suffissi. Ciò è importante per LUIS per comprendere le variazioni di un nuovo nome di dipendente. Anche i nomi di città sono costituiti da un'ampia gamma di numeri di parole e di sintassi. Questa varietà è importante per indicare a LUIS come queste entità possono essere visualizzate in un'espressione dell'utente. 
    
    Se entrambe le entità fossero costituite dallo stesso numero di parole e non presentassero altre variazioni, si indicherebbe a LUIS che l'entità è costituita solo da tale numero di parole e non da altre variazioni. LUIS non sarebbe in grado di stimare correttamente un set più ampio di variazioni perché non ne sono stati mostrati altri. 

    Se è stata rimossa l'entità keyPhrase, aggiungerla nuovamente all'app.

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-without-pattern"></a>Query sull'endpoint senza criterio

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Andare alla fine dell'URL nell'indirizzo e immettere `Move Wayne Berry from Miami to Mount Vernon`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

    ```JSON
    {
      "query": "Move Wayne Berry from Newark to Columbus",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.514479756
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.514479756
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.017118983
        },
        {
          "intent": "MoveEmployee",
          "score": 0.009982505
        },
        {
          "intent": "GetJobInformation",
          "score": 0.008637771
        },
        {
          "intent": "ApplyForJob",
          "score": 0.007115978
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.006120186
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00452428637
        },
        {
          "intent": "None",
          "score": 0.00400899537
        },
        {
          "intent": "OrgChart-Reports",
          "score": 0.00240071164
        },
        {
          "intent": "Utilities.Help",
          "score": 0.001770991
        },
        {
          "intent": "EmployeeFeedback",
          "score": 0.001697356
        },
        {
          "intent": "OrgChart-Manager",
          "score": 0.00168116146
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00163952739
        },
        {
          "intent": "FindForm",
          "score": 0.00112958835
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.629158735
        },
        {
          "entity": "newark",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 27,
          "score": 0.638941
        }
      ]
    }  
    ```

Il punteggio di stima della finalità è pari solo al 50%. Se l'applicazione client richiede un valore più alto, tale punteggio deve essere corretto. Nemmeno le entità sono state stimate.

Anche se i criteri consentono di assegnare in modo più corretto un punteggio di stima, le entità devono essere stimate correttamente prima che il criterio corrisponda all'espressione. 

## <a name="add-a-pattern-that-uses-roles"></a>Aggiungere un criterio che usa i ruoli
1. Selezionare **Build** (Compila) nel riquadro di spostamento superiore.

2. Selezionare **Patterns** (Criteri) nel riquadro di spostamento a sinistra.

3. Selezionare **NewEmployeeRelocationProcess** nell'elenco a discesa **Select an intent** (Selezionare una finalità). 

4. Immettere il criterio seguente: `move {NewEmployee} from {NewEmployeeRelocation:NewEmployeeReloOrigin} to {NewEmployeeRelocation:NewEmployeeReloDestination}[.]`

    Se si esegue il training, si pubblica e si esegue una query sull'endpoint, si potrebbe rimanere delusi nel vedere che le entità non vengono trovate e che il criterio non corrisponde e che pertanto la stima non è migliorata. Si tratta di una conseguenza di un numero di espressioni di esempio con entità etichettate non sufficiente. Anziché aggiungere altri esempi, aggiungere un elenco di frasi per risolvere il problema.

## <a name="create-a-phrase-list-for-cities"></a>Creare un elenco di frasi per le città
In modo analogo ai nomi di persone, i nomi di città sono complessi perché possono essere qualsiasi combinazione di parole e segni di punteggiatura. Le città dell'area e del mondo sono comunque note e di conseguenza LUIS deve disporre di un elenco di frasi per iniziare ad apprendere. 

1. Selezionare **Phrase list** (Elenco frasi) nella sezione **Improve app performance** (Migliora prestazioni app) nel menu a sinistra. 

2. Assegnare un nome all'elenco `Cities` e aggiungere l'elemento `values` seguente per l'elenco:

    |Valore dell'elenco di frasi|
    |--|
    |Seattle|
    |San Diego|
    |New York City|
    |Los Angeles|
    |Portland|
    |Philadephia|
    |Miami|
    |Dallas|

    Non aggiungere ogni città del mondo né ogni città dell'area. LUIS deve essere in grado di generalizzare la città in base all'elenco. 

    Verificare che l'opzione **These values are interchangeable** (Questi valori sono intercambiabili) sia selezionata. Questa impostazione indica che le parole nell'elenco vengono considerate come sinonimi. Si tratta del modo esatto in cui le parole devono essere considerate nel criterio.

    Ricordare che [l'ultima volta](luis-quickstart-primary-and-secondary-data.md) in cui la serie di esercitazioni ha creato un elenco di frasi è stato per migliorare il rilevamento di un'entità semplice.  

3. Eseguire il training dell'app e pubblicarla.

## <a name="query-endpoint-for-pattern"></a>Query di endpoint per il criterio
1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL dell'endpoint nella barra degli indirizzi. 

2. Passare alla fine dell'URL nell'indirizzo e immettere `Move wayne berry from miami to mount vernon`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

    ```JSON
    {
      "query": "Move Wayne Berry from Miami to Mount Vernon",
      "topScoringIntent": {
        "intent": "NewEmployeeRelocationProcess",
        "score": 0.9999999
      },
      "intents": [
        {
          "intent": "NewEmployeeRelocationProcess",
          "score": 0.9999999
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.49678385E-06
        },
        {
          "intent": "MoveEmployee",
          "score": 8.240291E-07
        },
        {
          "intent": "GetJobInformation",
          "score": 6.3131273E-07
        },
        {
          "intent": "None",
          "score": 4.25E-09
        },
        {
          "intent": "OrgChart-Manager",
          "score": 2.8E-09
        },
        {
          "intent": "OrgChart-Reports",
          "score": 2.8E-09
        },
        {
          "intent": "EmployeeFeedback",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.StartOver",
          "score": 1.64E-09
        },
        {
          "intent": "Utilities.Help",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Stop",
          "score": 1.48181822E-09
        },
        {
          "intent": "Utilities.Cancel",
          "score": 1.35E-09
        },
        {
          "intent": "FindForm",
          "score": 1.23846156E-09
        },
        {
          "intent": "ApplyForJob",
          "score": 5.692308E-10
        }
      ],
      "entities": [
        {
          "entity": "wayne berry",
          "type": "builtin.keyPhrase",
          "startIndex": 5,
          "endIndex": 15
        },
        {
          "entity": "miami",
          "type": "builtin.keyPhrase",
          "startIndex": 22,
          "endIndex": 26
        },
        {
          "entity": "wayne berry",
          "type": "NewEmployee",
          "startIndex": 5,
          "endIndex": 15,
          "score": 0.9410646,
          "role": ""
        },
        {
          "entity": "miami",
          "type": "NewEmployeeRelocation",
          "startIndex": 22,
          "endIndex": 26,
          "score": 0.9853915,
          "role": "NewEmployeeReloOrigin"
        },
        {
          "entity": "mount vernon",
          "type": "NewEmployeeRelocation",
          "startIndex": 31,
          "endIndex": 42,
          "score": 0.986044347,
          "role": "NewEmployeeReloDestination"
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
}
    ```

Il punteggio di finalità è ora molto più elevato e i nomi di ruolo fanno parte della risposta dell'entità.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sulle procedure consigliate per le app LUIS](luis-concept-best-practices.md)
