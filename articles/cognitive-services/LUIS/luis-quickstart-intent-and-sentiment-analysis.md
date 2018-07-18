---
title: Esercitazione per creare un'app di Language Understanding in grado di restituire un'analisi del sentiment - Azure | Microsoft Docs
description: Questa esercitazione offre informazioni su come aggiungere un'analisi del sentiment all'app di Language Understanding per analizzare espressioni di sentimenti positivi, negativi e neutri.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: v-geberr
ms.openlocfilehash: 1a48810287c1639910db8e39af2da61d836b2988
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340934"
---
# <a name="tutorial-8--add-sentiment-analysis"></a>Esercitazione: 8.  Aggiungere l'analisi del sentiment
Questa esercitazione mostra come creare un'app che illustra come estrarre una valutazione positiva, negativa e neutra da espressioni.

<!-- green checkmark -->
> [!div class="checklist"]
> * Informazioni sull'analisi del sentiment
> * Usare un'app LUIS nel dominio delle risorse umane (HR) 
> * Aggiungere l'analisi del sentiment
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding 

Per questo articolo è necessario un account [LUIS](luis-reference-regions.md#luis-website) gratuito per creare un'applicazione LUIS personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Se non disponi dell'app relativa alle risorse umane dell'esercitazione sull'[entità KeyPhrase predefinita](luis-quickstart-intent-and-key-phrase.md), [importa](create-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json).

Se si vuole mantenere l'app relativa alle risorse umane originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `sentiment`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="sentiment-analysis"></a>Analisi del sentiment
L'analisi del sentiment offre la possibilità di determinare se un'espressione utente è positiva, negativa o neutra. 

Le espressioni seguenti mostrano alcuni esempi di valutazione:

|Valutazione|Score|Espressione|
|:--|:--|:--|
|positivo|0,91 |John W. Smith did a great job on the presentation in Paris.|
|positivo|0,84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch.|

L'analisi del sentiment è un'impostazione dell'app che si applica a ogni espressione. Non è necessario trovare le parole che indicano il sentiment nell'espressione ed etichettarle, perché l'analisi del sentiment si applica all'intera espressione. 

## <a name="add-employeefeedback-intent"></a>Aggiungere la finalità EmployeeFeedback 
Aggiungere una nuova finalità per acquisire il feedback dei dipendenti dai membri dell'azienda. 

1. Assicurarsi che l'app relativa alle risorse umane sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** nella barra dei menu in alto a destra. 

    [ ![Schermata dell'app di Language Understanding con Build evidenziato nella barra dei menu in alto a destra](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Selezionare **Create new intent** (Crea nuova finalità).

    [ ![Screenshot dell'app LUIS con il pulsante relativo alla compilazione evidenziato in alto a destra sulla barra di spostamento](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. Immettere il nome della nuova finalità `EmployeeFeedback`.

    ![Finestra di dialogo per la creazione di una nuova finalità denominata EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Aggiungere diverse espressioni che indicano un dipendente che fa qualcosa di positivo o un'area che richiede un miglioramento:

    Tenere presente in questa app relativa alle risorse umane i dipendenti sono definiti nell'entità elenco, `Employee`, tramite il nome, l'indirizzo di posta elettronica, il numero di interno, il numero di telefono cellulare e il codice fiscale federale degli Stati Uniti. 

    |Espressioni|
    |--|
    |425-555-1212 did a nice job of welcoming back a co-worker from maternity leave|
    |234-56-7891 did a great job of comforting a co-worker in their time of grief.|
    |jill-jones@mycompany.com didn't have all the required invoices for the paperwork.|
    |john.w.smith@mycompany.com turned in the required forms a month late with no signatures|
    |x23456 didn't make it to the important marketing off-site meeting.|
    |x12345 missed the meeting for June reviews.|
    |Jill Jones rocked the sales pitch at Harvard|
    |John W. Smith did a great job on the presentation at Stanford|

    [ ![Screenshot dell'app LUIS con espressioni di esempio nella finalità EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
LUIS non riconosce la nuova finalità e le espressioni di esempio fino a quando non viene eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Train** (Esegui il training).

    ![Screenshot del pulsante Addestra evidenziato](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Screenshot della barra di notifica di completamento del training ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurare un'app per includere l'analisi del sentiment
Configurare l'analisi del sentiment nella pagina **Publish** (Pubblica). 

1. Selezionare **Pubblica** nella barra di spostamento in alto a destra.

    ![Screenshot della pagina Finalità con il pulsante Pubblica espanso ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Selezionare **Enable Sentiment Analysis** (Abilita analisi del sentiment). Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-an-utterance"></a>Eseguire la query dell'endpoint con un'espressione

1. Nella pagina **Pubblica**, selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    !["Screenshot della pagina Pubblica con URL endpoint evidenziato](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

2. Scorrere fino alla fine dell'URL nell'indirizzo e immettere `Jill Jones work with the media team on the public portal was amazing`. L'ultimo parametro QueryString è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni con etichetta, per cui rappresenta un buon test e deve restituire la finalità `EmployeeFeedback` con analisi del sentiment estratta.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

Il valore di sentimentAnalysis è positivo con un punteggio pari a 0,86. 

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Questa app, con analisi del sentiment attivata, ha individuato una finalità di query in linguaggio naturale e restituito i dati estratti, inclusa la valutazione generale, come score. 

Il chatbot dispone ora di informazioni sufficienti per determinare il passaggio successivo nella conversazione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Con questa richiesta viene eseguito Language Understanding. L'applicazione chiamante, ad esempio un chatbot, può rilevare il risultato topScoringIntent e i dati di valutazione dall'espressione per completare il passaggio successivo. Language Understanding non esegue questo lavoro programmatico per il chatbot o l'applicazione chiamante, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. Seleziona **App personali** nel menu in alto a sinistra. Seleziona il menu con i puntini di sospensione (...) a destra del nome dell'app nell'elenco di app, quindi seleziona **Elimina**. Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Chiamare l'API per endpoint LUIS con C#](luis-get-started-cs-get-intent.md) 

