---
title: 'Esercitazione 5: relazioni padre/figlio - entità gerarchica LUIS per i dati acquisiti in base al contesto'
titleSuffix: Azure Cognitive Services
description: Trovare informazioni correlate di dati basati sul contesto. Ad esempio, spostare una località di origine e destinazione di un computer fisico da un edificio e un ufficio a un altro edificio e ufficio sono informazioni correlate.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d3b8d0597f0732a4a3cfab79125a885b2d141c9f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424701"
---
# <a name="tutorial-5-extract-contextually-related-data"></a>Esercitazione 5: estrarre dati correlati in base al contesto
Questa esercitazione illustra come trovare informazioni correlate di dati basati sul contesto. Ad esempio, spostare una località di origine e destinazione di un computer fisico da un edificio e un ufficio a un altro edificio e ufficio sono informazioni correlate. Per generare un ordine di lavoro potrebbero essere necessarie entrambi i dati, i quali sono correlati tra loro.  

L'app determina dove spostare un dipendente, dalla posizione di origine (edificio e ufficio) alla posizione di destinazione (edificio e ufficio). Viene usata l'entità gerarchica per determinare le posizioni nell'espressione. Lo scopo dell'entità **gerarchica** è trovare i dati correlati all'interno dell'espressione in base al contesto. 

L'entità gerarchica è una buona soluzione per questo tipo di dati, perché i due dati:

* Sono entità semplici.
* Sono correlati tra loro nel contesto dell'espressione.
* Usano parole specifiche per indicare ogni posizione. Le parole usate sono, ad esempio, from/to, leaving/headed to, away from/toward.
* Le due località si trovano spesso nella stessa espressione. 
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere le finalità 
> * Aggiungere un'entità gerarchica di località con elementi figlio di origine e destinazione
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente
Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-list-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `hier`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Rimuovere l'entità numero predefinita dall'app
Per visualizzare l'intera espressione e contrassegnare gli elementi figlio gerarchici, è necessario rimuovere temporaneamente l'entità numero predefinita.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Entità** nel menu a sinistra.

3. Selezionare la casella di controllo a sinistra dell'entità numero nell'elenco. Selezionare **Elimina**. 

## <a name="add-utterances-to-moveemployee-intent"></a>Aggiungere espressioni alla finalità MoveEmployee

1. Selezionare **Intents** (Finalità) dal menu a sinistra.

2. Selezionare **MoveEmployee** nell'elenco di finalità.

