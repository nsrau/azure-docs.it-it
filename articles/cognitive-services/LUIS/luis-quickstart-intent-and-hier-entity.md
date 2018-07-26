---
title: Esercitazione per creare un'app di Language Understanding per ottenere dati di località - Azure | Microsoft Docs
description: Questa esercitazione illustra come creare una semplice app di Language Understanding usando finalità e un'entità gerarchica per estrarre dati.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/04/2018
ms.author: v-geberr
ms.openlocfilehash: babfc2f82e17f3745af1d940df89763170a002bd
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929587"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Tutorial: 5. Aggiungere un'entità gerarchica
In questa esercitazione si crea un'app che dimostra come trovare dati correlati in base al contesto. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità gerarchiche e gli elementi figlio contestualmente acquisiti 
> * Usare un'app LUIS nel dominio delle risorse umane (HR) 
> * Aggiungere un'entità gerarchica di località con elementi figlio di origine e destinazione
> * Eseguire il training e pubblicare l'app
> * Eseguire una query dell'endpoint dell'app per visualizzare una risposta JSON di Language Understanding, inclusi gli elementi figlio gerarchici 

Per questo articolo è necessario un account [LUIS](luis-reference-regions.md#luis-website) gratuito per creare un'applicazione LUIS personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha l'app relativa alle risorse umane dell'esercitazione sulle [entità elenco](luis-quickstart-intent-and-list-entity.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json).

Se si vuole mantenere l'app relativa alle risorse umane originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `hier`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="purpose-of-the-app-with-this-entity"></a>Scopo dell'app con questa entità
L'app determina dove spostare un dipendente, dalla posizione di origine (edificio e ufficio) alla posizione di destinazione (edificio e ufficio). Viene usata l'entità gerarchica per determinare le posizioni nell'espressione. 

L'entità gerarchica è una buona soluzione per questo tipo di dati, perché i due dati:

* Sono correlati tra loro nel contesto dell'espressione.
* Usano parole specifiche per indicare ogni posizione. Le parole usate sono, ad esempio, from/to, leaving/headed to, away from/toward.
* Le due località si trovano spesso nella stessa espressione. 

Lo scopo dell'entità **gerarchica** è trovare i dati correlati all'interno dell'espressione in base al contesto. Si consideri l'espressione seguente:

```JSON
mv Jill Jones from a-2349 to b-1298
```
Nell'espressione sono specificate due posizioni, `a-2349` e `b-1298`. Si supponga che la lettera corrisponda a un nome di edificio e il numero indichi l'ufficio all'interno di tale edificio. È logico che entrambi i valori siano raggruppati come figli di un'entità gerarchica, `Locations`, perché è necessario estrarre entrambi i dati dall'espressione e si tratta di valori correlati. 
 
Se è presente un solo elemento figlio (di origine o di destinazione) di un'entità gerarchica, verrà comunque estratto. Non è necessario individuare tutti gli elementi figlio per estrarne solo uno o alcuni. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Rimuovere l'entità numero predefinita dall'app
Per visualizzare l'intera espressione e contrassegnare gli elementi figlio gerarchici, è necessario rimuovere temporaneamente l'entità numero predefinita.

1. Assicurarsi che l'app relativa alle risorse umane sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** nella barra dei menu in alto a destra. 

    [ ![Screenshot dell'app LUIS con il pulsante relativo alla compilazione evidenziato in alto a destra sulla barra di spostamento](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png#lightbox)

2. Selezionare **Entità** nel menu a sinistra.

    [ ![Screenshot dell'app LUIS con il pulsante relativo alle entità evidenziato nel menu a sinistra](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png#lightbox)


3. Selezionare i puntini di sospensione (***...***) a destra dell'entità number nell'elenco. Selezionare **Elimina**. 

    [ ![Screenshot dell'app LUIS nella pagina di elenco delle entità, con il pulsante per l'eliminazione evidenziato per l'entità numero predefinita](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Aggiungere espressioni alla finalità MoveEmployee

1. Selezionare **Intents** (Finalità) dal menu a sinistra.

    [ ![Screenshot dell'app LUIS con la voce relativa alle finalità evidenziata nel menu a sinistra](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png#lightbox)

2. Selezionare **MoveEmployee** nell'elenco di finalità.

    [ ![Screenshot dell'app LUIS con la finalità MoveEmployee evidenziata nel menu a sinistra](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Aggiungere le espressioni di esempio seguenti:

    |Espressioni di esempio|
    |--|
    |Move John W. Smith **to** a-2345|
    |Direct Jill Jones **to** b-3499|
    |Organize the move of x23456 **from** hh-2345 **to** e-0234|
    |Begin paperwork to set x12345 **leaving** a-3459 **headed to** f-34567|
    |Displace 425-555-0000 **away from** g-2323 **toward** hh-2345|

    Nell'esercitazione sull'[entità elenco](luis-quickstart-intent-and-list-entity.md) un dipendente può essere designato per nome, indirizzo di posta elettronica, numero di interno, numero di telefono cellulare o codice fiscale federale degli Stati Uniti. Questi numeri dei dipendenti sono usati nelle espressioni. Le espressioni di esempio precedenti includono modi diversi per indicare le posizioni di origine e di destinazione, con formattazione in grassetto. Un paio di espressioni includono intenzionalmente solo le destinazioni. In questo modo, LUIS può comprendere come vengono inserite tali posizioni nell'espressione quando l'origine non è specificata.

    [ ![Screenshot di LUIS con nuove espressioni nella finalità MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)
     

## <a name="create-a-location-entity"></a>Creare un'entità posizione
LUIS deve comprendere le posizioni tramite etichette per l'origine e la destinazione nelle espressioni. Se è necessario visualizzare l'espressione nella vista token (non elaborata), selezionare l'interruttore **Entities View** (Vista entità) sulla barra sopra le espressioni. Dopo aver spostato l'interruttore, l'etichetta del controllo diventerà **Tokens View** (Vista token).

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

    [ ![Screenshot del pulsante relativo alle entità evidenziato nel riquadro di spostamento a sinistra](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png#lightbox)

2. Fare clic sul pulsante **Manage prebuilt entities** (Gestisci entità predefinite).

    [ ![Screenshot dell'elenco di entità con il pulsante per la gestione delle entità predefinite evidenziato](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Selezionare **number** nell'elenco di entità predefinite e quindi **Done** (Fine).

    ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non rileva le modifiche a finalità ed entità (il modello) finché non viene eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Train** (Esegui il training).

    ![Eseguire il training dell'app](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Training completato](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere una previsione di Language Understanding in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Publish** (Pubblica). 

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query sull'endpoint con un'espressione diversa
1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Schermata della pagina Publish (Pubblica) con l'URL endpoint evidenziato")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Passare alla fine dell'URL nella barra degli indirizzi e immettere `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `MoveEmployee` con l'entità gerarchica estratta.

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

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Sarebbe stato possibile usare un'espressione regolare per ogni posizione?
Sì, creare l'espressione regolare con i ruoli di origine e di destinazione e usarla in un modello.

Le posizioni in questo esempio, ad esempio `a-1234`, seguono un formato specifico di una o due lettere seguite da un trattino e quindi da una serie di 4 o 5 numeri. Questi dati possono essere descritti come un'entità espressione regolare con un ruolo per ogni posizione. I ruoli sono disponibili per i modelli. È possibile creare modelli basati su queste espressioni, quindi creare un'espressione regolare per il formato della posizione e aggiungerla ai modelli. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Con solo poche finalità e un'entità gerarchica, quest'app ha identificato una finalità di query in linguaggio naturale e ha restituito i dati estratti. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione principale, `MoveEmployee`, e le informazioni di località trovate nell'espressione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i dati dall'entità per completare il passaggio successivo. Language Understanding non esegue questa attività a livello di codice per il chatbot o l'applicazione chiamante, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A questo scopo, selezionare il pulsante con i puntini di sospensione (***...***) a destra del nome dell'app nell'elenco di app e quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità composta](luis-tutorial-composite-entity.md) 