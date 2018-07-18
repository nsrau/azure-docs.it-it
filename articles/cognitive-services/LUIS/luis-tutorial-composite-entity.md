---
title: Creare un'entità composita per estrarre dati complessi - Azure | Microsoft Docs
description: Informazioni su come creare un'entità composita nell'app LUIS per estrarre tipi diversi di dati di entità.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264386"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Usare l'entità composita per estrarre dati complessi
Questa app semplice include due [finalità](luis-concept-intent.md) e diverse entità. La sua funzione è prenotare voli, ad esempio '1 biglietto da Seattle al Cairo venerdì' e restituire tutte le specifiche della prenotazione come unico dato. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
* Aggiungere entità predefinite datetimeV2 e number
* Creare un'entità composita
* Eseguire una query nell'app LUIS e ricevere i dati dell'entità composita

## <a name="before-you-begin"></a>Prima di iniziare
* App LUIS dall'**[avvio rapido gerarchico](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Se non si dispone di una sottoscrizione, è possibile registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>L'entità composita è un raggruppamento logico 
La funzione dell'entità è trovare e categorizzare parti del testo nell'espressione. Un'entità [composita](luis-concept-entity-types.md) è data da altri tipi di entità appresi dal contesto. Per questa app di viaggi che accetta prenotazioni dei voli, sono disponibili informazioni quali date, posizioni e numero di posti. 

Prima che venga creata un'entità composita le informazioni sono entità separate. Creare un'entità composita quando le entità separate possono essere raggruppate logicamente e questo raggruppamento logico è utile per il chatbot o per altre applicazioni che usano LUIS. 

Semplici espressioni di esempio degli utenti includono:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
L'entità composita corrisponde al numero di posti, alla località di partenza, alla località di destinazione e alla data. 

## <a name="what-luis-does"></a>Funzioni di LUIS
Dopo che la finalità e le entità dell'espressione sono state identificate, [estratte](luis-concept-data-extraction.md#list-entity-data) e restituite in JSON dall'[endpoint](https://aka.ms/luis-endpoint-apis), LUIS ha assolto alla sua funzione. L'applicazione chiamante o il chatbot acquisisce la risposta JSON e soddisfa la richiesta nel modo previsto a seconda dell'app o del chatbot. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Aggiungere entità predefinite number e datetimeV2
1. Selezionare l'app `MyTravelApp` dall'elenco di app nel sito Web [LUIS][LUIS].

2. Quando si apre l'app, selezionare il collegamento di spostamento sinistro **Entities** (Entità).

    ![Selezionare il pulsante entities (entità)](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Selezionare **Manage prebuilt entities** (Gestisci entità predefinite).

    ![Selezionare il pulsante entities (entità)](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. Nella casella popup selezionare **number** e **datetimeV2**.

    ![Selezionare il pulsante entities (entità)](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Per poter estrarre le nuove entità, selezionare **Train** (Esegui il training) nella barra di spostamento superiore.

    ![Selezionare il pulsante Train (Esegui il training)](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Usare la finalità esistente per creare un'entità composita
1. Selezionare **Intents** (Finalità) nel pannello di spostamento sinistro. 

    ![Selezionare la pagina Intents (Finalità)](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Selezionare `BookFlight` dall'elenco **Intents** (Finalità).  

    ![Selezionare la finalità BookFlight dall'elenco](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Le entità predefinite number e datetimeV2 vengono etichettate nelle espressioni.

3. Per l'espressione `book 2 flights from seattle to cairo next monday`, selezionare l'entità `number` blu, quindi selezionare **Wrap in composite entity** (Esegui il wrapping nell'entità composita) dall'elenco. Una linea verde, sotto le parole, segue il cursore mentre si sposta a destra, indicando un'entità composita. Spostarsi a destra per selezionare l'ultima entità predefinita `datetimeV2`, quindi immettere `FlightReservation` nella finestra popup, infine selezionare **Create new composite** (Crea nuova entità composita). 

    ![Creare un'entità composita nella pagina intents (finalità)](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Verrà visualizzata una finestra di dialogo popup in cui verificare gli elementi figlio dell'entità composita. Selezionare **Operazione completata**.

    ![Creare un'entità composita nella pagina intents (finalità)](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Eseguire il wrapping delle entità nell'entità composita
Dopo aver creato l'entità composita, etichettare le espressioni rimanenti nell'entità composita. Per eseguire il wrapping di una frase come entità composita, è necessario selezionare la parola più a sinistra, selezionare **Wrap in composite entity** (Esegui il wrapping nell'entità composita), selezionare la parola più a destra e infine selezionare l'entità composita nominata `FlightReservation`. Si tratta di una sequenza rapida di selezioni, suddivisa nei passaggi seguenti:

1. Nell'espressione `schedule 4 seats from paris to london for april 1` selezionare il 4 come entità predefinita number.

    ![Selezionare la parola più a sinistra](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. Selezionare **Wrap in composite entity** (Esegui il wrapping nell'entità composita) dall'elenco che viene visualizzato.

    ![Selezionare wrap (Esegui il wrapping) dall'elenco](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Selezionare la parola più a destra. La linea verde che compare sotto la frase indica un'entità composita.

    ![Selezionare la parola più a destra](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. Selezionare il nome composito `FlightReservation` dall'elenco che viene visualizzato.

    ![Selezionare l'entità composita nominata](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Per l'ultima espressione, eseguire il wrapping di `London` e `tomorrow` nell'entità composita, usando le stesse istruzioni. 

## <a name="train-the-luis-app"></a>Eseguire il training dell'app LUIS
LUIS non apprende le finalità e le entità (il modello) finché non viene sottoposta a training. 

1. Nella parte superiore destra del sito Web LUIS selezionare il pulsante **Train** (Esegui il training).

    ![Eseguire il training dell'app](./media/luis-tutorial-composite-entity/train-button.png)

2. Il training è completato quando nella barra di stato verde nella parte superiore del sito Web viene confermato il completamento.

    ![Training completato](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere una stima LUIS in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web LUIS selezionare il pulsante **Publish** (Pubblica). 

2. Selezionare lo slot di produzione e il pulsante **Publish** (Pubblica).

    ![pubblicare l'app](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. La pubblicazione è completata quando la barra di stato verde nella parte superiore del sito Web conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query nell'endpoint con un'espressione diversa
1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    ![Selezionare l'URL endpoint](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Andare alla fine dell'URL nell'indirizzo e immettere `reserve 3 seats from London to Cairo on Sunday`. L'ultimo parametro querystring è `q`, la query dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, pertanto rappresenta un buon test e dovrebbe restituire la finalità `BookFlight` con l'entità gerarchica estratta.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Questa espressione restituisce una matrice di entità composite incluso l'oggetto **flightreservation** con i dati estratti.  

## <a name="what-has-this-luis-app-accomplished"></a>A quali funzioni ha assolto questa app LUIS?
Questa app, con solo due finalità e un'entità composita, ha identificato un'intenzione di query in linguaggio naturale e ha restituito i dati estratti. 

Il chatbot dispone ora di informazioni sufficienti per determinare l'azione principale, `BookFlight`, e le informazioni sulla prenotazione trovate nell'espressione. 

## <a name="where-is-this-luis-data-used"></a>Dove vengono usati questi dati LUIS? 
LUIS ha terminato con questa richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i dati dell'entità per completare il passaggio successivo. LUIS non esegue questo lavoro programmatico per il bot o l'applicazione chiamante, ma si limita a determinare l'intenzione dell'utente. 

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sulle entità](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
