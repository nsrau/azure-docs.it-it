---
title: Esercitazione per creare un'app di Language Understanding per ottenere dati di località - Azure | Microsoft Docs
description: Questa esercitazione illustra come creare una semplice app di Language Understanding usando finalità e un'entità gerarchica per estrarre dati.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266499"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Esercitazione: Creare un'app che usa un'entità gerarchica
In questa esercitazione si crea un'app che dimostra come trovare dati correlati in base al contesto. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità gerarchiche e gli elementi figlio contestualmente acquisiti 
> * Creare una nuova app di Language Understanding per il settore dei viaggi con finalità Bookflight
> * Aggiungere la finalità _None_ (Nessuna) ed espressioni di esempio
> * Aggiungere un'entità gerarchica di località con elementi figlio di origine e destinazione
> * Eseguire il training e pubblicare l'app
> * Eseguire una query dell'endpoint dell'app per visualizzare una risposta JSON di Language Understanding, inclusi gli elementi figlio gerarchici 

Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="purpose-of-the-app-with-this-entity"></a>Scopo dell'app con questa entità
Questa app determina se un utente intende prenotare un volo. Usa l'entità gerarchica per determinare le località, la città di origine e la città di destinazione, all'interno del testo dell'utente. 

L'entità gerarchica è una buona soluzione per questo tipo di dati, perché i due dati:

* Sono in entrambi i casi località, generalmente espresse come città o codici aeroportuali.
* Hanno generalmente una scelta di termini specifici usati in tutto il mondo per determinare qual è l'origine e qual è la destinazione. Questi termini includono: a, diretto a, da, in partenza da.
* Le due località si trovano spesso nella stessa espressione. 

Lo scopo dell'entità **gerarchica** è trovare i dati correlati all'interno dell'espressione in base al contesto. Si consideri l'espressione seguente:

```JSON
1 ticket from Seattle to Cairo`
```

L'espressione specifica due località. Una è la città di origine, Seattle, l'altro è la città di destinazione, Il Cairo. Queste città sono entrambe importanti per la prenotazione di un volo. Anche se possono essere trovate usando entità semplici, sono in relazione tra loro e si trovano spesso nella stessa espressione. È pertanto opportuno che entrambe siano raggruppate come elementi figlio di un'entità gerarchica, ovvero **"Location"** (Posizione). 

In quanto entità di Machine Learning, l'app necessita di espressioni di esempio con le città di origine e di destinazione etichettate. Con questa operazione, Language Understanding apprende dove si trovano le entità nelle espressioni, la loro lunghezza e le parole che le circondano. 

## <a name="app-intents"></a>Finalità dell'app
Le finalità sono categorie di intenzioni dell'utente. Questa app ha due finalità: BookFlight e None (Nessuna). La finalità [None](luis-concept-intent.md#none-intent-is-fallback-for-app) (Nessuna) è utile per indicare qualsiasi elemento che non rientra nell'ambito dell'app.  

## <a name="hierarchical-entity-is-contextually-learned"></a>L'apprendimento dell'entità gerarchica è su base contestuale 
Lo scopo dell'entità è trovare e classificare le parti del testo nell'espressione. Un'entità [gerarchica](luis-concept-entity-types.md) è un'entità padre-figlio basata sul contesto d'uso. È possibile determinare le città di origine e di destinazione in un'espressione in base all'uso di `to` e `from`. Questi sono esempi di uso contestuale.  

Per questa applicazione di viaggio, Language Understanding estrae le località di origine e di destinazione per poter creare e compilare una prenotazione standard. Language Understanding consente varianti, abbreviazioni e gergo nelle espressioni. 

Semplici espressioni di esempio degli utenti includono:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

Le versioni abbreviate o gergali delle espressioni includono:

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
L'entità gerarchica associa le località di origine e destinazione. Se è presente un solo elemento figlio (di origine o di destinazione) di un'entità gerarchica, verrà comunque estratto. Non è necessario individuare tutti gli elementi figlio per estrarne solo uno o alcuni. 

## <a name="what-luis-does"></a>Operazioni di Language Understanding
Le operazioni di Language Understanding terminano quando la finalità e le entità dell'espressione sono state identificate, [estratte](luis-concept-data-extraction.md#list-entity-data) e restituite in formato JSON dall'[endpoint](https://aka.ms/luis-endpoint-apis). L'applicazione chiamante o il chatbot acquisisce la risposta JSON e soddisfa la richiesta nel modo previsto per l'app o il chatbot. 

## <a name="create-a-new-app"></a>Creare una nuova app
1. Accedere al sito Web di [Language Understanding][LUIS]. Accertarsi di accedere all'[area ][LUIS-regions] in cui è necessario pubblicare gli endpoint di Language Understanding.

2. Sul sito Web di [Language Understanding][LUIS] selezionare **Create new app** (Crea nuova app).  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Screenshot della pagina App lists (Elenchi di app)")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. Nella finestra di dialogo popup, immettere il nome `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "Schermata della finestra di dialogo popup Create new app (Crea nuova app)")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. Al termine del processo, l'app visualizza la pagina **Intents** (Finalità) con la finalità **None** (Nessuna). 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "Schermata dell'elenco di finalità con la sola finalità None (Nessuna)")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Creare una nuova finalità

