---
title: Esercitazione su come chiamare un'app di Language Understanding (LUIS) tramite C# | Microsoft Docs
description: Questa esercitazione illustra come chiamare un'app di Language Understanding con C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 0416d19d27810a2ab8eeb20e16b2f921fc7826ee
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263490"
---
# <a name="tutorial-call-a-luis-endpoint-using-c"></a>Esercitazione: Chiamare un endpoint di Language Understanding tramite C#

Passare espressioni a un endpoint di Language Understanding per ottenere finalità ed entità.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare una sottoscrizione di Language Understanding e copiare il valore chiave per un uso successivo
> * Visualizzare i risultati dell'endpoint di Language Understanding dal browser all'app pubblica IoT di esempio
> * Creare un'app console in Visual Studio C# per effettuare una chiamata HTTPS all'endpoint di Language Understanding

<!-- link to free account --> Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="create-luis-subscription-key"></a>Creare una chiave di sottoscrizione di Language Understanding
1. Creare prima di tutto un [account API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nel portale di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

2. Accedere al portale di Azure all'indirizzo https://portal.azure.com. 

3. Seguire i passaggi descritti in [Creating Subscription Keys using Azure](./luis-how-to-azure-subscription.md) (Creazione di chiavi di sottoscrizione tramite Azure) per ottenere una chiave.

4. Tornare al sito Web di [Language Understanding](luis-reference-regions.md). Accedere usando l'account Azure. 

    [![](media/luis-get-started-cs-get-intent/app-list.png "Screenshot dell'elenco di app")](media/luis-get-started-cs-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Informazioni sui risultati restituiti da Language Understanding

Per comprendere i risultati restituiti da Language Understanding, è possibile incollare l'URL di un'app di Language Understanding di esempio in una finestra del browser. L'app di esempio è un'app IoT che rileva se l'utente vuole accendere o spegnere le luci.

1. L'endpoint dell'app di esempio ha il formato seguente: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Copiare l'URL e sostituire la chiave di sottoscrizione per il valore del campo `subscription-key`.
2. Incollare l'URL in una finestra del browser e premere INVIO. Il browser visualizzerà un risultato JSON che indica che Language Understanding rileva la finalità `HomeAutomation.TurnOn` e l'entità `HomeAutomation.Room` con il valore `bedroom`.

    ![Il risultato JSON rileva la finalità TurnOn](./media/luis-get-started-cs-get-intent/turn-on-bedroom.png)
3. Sostituire il valore del parametro `q=` nell'URL con `turn off the living room light` e premere INVIO. Il risultato indica adesso che Language Understanding ha rilevato la finalità `HomeAutomation.TurnOff` e l'entità `HomeAutomation.Room` con valore `living room`. 

    ![Il risultato JSON rileva la finalità TurnOff](./media/luis-get-started-cs-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-c"></a>Utilizzare un risultato di Language Understanding tramite l'API dell'endpoint con C# 

È possibile usare C# per accedere agli stessi risultati visualizzati nella finestra del browser nel passaggio precedente. 

1. Creare una nuova applicazione console in Visual Studio. Copiare il codice seguente e salvarlo in un file *.cs:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/csharp/Program.cs)]
1. Sostituire il valore della variabile `subscriptionKey` con la chiave di sottoscrizione di Language Understanding.

3. Nel progetto di Visual Studio aggiungere un riferimento a **System.Web**.

4. Eseguire l'applicazione console. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

![La finestra della console mostra i risultati JSON da Language Understanding](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Le due risorse create in questa esercitazione sono la chiave di sottoscrizione di Language Understanding e il progetto C#. Eliminare la chiave di sottoscrizione di Language Understanding dal portale di Azure. Chiudere il progetto di Visual Studio e rimuovere la directory dal file system. 

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-cs-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website