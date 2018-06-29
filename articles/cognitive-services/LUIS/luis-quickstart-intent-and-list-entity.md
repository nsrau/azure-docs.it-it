---
title: "Esercitazione: Creare un'app di Language Understanding per ottenere un elenco di dati di corrispondenza di testo esatta - Azure | Microsoft Docs"
description: Questa esercitazione illustra come creare una semplice app di Language Understanding usando finalità ed entità elenco per estrarre i dati in questa guida introduttiva.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264828"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Esercitazione: Creare un'app usando un'entità elenco
In questa esercitazione si crea un'app che dimostra come ottenere dati corrispondenti a un elenco predefinito. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Informazioni sulle entità elenco 
> * Creare una nuova app di Language Understanding per il settore delle bevande con la finalità OrderDrinks
> * Aggiungere la finalità _None_ (Nessuna) ed espressioni di esempio
> * Aggiungere l'entità elenco per estrarre bevande dalle espressioni
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere la risposta JSON di Language Understanding

Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="purpose-of-the-list-entity"></a>Scopo dell'entità elenco
Questa app acquisisce gli ordini di bevande come `1 coke and 1 milk please` e restituisce dati come il tipo di bevanda. Un'entità **elenco** di bevande cerca le corrispondenze di testo esatte e le restituisce. 

Un'entità elenco è una buona scelta per questo tipo di dati, quando i valori rappresentano un set noto. I nomi delle bevande possono variare e comprendere gergo e abbreviazioni, ma non cambiano frequentemente. 

## <a name="app-intents"></a>Finalità dell'app
Le finalità sono categorie di intenzioni dell'utente. Questa app ha due finalità: OrderDrink e None (Nessuna). La finalità [None](luis-concept-intent.md#none-intent-is-fallback-for-app) (Nessuna) è utile per indicare qualsiasi elemento che non rientra nell'ambito dell'app.  

## <a name="list-entity-is-an-exact-text-match"></a>L'entità elenco è una corrispondenza di testo esatta
Lo scopo dell'entità è trovare e classificare le parti del testo nell'espressione. Un'entità [elenco](luis-concept-entity-types.md) prevede un'esatta corrispondenza di parole o frasi.  

Per questa app per bevande, Language Understanding estrae l'ordine delle bevande in modo tale da poter creare ed evadere un ordine standard. Language Understanding consente varianti, abbreviazioni e gergo nelle espressioni. 

Semplici espressioni di esempio degli utenti includono:

```
2 glasses of milk
3 bottles of water
2 cokes
```

Le versioni abbreviate o gergali delle espressioni includono:

```
5 milk
3 h2o
1 pop
```
 
L'entità elenco associa `h2o` a water (acqua) e `pop` a soft drink (bevanda analcolica).  

