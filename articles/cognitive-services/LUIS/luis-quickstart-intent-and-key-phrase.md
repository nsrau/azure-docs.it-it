---
title: Esercitazione per creare un'app LUIS che restituisce frasi chiave - Azure | Microsoft Docs
description: Questa esercitazione offre informazioni su come aggiungere e restituire entità KeyPhrase all'app LUIS per analizzare domini chiave.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264616"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Esercitazione: creare un'app in grado di restituire dati di entità KeyPhrase presenti in espressioni
Questa esercitazione mostra come creare un'app che illustra come estrarre domini chiave da espressioni.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità KeyPhrase 
> * Creare una nuova app di Language Understanding per il dominio Risorse umane
> * Aggiungere la finalità _Nessuno_ ed espressioni di esempio
> * Aggiungere entità KeyPhrase per estrarre il contenuto da un'espressione
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding

Per questo articolo è possibile usare un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="keyphrase-entity-extraction"></a>Estrazione di entità KeyPhrase
Il dominio chiave viene fornito dall'entità predefinita **KeyPhrase**. Questa entità restituisce il dominio chiave nell'espressione

Le espressioni seguenti mostrano alcuni esempi di frasi chiave:

|Espressione|Valori di entità KeyPhrase|
|--|--|
|Per il prossimo anno è disponibile un nuovo piano sanitario a una franchigia inferiore?|"franchigia inferiore"<br>"nuovo piano sanitario"<br>"anno"|
|Il visual training è coperto dal piano sanitario con franchigia elevata?|"piano sanitario con franchigia elevata"<br>"visual training"|

Oltre a tutte le altre entità estratte, il chatbot può prendere in considerazione questi valori per decidere il passaggio successivo nella conversazione.

## <a name="download-sample-app"></a>Scaricare l'app di esempio
Scaricare l'app [Risorse umane](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) e salvarla in un file con estensione *.JSON. Questa app di esempio riconosce le espressioni rilevanti per benefit dei dipendenti, organigrammi e attività materiali.

## <a name="create-a-new-app"></a>Creare una nuova app
1. Accedere al sito Web di [Language Understanding][LUIS]. Accertarsi di accedere all'[area][LUIS-regions] in cui è necessario pubblicare gli endpoint di Language Understanding.

2. Sul sito Web di [Language Understanding][LUIS], selezionare **Import new app** (Importa nuova app) per importare l'app Risorse umane scaricata nella sezione precedente. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Screenshot della pagina Elenchi di app")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. Nella finestra di dialogo **Import new app** (Importa nuova app), denominare l'app `Human Resources with Key Phrase entity`. 

    ![Immagine della finestra di dialogo Crea nuova app](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Una volta completato il processo di creazione dell'app, Language Understanding visualizza l'elenco di finalità.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Screenshot della pagina di elenchi delle finalità")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Aggiungere entità KeyPhrase 
Aggiungere entità KeyPhrase predefinite per estrarre domini da espressioni.

1. Selezionare **Entità** nel menu a sinistra.

    [ ![Screenshot delle Entità evidenziate nella barra di spostamento a sinistra della sezione Genera](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Selezionare **Manage prebuilt entities** (Gestisci entità predefinite).

    [ ![Screenshot della finestra di dialogo popup Elenco di entità](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. Nella finestra di dialogo popup, selezionare **KeyPhrase**, quindi selezionare **Operazione completata**. 

    [ ![Screenshot della finestra di dialogo popup Elenco di entità](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non riconosce questa modifica al modello fino a quando non è stato eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Addestra**.

    ![Screenshot del pulsante Addestra evidenziato](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Screenshot della barra di notifica di completamento del training ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Pubblicare l'app su endpoint

1. Selezionare **Pubblica** nella barra di spostamento in alto a destra.

    ![Screenshot della pagina Entità con il pulsante Pubblica espanso ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Pubblica**.

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Screenshot della pagina Pubblica con il pulsante Publish to production slot (Pubblica su slot di produzione) evidenziato")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-an-utterance"></a>Eseguire la query dell'endpoint con un'espressione

1. Nella pagina **Pubblica**, selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    ![Screenshot della pagina Pubblica con l'URL endpoint evidenziato](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Scorrere alla fine dell'URL nell'indirizzo e immettere `Is there a new medical plan with a lower deductible offered next year?`. L'ultimo parametro QueryString è `q`, la **query** dell'espressione. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Che cosa ha permesso di ottenere questa app di Language Understanding?
Questa app, con il rilevamento di entità KeyPhrase, ha identificato una finalità di query in linguaggio naturale e restituito i dati estratti, tra cui il dominio principale. 

Il chatbot dispone ora di informazioni sufficienti per determinare il passaggio successivo nella conversazione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Con questa richiesta viene eseguito Language Understanding. L'applicazione chiamante, ad esempio un chatbot, può rilevare il risultato topScoringIntent e i dati KeyPhrase dall'espressione per completare il passaggio successivo. Language Understanding non esegue questo lavoro programmatico per il chatbot o l'applicazione chiamante, ma determina solo l'intenzione dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A tale scopo, selezionare il menu con tre punti (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Elimina**. Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?), selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app in grado di restituire una valutazione assieme a una stima delle finalità](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
