---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/27/2020
ms.author: dapine
ms.openlocfilehash: 58122066e65fbcb02b6b4333985785b219d3dbbd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900512"
---
Per completare l'argomento di avvio rapido sul riconoscimento di finalità, è necessario creare un account e un progetto LUIS usando l'anteprima del portale LUIS. Questo argomento di avvio rapido richiede solo una sottoscrizione di LUIS. *Non* è necessario avere una sottoscrizione del servizio Voce.

Per prima cosa è necessario creare un account e un'app LUIS tramite l'anteprima del portale LUIS. L'app LUIS creata userà un dominio predefinito per la domotica, che fornisce finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud che è possibile chiamare con Speech SDK. 

Per creare l'app LUIS, seguire queste istruzioni:

* <a href="https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Avvio rapido: Compilare app di dominio predefinite <span class="docon docon-navigate-external x-hidden-focus"></span></a>

Al termine, saranno necessari quattro elementi:

* Eseguire di nuovo la pubblicazione con il **priming del riconoscimento vocale** attivato
* La **chiave primaria** di LUIS
* La **località** di LUIS
* L'**ID app** di LUIS

È possibile trovare queste informazioni nell'[anteprima del portale LUIS](https://preview.luis.ai/):

1. Nel portale di anteprima LUIS selezionare l'app e quindi selezionare il pulsante **Pubblica**.

2. Selezionare lo slot **Produzione**, se si usa `en-US` impostare l'opzione di **Priming del riconoscimento vocale** sulla posizione **attivata**. Quindi selezionare il pulsante **Pubblica**.

    > [!IMPORTANT]
    > L'opzione di **priming del riconoscimento vocale** è particolarmente consigliata, in quanto migliorerà l'accuratezza del riconoscimento vocale.

    > [!div class="mx-imgBorder"]
    > ![Pubblicare LUIS nell'endpoint](../../../media/luis/publish-app-popup.png)

3. Nell'anteprima del portale LUIS selezionare **Gestisci** e quindi **Risorse di Azure**. In questa pagina è possibile trovare la chiave e l'_area_ di LUIS.

   > [!div class="mx-imgBorder"]
   > ![Chiave e area di LUIS](../../../media/luis/luis-key-region.png)

4. Una volta ottenute la chiave e l'area, sarà necessario l'ID app. Selezionare **Impostazioni applicazione**: l'ID app è disponibile in questa pagina.

   > [!div class="mx-imgBorder"]
   > ![ID app di LUIS](../../../media/luis/luis-app-id.png)
