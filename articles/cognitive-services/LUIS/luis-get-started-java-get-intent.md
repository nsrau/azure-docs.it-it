---
title: Esercitazione su come chiamare un'app di Language Understanding (LUIS) tramite Java | Microsoft Docs
description: Questa esercitazione illustra come chiamare un'app di Language Understanding con Java.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 7d27b464c86e979132dd44c0edcf981a475040b3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263721"
---
# <a name="tutorial-call-a-luis-endpoint-using-java"></a>Esercitazione: Chiamare un endpoint di Language Understanding tramite Java
Passare espressioni a un endpoint di Language Understanding per ottenere finalità ed entità.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare una sottoscrizione di Language Understanding e copiare il valore chiave per un uso successivo
> * Visualizzare i risultati dell'endpoint di Language Understanding dal browser all'app pubblica IoT di esempio
> * Creare un' app console in Visual Studio C# per effettuare una chiamata HTTPS all'endpoint di Language Understanding

Per questo articolo è necessario un account [LUIS][LUIS] gratuito per creare la propria applicazione.

## <a name="create-luis-subscription-key"></a>Creare una chiave di sottoscrizione di Language Understanding
Per effettuare chiamate all'app di Language Understanding di esempio usata in questa procedura dettagliata è necessaria una chiave API per Servizi cognitivi. 

Per ottenere una chiave API, seguire questa procedura: 
1. Creare innanzitutto un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nel Portale di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

2. Accedere al portale di Azure all'indirizzo https://portal.azure.com. 

3. Seguire i passaggi descritti in [Creating Subscription Keys using Azure](./luis-how-to-azure-subscription.md) (Creazione di chiavi di sottoscrizione tramite Azure) per ottenere una chiave.

4. Accedere nuovamente al sito Web di [Language Understanding](luis-reference-regions.md) con il proprio account di Azure. 

    [![](media/luis-get-started-java-get-intent/app-list.png "Screenshot dell'Elenco di app")](media/luis-get-started-java-get-intent/app-list.png)

## <a name="understand-what-luis-returns"></a>Comprendere i risultati restituiti da Language Understanding

Per comprendere i risultati restituiti da Language Understanding, è possibile incollare l'URL di un'app di Language Understanding di esempio in una finestra del browser. L'app di esempio è un'app IoT che rileva se l'utente desidera accendere o spegnere le luci.

1. L'endpoint dell'app di esempio ha il formato seguente: `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=<YOUR_API_KEY>&verbose=false&q=turn%20on%20the%20bedroom%20light` Copiare l'URL e sostituire la chiave di sottoscrizione per il valore del campo `subscription-key`.
2. Incollare l'URL in una finestra del browser e premere INVIO. Il browser visualizzerà un risultato JSON che indica che Language Understanding rileva la finalità `HomeAutomation.TurnOn` e l'entità `HomeAutomation.Room` con il valore `bedroom`.

    ![Il risultato JSON rileva la finalità TurnOn](./media/luis-get-started-java-get-intent/turn-on-bedroom.png)
3. Modificare il valore del parametro `q=` nell'URL per `turn off the living room light`, quindi premere INVIO. Il risultato indica adesso che Language Understanding ha rilevato la finalità `HomeAutomation.TurnOff` e l'entità `HomeAutomation.Room` con valore `living room`. 

    ![Il risultato JSON rileva la finalità TurnOff](./media/luis-get-started-java-get-intent/turn-off-living-room.png)


## <a name="consume-a-luis-result-using-the-endpoint-api-with-java"></a>Usare un risultato di Language Understanding tramite l'API per endpoint con Java 

È possibile usare Java per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente. 
1. Copiare il codice seguente per creare una classe nell'ambiente di sviluppo integrato:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/endpoint-api-samples/java/call-endpoint.java)]
2. Sostituire il valore della variabile `SubscriptionKey` con la chiave di sottoscrizione di Language Understanding.

3. Nell'ambiente di sviluppo integrato, aggiungere riferimenti alle librerie `httpclient` e `httpcore`.

4. Eseguire l'applicazione console. Verrà visualizzato lo stesso JSON illustrato in precedenza nella finestra del browser.

![La finestra della console mostra i risultati JSON da Language Understanding](./media/luis-get-started-java-get-intent/console-turn-on.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Le due risorse create in questa esercitazione sono la chiave di sottoscrizione di Language Understanding e il progetto C#. Eliminare la chiave di sottoscrizione di Language Understanding dal portale di Azure. Chiudere il progetto di Visual Studio e rimuovere la directory dal file system.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-java-add-utterance.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
