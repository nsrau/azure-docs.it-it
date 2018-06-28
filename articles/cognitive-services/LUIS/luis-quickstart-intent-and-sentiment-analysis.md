---
title: Esercitazione per creare un'app di Language Understanding in grado di restituire un'analisi del sentiment - Azure | Microsoft Docs
description: Questa esercitazione offre informazioni su come aggiungere un'analisi del sentiment all'app di Language Understanding per analizzare espressioni di sentimenti positivi, negativi e neutri.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265335"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Esercitazione: Creare un'app in grado di restituire una valutazione assieme a una stima delle finalità
Questa esercitazione mostra come creare un'app che illustra come estrarre una valutazione positiva, negativa e neutra da espressioni.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità gerarchiche e gli elementi figlio contestualmente acquisiti 
> * Crea una nuova app di Language Understanding per un dominio di viaggio con finalità Bookflight
> * Aggiungere la finalità _Nessuno_ ed espressioni di esempio
> * Aggiungere un'entità di percorso gerarchica con elementi figlio di origine e destinazione
> * Eseguire il training e pubblicare l'app
> * Eseguire una query dell'endpoint dell'app per visualizzare una risposta JSON di Language Understanding, inclusi elementi figlio gerarchici 

Per questo articolo è necessario un account [LUIS][LUIS] gratuito per creare la propria applicazione.

## <a name="sentiment-analysis"></a>Analisi del sentiment
L'analisi del sentiment offre la possibilità di determinare se un'espressione utente è positiva, negativa o neutra. 

Le espressioni seguenti mostrano alcuni esempi di valutazione:

|Valutazione e score|Espressione|
|:--|--|
|positivo - 0,89 |L'associazione minestra e insalata era eccellente.|
|negativo - 0,07 |Non mi sono piaciuti gli aperitivi serviti durante la cena.|

L'analisi del sentiment è come un'impostazione dell'app che si applica a ogni espressione. Non è necessario trovare le parole che indicano una valutazione nelle espressioni ed etichettarle: sarà LUIS a farlo automaticamente.

## <a name="create-a-new-app"></a>Creare una nuova app
1. Accedere al sito Web di [Language Understanding][LUIS]. Accertarsi di accedere all'[area][LUIS-regions] in cui è necessario pubblicare gli endpoint di Language Understanding.

2. Sul sito Web di [Language Understanding][LUIS], selezionare **Creare nuova app**. 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Screenshot della pagina Elenchi di app")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. Nella finestra di dialogo **Crea nuova app**, denominare l'app `Restaurant Reservations With Sentiment` e selezionare **Operazione completata**. 

    ![Immagine della finestra di dialogo Crea nuova app](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Una volta completato il processo di creazione app, LUIS visualizza l'elenco di finalità contenente la finalità Nessuno.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Screenshot della pagina di elenchi delle finalità)")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Aggiungere un dominio predefinito
Aggiungere un dominio predefinito per aggiungere rapidamente finalità, entità ed espressioni con etichetta.

1. Selezionare **Prebuilt Domains** (Domini predefiniti) dal menu a sinistra.

    [ ![Screenshot del pulsante Prebuilt Domains (Domini predefiniti)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Selezionare **Aggiungi dominio** per il dominio predefinito **RestaurantReservation**. Attendere fino a quando il dominio non viene aggiunto.

    [ ![Screenshot dell'elenco Prebuilt Domains (Domini predefiniti)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. Selezionare **Intents** (Finalità) nel pannello di navigazione a sinistra. Questo dominio predefinito ha una sola finalità.

    [ ![Screenshot dell'elenco Prebuilt Domains (Domini predefiniti) con finalità evidenziate nel riquadro di navigazione a sinistra](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Selezionare la finalità **RestaurantReservation.Reserve**. 

    [ ![Screenshot dell'elenco Intents (Finalità) con la finalità RestaurantReservation.Reserve evidenziata](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Attivare/disattivare **Visualizza entità** per visualizzare le numerose finalità fornite con entità specifiche del dominio con etichetta.

    [ ![Screenshot della finalità RestaurantReservation.Reserve con Visualizza entità attivato sulla visualizzazione Token evidenziato](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non rileva le modifiche a finalità ed entità (il modello) finché non viene eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Addestra**.

    ![Screenshot del pulsante Addestra evidenziato](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Screenshot della barra di notifica di completamento del training ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurare un'app per includere l'analisi del sentiment
L'analisi del sentiment è attivata sulla pagina **Pubblica**. 

1. Selezionare **Pubblica** nella barra di spostamento in alto a destra.

    ![Screenshot della pagina Finalità con il pulsante Pubblica espanso ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Selezionare **Enable Sentiment Analysis** (Abilita analisi del sentiment).

    ![Screenshot della pagina Pubblica con Enable Sentiment Analysis (Abilita analisi del sentiment) evidenziato ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Pubblica**.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Screenshot della pagina Pubblica con il pulsante Publish to production slot (Pubblica su slot di produzione) evidenziato")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-an-utterance"></a>Eseguire la query dell'endpoint con un'espressione

1. Nella pagina **Pubblica**, selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. 

    !["Screenshot della pagina Pubblica con URL endpoint evidenziato](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Scorrere fino alla fine dell'URL nell'indirizzo e immettere `Reserve table for  10 on upper level away from kitchen`. L'ultimo parametro QueryString è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni con etichetta, per cui rappresenta un buon test e deve restituire la finalità `RestaurantReservation.Reserve` con analisi del sentiment estratta.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Che cosa ha permesso di ottenere questa app di Language Understanding?
Questa app, con analisi del sentiment attivata, ha individuato una finalità di query in linguaggio naturale e restituito i dati estratti, inclusa la valutazione generale, come score. 

Il chatbot dispone ora di informazioni sufficienti per determinare il passaggio successivo nella conversazione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Con questa richiesta viene eseguito Language Understanding. L'applicazione chiamante, ad esempio un chatbot, può rilevare il risultato topScoringIntent e i dati di valutazione dall'espressione per completare il passaggio successivo. Language Understanding non esegue questo lavoro programmatico per il chatbot o l'applicazione chiamante, ma determina solo l'intenzione dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A tale scopo, selezionare il menu con tre punti (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Elimina**. Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?), selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"] 
> [Chiamare l'API per endpoint LUIS con C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
