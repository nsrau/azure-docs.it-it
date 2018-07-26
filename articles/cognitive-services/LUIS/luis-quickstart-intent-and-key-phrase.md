---
title: Esercitazione per creare un'app LUIS che restituisce frasi chiave - Azure | Microsoft Docs
description: Questa esercitazione offre informazioni su come aggiungere e restituire entità KeyPhrase all'app LUIS per analizzare domini chiave.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: ccefb4c2890d74978f340778cfab7cad979c9802
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929552"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Esercitazione: 8. Aggiungere entità KeyPhrase 
In questa esercitazione viene usata un'app che illustra come estrarre domini chiave da espressioni.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità KeyPhrase 
> * Usare un'app LUIS nel dominio delle risorse umane (HR) 
> * Aggiungere entità KeyPhrase per estrarre il contenuto da un'espressione
> * Eseguire il training e pubblicare l'app
> * Eseguire una query sull'endpoint dell'app per visualizzare una risposta JSON LUIS che include frasi chiave

Per questo articolo è possibile usare l'account [LUIS](luis-reference-regions.md#publishing-regions) gratuito per creare un'applicazione LUIS personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha l'app relativa alle risorse umane dell'esercitazione sull'[entità semplice](luis-quickstart-primary-and-secondary-data.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json).

Se si vuole mantenere l'app relativa alle risorse umane originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `keyphrase`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

## <a name="keyphrase-entity-extraction"></a>Estrazione di entità KeyPhrase
Il dominio chiave viene fornito dall'entità predefinita, **KeyPhrase**. Questa entità restituisce il dominio chiave nell'espressione.

Le espressioni seguenti mostrano alcuni esempi di frasi chiave:

|Espressione|Valori di entità KeyPhrase|
|--|--|
|Per il prossimo anno è disponibile un nuovo piano sanitario a una franchigia inferiore?|"franchigia inferiore"<br>"nuovo piano sanitario"<br>"anno"|
|Il visual training è coperto dal piano sanitario con franchigia elevata?|"piano sanitario con franchigia elevata"<br>"visual training"|

L'applicazione client può usare questi valori, insieme ad altre entità estratte, per stabilire il passaggio successivo nella conversazione.

## <a name="add-keyphrase-entity"></a>Aggiungere entità KeyPhrase 
Aggiungere entità KeyPhrase predefinite per estrarre domini da espressioni.

1. Assicurarsi che l'app relativa alle risorse umane sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** nella barra dei menu in alto a destra. 

    [ ![Screenshot dell'app LUIS con il pulsante relativo alla compilazione evidenziato in alto a destra sulla barra di spostamento](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. Selezionare **Entità** nel menu a sinistra.

    [ ![Screenshot delle Entità evidenziate nella barra di spostamento a sinistra della sezione Genera](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Selezionare **Manage prebuilt entities** (Gestisci entità predefinite).

    [ ![Screenshot della finestra di dialogo popup Elenco di entità](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. Nella finestra di dialogo popup, selezionare **KeyPhrase**, quindi selezionare **Operazione completata**. 

    [ ![Screenshot della finestra di dialogo popup Elenco di entità](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. Scegliere **Intents** (Finalità) dal menu a sinistra e quindi scegliere la finalità **Utilities.Confirm**. L'entità KeyPhrase è etichettata in diverse espressioni. 

    [ ![Screenshot della finalità Utilities.Confirm con l'entità KeyPhrase etichettata nelle espressioni](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
È necessario eseguire il training della nuova versione `keyphrase` dell'app.  

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Train** (Esegui il training).

    ![Eseguire il training dell'app](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Training completato](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Pubblicare l'app su endpoint

1. Selezionare **Pubblica** nella barra di spostamento in alto a destra.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-an-utterance"></a>Eseguire la query dell'endpoint con un'espressione

1. Nella pagina **Pubblica**, selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    ![Screenshot della pagina Pubblica con l'URL endpoint evidenziato](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Scorrere alla fine dell'URL nell'indirizzo e immettere `does form hrf-123456 cover the new dental benefits and medical plan`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

Durante la ricerca per di un modulo, l'utente ha fornito più informazioni del necessario per trovare il modulo. Le informazioni aggiuntive vengono restituite come **builtin.keyPhrase**. L'applicazione client può usare queste informazioni aggiuntive per una domanda di completamento, ad esempio per chiedere se si desidera parlare con un rappresentate del reparto risorse umane in merito ai nuovi vantaggi dentistici, oppure per fornire un menu con altre opzioni, tra cui una relativa ad altre informazioni sui vantaggi dentistici o su un piano medico.

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Questa app, con il rilevamento di entità KeyPhrase, ha identificato una finalità di query in linguaggio naturale e restituito i dati estratti, tra cui il dominio principale. 

Il chatbot dispone ora di informazioni sufficienti per determinare il passaggio successivo nella conversazione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Con questa richiesta viene eseguito Language Understanding. L'applicazione chiamante, ad esempio un chatbot, può rilevare il risultato topScoringIntent e i dati KeyPhrase dall'espressione per completare il passaggio successivo. Language Understanding non esegue questo lavoro programmatico per il chatbot o l'applicazione chiamante, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. Seleziona **App personali** nel menu in alto a sinistra. Selezionare il pulsante con i puntini di sospensione (***...***) a destra del nome dell'app nell'elenco di app e quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere l'analisi del sentiment all'app](luis-quickstart-intent-and-sentiment-analysis.md)