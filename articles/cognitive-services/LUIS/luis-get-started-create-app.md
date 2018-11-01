---
title: 10 minuti per creare la prima app LUIS
titleSuffix: Azure Cognitive Services
description: Creare un'app LUIS che usa il dominio predefinito `HomeAutomation` per accendere e spegnere luci ed elettrodomestici. Questo dominio predefinito fornisce all'utente finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: cc4a1a7f99de657a6199985dd0c963eed741ac97
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210024"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Guida introduttiva: Usare l'app Home Automation predefinita

In questa guida introduttiva si crea un'app LUIS che usa il dominio predefinito `HomeAutomation` per accendere e spegnere luci ed elettrodomestici. Questo dominio predefinito fornisce all'utente finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud.

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario un account LUIS gratuito, creato nel portale LUIS all'indirizzo [https://www.luis.ai](https://www.luis.ai). 

## <a name="create-a-new-app"></a>Creare una nuova app
È possibile creare e gestire le applicazioni in **My Apps** (App personali). 

1. Accedere al portale LUIS.

2. Selezionare **Create new app** (Crea nuova app).

    [![](media/luis-quickstart-new-app/app-list.png "Screenshot dell'elenco di app")](media/luis-quickstart-new-app/app-list.png)

3. Nella finestra di dialogo assegnare all'applicazione il nome "Home Automation".

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Schermata della finestra di dialogo popup Create new app (Crea nuova app)")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Scegliere le impostazioni cultura dell'applicazione. Per questa app Home Automation scegliere English (Inglese). Al termine selezionare **Done** (Fine). Viene creata l'app Home Automation. 

    >[!NOTE]
    >Le impostazioni cultura non possono essere modificate dopo la creazione dell'applicazione. 

## <a name="add-prebuilt-domain"></a>Aggiungi dominio predefinito

Selezionare **Prebuilt domains** (Domini predefiniti) nel riquadro di spostamento a sinistra e quindi cercare "Home". Selezionare **Add domain** (Aggiungi dominio).

[![](media/luis-quickstart-new-app/home-automation.png "Screenshot del dominio Home Automation richiamato nel menu dei domini predefiniti")](media/luis-quickstart-new-app/home-automation.png)

Dopo che il dominio è stato aggiunto, nella casella del dominio predefinito viene visualizzato un pulsante **Remove domain** (Rimuovi dominio).

[![](media/luis-quickstart-new-app/remove-domain.png "Screenshot del dominio Home Automation con il pulsante di rimozione")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Finalità ed entità

Selezionare **Intents** (Finalità) nel riquadro di spostamento a sinistra per esaminare le finalità del dominio HomeAutomation. Per ogni finalità sono definite espressioni di esempio.

> [!NOTE]
> **None** (Nessuna) è una finalità fornita da tutte le app LUIS. Viene usata per gestire le espressioni che non corrispondono alle funzionalità offerte dall'app. 

Selezionare la finalità **HomeAutomation.TurnOff**. È possibile vedere che la finalità contiene un elenco di espressioni con etichette di entità.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Screenshot della finalità HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Test dell'app
Dopo aver eseguito il training dell'app, è possibile eseguirne il test. Selezionare **Test** nella barra di spostamento superiore. Digitare un'espressione di test, ad esempio "Turn off the lights" nel riquadro di test interattivo e premere INVIO. 

```
Turn off the lights
```

Verificare che la finalità con il punteggio più elevato corrisponda a quella prevista per ogni espressione di test.

In questo esempio, "Turn off the lights" viene correttamente identificata come la finalità con il punteggio più elevato per "HomeAutomation.TurnOff".

[![](media/luis-quickstart-new-app/test.png "Screenshot del pannello Test con l'espressione evidenziata")](media/luis-quickstart-new-app/test.png)


Selezionare nuovamente **Test** per comprimere il riquadro di test. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query nell'endpoint con un'espressione diversa

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Andare alla fine dell'URL nell'indirizzo e immettere `turn off the living room light`, quindi premere Invio. Il browser mostra la risposta JSON dell'endpoint HTTP.

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "Screenshot del browser con il risultato JSON che rileva la finalità TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Pulire le risorse

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

È possibile chiamare l'endpoint dal codice:

> [!div class="nextstepaction"]
> [Chiamare un endpoint LUIS tramite codice](luis-get-started-cs-get-intent.md)