## <a name="what-luis-does"></a>Operazioni di Language Understanding
Le operazioni di Language Understanding terminano quando la finalità e le entità dell'espressione sono state identificate, [estratte](luis-concept-data-extraction.md#list-entity-data) e restituite in formato JSON dall'[endpoint](https://aka.ms/luis-endpoint-apis). L'applicazione chiamante o il chatbot acquisisce la risposta JSON e soddisfa la richiesta nel modo previsto per l'app o il chatbot. 

## <a name="create-a-new-app"></a>Creare una nuova app
1. Accedere al sito Web di [Language Understanding][LUIS]. Accertarsi di accedere all'[area ][LUIS-regions] in cui è necessario pubblicare gli endpoint di Language Understanding.

2. Sul sito Web di [Language Understanding][LUIS] selezionare **Create new app** (Crea nuova app).  

    ![Creare nuova app](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. Nella finestra di dialogo popup, immettere il nome `MyDrinklist`. 

    ![Assegnare all'app il nome MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. Al termine del processo, l'app visualizza la pagina **Intents** (Finalità) con la finalità **None** (Nessuna). 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Schermata della pagina Intents (Finalità)")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Creare una nuova finalità

1. Nella pagina **Intents** (Finalità) selezionare **Create new intent** (Crea nuova finalità). 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Schermata della pagina Intents (Finalità) con il pulsante Create new intent (Crea nuova finalità) evidenziato")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Immettere il nome della nuova finalità `OrderDrinks`. È necessario selezionare questa finalità ogni volta che un utente vuole ordinare una bevanda.

    Creando una finalità, si crea la categoria principale di informazioni da identificare. Assegnando un nome alla categoria, qualsiasi altra applicazione che usi i risultati delle query di Language Understanding può usare il nome della categoria per trovare una risposta o intraprendere azioni appropriate. Language Understanding non risponde a queste domande, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Schermata di creazione della nuova finalità OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Aggiungere alla finalità `OrderDrinks` diverse espressioni che verranno presumibilmente usate dagli utenti, ad esempio:

    | Espressioni di esempio|
    |--|
    |Vorrei 2 Coca Cola e una bottiglia d'acqua in camera|
    |2 Perrier al lime|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Schermata di inserimento delle espressioni nella pagina della finalità OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Aggiungere espressioni alla finalità None (Nessuna)

L'app di Language Understanding non ha attualmente espressioni per la finalità **None** (Nessuna). Sono necessarie espressioni alle quali l'app non dovrà rispondere, quindi occorre inserire espressioni nella finalità **None** (Nessuna). Non lasciarla vuota. 

1. Selezionare **Intents** (Finalità) dal pannello di sinistra. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Schermata di selezione del collegamento Intents (Finalità) dal pannello di sinistra")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app:

    | Espressioni di esempio|
    |--|
    |Annullare.|
    |Arrivederci|
    |Che succede?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Quando l'espressione viene considerata associata alla finalità None (Nessuna)
Nell'applicazione chiamante di Language Understanding (ad esempio un chatbot), quando Language Understanding restituisce la finalità **None** (Nessuna) per un'espressione, il bot può chiedere se l'utente intende terminare la conversazione. Il bot può anche dare altre indicazioni per proseguire la conversazione, se l'utente non intende terminarla. 

Nella finalità **None** (Nessuna) vengono usate le entità. Se la finalità con il punteggio più alto è **None** (Nessuna), ma viene estratta un'entità significativa per il chatbot, il chatbot può proseguire con una domanda incentrata sulla finalità del cliente. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Creare un'entità di menu dalla pagina Intent (Finalità)
Ora che le due finalità hanno espressioni, Language Understanding deve comprendere cos'è una bevanda. Tornare alla finalità `OrderDrinks` ed etichettare (contrassegnare) le bevande nell'espressione seguendo questa procedura:

1. Tornare alla finalità `OrderDrinks` selezionando **Intents** (Finalità) nel pannello di sinistra.

2. Selezionare `OrderDrinks` dall'elenco delle finalità.

3. Nell'espressione `Please send 2 cokes and a bottle of water to my room` selezionare la parola `water`. Verrà visualizzato un menu a discesa con una casella di testo in alto per creare una nuova entità. Immettere il nome dell'entità `Drink` nella casella di testo, quindi selezionare **Create new entity** (Crea nuova entità) nel menu a discesa. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Schermata di creazione della nuova entità tramite la selezione di una parola nell'espressione")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. Nella finestra popup selezionare il tipo di entità **List** (Elenco). Aggiungere il sinonimo `h20`. Premere il tasto INVIO dopo ogni sinonimo. Non aggiungere `perrier` all'elenco dei sinonimi, perché verrà aggiunto nel passaggio successivo come esempio. Selezionare **Operazione completata**.

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Schermata di configurazione della nuova entità")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Ora che l'entità è stata creata, etichettare gli altri sinonimi di water (acqua) selezionandoli, quindi selezionare `Drink` nell'elenco a discesa. Seguire il menu a destra, quindi selezionare `Set as synonym` e infine `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Schermata dell'etichettatura dell'espressione con l'entità esistente")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Modificare l'entità elenco dalla pagina delle entità
L'entità dell'elenco delle bevande è stata creata ma non ha molti elementi e sinonimi. Se si conoscono alcuni dei termini, delle abbreviazioni e del gergo, è più rapido compilare l'elenco nella pagina **Entity** (Entità). 

1. Selezionare **Entities** (Entità) dal pannello di sinistra.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Schermata di selezione delle entità dal pannello di sinistra")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Selezionare `Drink` dall'elenco di entità.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Schermata di selezione dell'entità Drink dall'elenco di entità")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. Nella casella di testo immettere `Soda pop`, quindi premere INVIO. Si tratta di un termine ampiamente usato per le bevande gassate. Ogni cultura ha un termine gergale o alternativo per questo tipo di bevanda.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Schermata di immissione del nome canonico")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. Nella stessa riga di `Soda pop` immettere i sinonimi, ad esempio: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    I sinonimi possono includere frasi, punteggiatura, possessivi e plurali. Poiché l'entità elenco è una corrispondenza di testo esatta (tranne che per le maiuscole/minuscole), i sinonimi devono avere tutte le varianti. È possibile espandere l'elenco man mano che si apprendono altre varianti dai log delle query o dall'esame dei risultati degli endpoint. 

    Questo articolo presenta solo alcuni sinonimi, per mantenere l'esempio sintetico. Un'app di Language Understanding a livello di produzione avrà molti sinonimi e verrà esaminata ed espansa periodicamente. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Schermata di aggiunta dei sinonimi")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non rileva le modifiche a finalità ed entità (il modello) finché non viene eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Train** (Esegui il training).

    ![Eseguire il training dell'app](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Training completato](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere una previsione di Language Understanding in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Publish** (Pubblica). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Schermata di selezione del pulsante Publish (Pubblica)")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Schermata di selezione del pulsante Publish to production slot (Pubblica in slot di produzione)")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query sull'endpoint con un'espressione diversa
1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Schermata dell'URL endpoint nella pagina Publish (Pubblica)")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Passare alla fine dell'URL nell'indirizzo e immettere `2 cokes and 3 waters`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `OrderDrinks` con i due tipi di bevanda `cokes` e `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Dov'è l'elaborazione del linguaggio naturale nell'entità elenco? 
L'entità elenco è una corrispondenza di testo esatta, quindi non si basa sull'elaborazione del linguaggio naturale o su Machine Learning. Language Understanding usa l'elaborazione del linguaggio naturale (o Machine Learning) per selezionare la finalità corretta con il punteggio più alto. Un'espressione può essere un insieme di più entità o anche di più tipi di entità. Ogni espressione viene elaborata per tutte le entità nell'app, incluse le entità di elaborazione del linguaggio naturale (o Machine Learning) come l'entità **Simple**.

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Con solo due finalità e un'entità elenco, questa app ha identificato una finalità di query in linguaggio naturale e ha restituito i dati estratti. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione primaria, `OrderDrinks`, e quali tipi di bevande sono stati ordinati all'entità elenco Drink. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i dati dall'entità per completare il passaggio successivo. Language Understanding non esegue questa attività a livello di codice per il chatbot o l'applicazione chiamante, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A tale scopo, selezionare il menu con i puntini di sospensione (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come aggiungere un'entità di espressione regolare](luis-quickstart-intents-regex-entity.md)

Aggiungere l'**entità predefinita** [numero](luis-how-to-add-entities.md#add-prebuilt-entity) per estrarre il numero. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