1. Nella pagina **Intents** (Finalità) selezionare **Create new intent** (Crea nuova finalità). 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "Schermata dell'elenco delle finalità con il pulsante Create new intent (Crea nuova finalità) evidenziato")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Immettere il nome della nuova finalità `BookFlight`. È necessario selezionare questa finalità ogni volta che un utente vuole prenotare un volo.

    Creando una finalità, si crea la categoria principale di informazioni da identificare. Assegnando un nome alla categoria, qualsiasi altra applicazione che usi i risultati delle query di Language Understanding può usare il nome della categoria per trovare una risposta o intraprendere azioni appropriate. Language Understanding non risponde a queste domande, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "Schermata della finestra di dialogo popup Create new intent (Crea nuova finalità)")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Aggiungere alla finalità `BookFlight` diverse espressioni che verranno presumibilmente usate dagli utenti, ad esempio:

    | Espressioni di esempio|
    |--|
    |Prenotare 2 voli da Seattle a Il Cairo per lunedì prossimo|
    |Prenotare un biglietto per Londra per domani|
    |Prenotare 4 posti da Parigi a Londra per il 1° aprile|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "Schermata di immissione delle espressioni nella pagina della finalità BookFlight")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Aggiungere espressioni alla finalità None (Nessuna)

L'app di Language Understanding non ha attualmente espressioni per la finalità **None** (Nessuna). Sono necessarie espressioni alle quali l'app non dovrà rispondere, quindi occorre inserire espressioni nella finalità **None** (Nessuna). Non lasciarla vuota. 

1. Selezionare **Intents** (Finalità) dal pannello di sinistra. 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "Schermata della pagina della finalità BookFlight con il pulsante Intents (Finalità) evidenziato")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app:

    | Espressioni di esempio|
    |--|
    |Annullare.|
    |Arrivederci|
    |Che succede?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Quando l'espressione viene considerata associata alla finalità None (Nessuna)
Nell'applicazione chiamante di Language Understanding (ad esempio un chatbot), quando Language Understanding restituisce la finalità **None** (Nessuna) per un'espressione, il bot può chiedere se l'utente intende terminare la conversazione. Il bot può anche dare altre indicazioni per proseguire la conversazione, se l'utente non intende terminarla. 

Nella finalità **None** (Nessuna) vengono usate le entità. Se la finalità con il punteggio più alto è **None** (Nessuna), ma viene estratta un'entità significativa per il chatbot, il chatbot può proseguire con una domanda incentrata sulla finalità del cliente. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Creare un'entità di località dalla pagina Intent (Finalità)
Ora che le due finalità hanno espressioni, Language Understanding deve comprendere cos'è una località. Tornare alla finalità `BookFlight` ed etichettare (contrassegnare) il nome della città nell'espressione seguendo questa procedura:

1. Tornare alla finalità `BookFlight` selezionando **Intents** (Finalità) nel pannello di sinistra.

2. Selezionare `BookFlight` dall'elenco delle finalità.

3. Nell'espressione `Book 2 flights from Seattle to Cairo next Monday` selezionare la parola `Seattle`. Verrà visualizzato un menu a discesa con una casella di testo in alto per creare una nuova entità. Immettere il nome dell'entità `Location` nella casella di testo, quindi selezionare **Create new entity** (Crea nuova entità) nel menu a discesa. 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "Schermata della finalità BookFlight, creazione di una nuova entità dal testo selezionato")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. Nella finestra popup, selezionare il tipo di entità **Hierarchical** (Gerarchica) con `Origin` e `Destination` come entità figlio. Selezionare **Operazione completata**.

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Schermata della finestra di dialogo popup di creazione di entità per la nuova entità di località")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    L'etichetta per `Seattle` è contrassegnata come `Location` perché Language Understanding non sa se il termine era l'origine, la destinazione o nessuna delle due. Selezionare `Seattle`, quindi selezionare Location (Località) e infine selezionare `Origin` dal menu a destra.

5. Ora che l'entità è stata creata ed è stata etichettata un'espressione, etichettare le altre città selezionando il nome della città, quindi Location (Località) e infine selezionando `Origin` o `Destination` nel menu a destra.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Schermata dell'entità di Bookflight con il testo dell'espressione selezionato per la selezione delle entità")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox).

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non rileva le modifiche a finalità ed entità (il modello) finché non viene eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Train** (Esegui il training).

    ![Eseguire il training dell'app](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Training completato](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere una previsione di Language Understanding in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Publish** (Pubblica). 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Schermata della finalità BookFlight con il pulsante Publish (Pubblica) evidenziato")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query sull'endpoint con un'espressione diversa
1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Schermata della pagina Publish (Pubblica) con l'URL endpoint evidenziato")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Passare alla fine dell'URL nell'indirizzo e immettere `1 ticket to Portland on Friday`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `BookFlight` con l'entità gerarchica estratta.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Con solo due finalità e un'entità gerarchica, questa app ha identificato una finalità di query in linguaggio naturale e ha restituito i dati estratti. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione principale, `BookFlight`, e le informazioni di località trovate nell'espressione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i dati dall'entità per completare il passaggio successivo. Language Understanding non esegue questa attività a livello di codice per il chatbot o l'applicazione chiamante, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A tale scopo, selezionare il menu con i puntini di sospensione (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità elenco](luis-quickstart-intent-and-list-entity.md) 

Aggiungere l'**entità predefinita** [numero](luis-how-to-add-entities.md#add-prebuilt-entity) per estrarre il numero. 

Aggiungere l'[entità predefinita](luis-how-to-add-entities.md#add-prebuilt-entity) **datetimeV2** per estrarre le informazioni di data.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