3. Aggiungere le espressioni di esempio seguenti:

    |Espressioni di esempio|
    |--|
    |Move John W. Smith **to** a-2345|
    |Direct Jill Jones **to** b-3499|
    |Organize the move of x23456 **from** hh-2345 **to** e-0234|
    |Begin paperwork to set x12345 **leaving** a-3459 **headed to** f-34567|
    |Displace 425-555-0000 **away from** g-2323 **toward** hh-2345|

    [ ![Screenshot di LUIS con nuove espressioni nella finalità MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

    Nell'esercitazione sull'[entità elenco](luis-quickstart-intent-and-list-entity.md) un dipendente viene designato per nome, indirizzo di posta elettronica, numero di interno, numero di telefono cellulare o codice fiscale federale degli Stati Uniti. Questi numeri dei dipendenti sono usati nelle espressioni. Le espressioni di esempio precedenti includono modi diversi per indicare le posizioni di origine e di destinazione, con formattazione in grassetto. Un paio di espressioni includono intenzionalmente solo le destinazioni. In questo modo, LUIS può comprendere come vengono inserite tali posizioni nell'espressione quando l'origine non è specificata.     

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="create-a-location-entity"></a>Creare un'entità posizione
LUIS deve comprendere le posizioni tramite etichette per l'origine e la destinazione nelle espressioni. Se è necessario visualizzare l'espressione nella vista token (non elaborata), selezionare l'interruttore **Entities View** (Vista entità) sulla barra sopra le espressioni. Dopo aver spostato l'interruttore, l'etichetta del controllo diventerà **Tokens View** (Vista token).

Si consideri l'espressione seguente:

```JSON
mv Jill Jones from a-2349 to b-1298
```

Nell'espressione sono specificate due posizioni, `a-2349` e `b-1298`. Si supponga che la lettera corrisponda a un nome di edificio e il numero indichi l'ufficio all'interno di tale edificio. È logico che entrambi i valori siano raggruppati come figli di un'entità gerarchica, `Locations`, perché è necessario estrarre entrambi i dati dall'espressione per completare la richiesta nell'applicazione client e i dati sono correlati tra loro. 
 
Se è presente un solo elemento figlio (di origine o di destinazione) di un'entità gerarchica, verrà comunque estratto. Non è necessario individuare tutti gli elementi figlio per estrarne solo uno o alcuni. 

1. Nell'espressione `Displace 425-555-0000 away from g-2323 toward hh-2345` selezionare la parola `g-2323`. Verrà visualizzato un menu a discesa con una casella di testo nella parte superiore. Immettere il nome dell'entità `Locations` nella casella di testo, quindi selezionare **Create new entity** (Crea nuova entità) nel menu a discesa. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Screenshot della creazione di una nuova entità nella pagina delle finalità")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Nella finestra popup, selezionare il tipo di entità **Hierarchical** (Gerarchica) con `Origin` e `Destination` come entità figlio. Selezionare **Operazione completata**.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Screenshot della finestra popup di creazione dell'entità per la nuova entità posizione")

3. L'etichetta per `g-2323` è contrassegnata come `Locations` perché Language Understanding non sa se il termine era l'origine, la destinazione o nessuna delle due. Selezionare `g-2323` e quindi **Locations**, seguire il menu a destra e selezionare `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Screenshot della finestra popup di selezione delle etichette per le entità per la modifica degli elementi figlio dell'entità Locations")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Impostare le etichette per le altre posizioni in tutte le altre espressioni selezionando l'edificio e l'ufficio nell'espressione, quindi selezionando Locations e seguendo il menu a destra per selezionare `Origin` o `Destination`. Una volta etichettate tutte le posizioni, l'aspetto delle espressioni in **Tokens View** (Vista token) diventa simile a un modello. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Screenshot dell'entità Locations etichettata nelle espressioni")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Aggiungere un'entità numero predefinita all'app
Aggiungere di nuovo l'entità numero predefinita nell'applicazione.

1. Scegliere **Entities** (Entità) dal menu di spostamento a sinistra.

2. Selezionare il pulsante **Add prebuilt entity** (Aggiungi entità predefinite).

3. Selezionare **number** nell'elenco di entità predefinite e quindi **Done** (Fine).

    ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query nell'endpoint con un'espressione diversa

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


2. Passare alla fine dell'URL nella barra degli indirizzi e immettere `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, pertanto rappresenta un buon test e dovrebbe restituire la finalità `MoveEmployee` con l'entità gerarchica estratta.

    ```JSON
    {
      "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9966052
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9966052
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0325253047
        },
        {
          "intent": "FindForm",
          "score": 0.006137873
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00462633232
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00415637763
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00382325822
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00249120337
        },
        {
          "intent": "None",
          "score": 0.00130756292
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00119622645
        },
        {
          "intent": "Utilities.Confirm",
          "score": 1.26910036E-05
        }
      ],
      "entities": [
        {
          "entity": "jill - jones @ mycompany . com",
          "type": "Employee",
          "startIndex": 18,
          "endIndex": 41,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "x - 2345",
          "type": "Locations::Origin",
          "startIndex": 48,
          "endIndex": 53,
          "score": 0.8520272
        },
        {
          "entity": "g - 23456",
          "type": "Locations::Destination",
          "startIndex": 58,
          "endIndex": 64,
          "score": 0.974032
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 49,
          "endIndex": 53,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "-23456",
          "type": "builtin.number",
          "startIndex": 59,
          "endIndex": 64,
          "resolution": {
            "value": "-23456"
          }
        }
      ]
    }
    ```
    
    Viene stimata la finalità corretta e la matrice delle entità mostra sia il valore di origine sia il valore di destinazione nella proprietà **entità** corrispondente.
    

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Sarebbe stato possibile usare un'espressione regolare per ogni posizione?
Sì, creare l'entità di espressione regolare con i ruoli di origine e di destinazione e usarla in un modello.

Le posizioni in questo esempio, ad esempio `a-1234`, seguono un formato specifico di una o due lettere seguite da un trattino e quindi da una serie di 4 o 5 numeri. Questi dati possono essere descritti come un'entità espressione regolare con un ruolo per ogni posizione. I ruoli sono disponibili solo per i modelli. È possibile creare modelli basati su queste espressioni, quindi creare un'espressione regolare per il formato della posizione e aggiungerla ai modelli. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="hierarchical-entities-versus-roles"></a>Ruoli ed entità gerarchiche

Per ulteriori informazioni, vedere [Ruoli ed entità gerarchiche](luis-concept-roles.md#roles-versus-hierarchical-entities).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata creata una nuova finalità e sono state aggiunte espressioni di esempio per i dati acquisiti in base al contesto relativi alla posizione di origine e di destinazione. Una volta eseguiti il training e la pubblicazione dell'app, un'applicazione client può usare tali informazioni per creare un ticket di spostamento con le informazioni pertinenti.

> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità composta](luis-tutorial-composite-entity.md) 
