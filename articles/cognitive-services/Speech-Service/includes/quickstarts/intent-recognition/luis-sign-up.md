---
title: 'Avvio rapido: Riconoscere la voce, le finalità e le entità, Python - Servizio Voce'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/27/2019
ms.author: erhopf
ms.openlocfilehash: aee5d6e1f6ed0519df7d1059b39f215d9f0d9091
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660515"
---
Per completare l'argomento di avvio rapido sul riconoscimento di finalità, è necessario creare un account e un progetto LUIS usando l'anteprima del portale LUIS. Questo argomento di avvio rapido richiede solo una sottoscrizione di LUIS. Non è necessario avere una sottoscrizione del servizio Voce.

Per prima cosa è necessario creare un account e un'app LUIS tramite l'anteprima del portale LUIS. L'app LUIS creata userà un dominio predefinito per la domotica, che fornisce finalità, entità ed espressioni di esempio. Al termine, sarà disponibile un endpoint LUIS in esecuzione nel cloud che è possibile chiamare con Speech SDK. 

Per creare l'app LUIS, seguire queste istruzioni: 

* <a href="https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-get-started-create-app" target="_blank">Avvio rapido: Compilare app di dominio predefinite</a>

Al termine, saranno necessari tre elementi: 

* La chiave di LUIS
* L'area di LUIS
* L'ID app di LUIS.

È possibile trovare queste informazioni nell'[anteprima del portale LUIS](https://preview.luis.ai/):

1. Nell'anteprima del portale LUIS selezionare **Gestisci** e quindi **Risorse di Azure**. In questa pagina è possibile trovare la chiave e l'_area_ di LUIS.  

   > [!div class="mx-imgBorder"]
   > ![Chiave e area di LUIS](../../../media/luis/luis-key-region.png)

2. Una volta ottenute la chiave e l'area, sarà necessario l'ID app. Selezionare **Impostazioni applicazione**: l'ID app è disponibile in questa pagina.

   > [!div class="mx-imgBorder"]
   > ![ID app di LUIS](../../../media/luis/luis-app-id.png)