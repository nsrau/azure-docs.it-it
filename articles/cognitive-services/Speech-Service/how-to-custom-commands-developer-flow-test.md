---
title: Testare l'app Comandi personalizzati
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono illustrati diversi approcci per il test di un'applicazione di comandi personalizzata.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c0bd21f55fee4d8487826deae23093ede293c8c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021814"
---
# <a name="test-your-custom-commands-application"></a>Testare l'applicazione dei comandi personalizzati

In questo articolo vengono illustrati diversi approcci per il test di un'applicazione di comandi personalizzata.

## <a name="test-in-the-portal"></a>Eseguire il test nel portale

Il test nel portale è il modo più semplice e rapido per verificare se l'applicazione di comando personalizzata funziona come previsto. Dopo aver eseguito il training dell'app, fare clic sul `Test` pulsante per avviare il test.

> [!div class="mx-imgBorder"]
> ![Eseguire il test nel portale](media/custom-commands/create-basic-test-chat.png)

## <a name="test-with-windows-voice-assistant-client"></a>Eseguire test con il client Windows Voice Assistant

Il client Windows Voice Assistant è un'applicazione Windows Presentation Foundation (WPF) in C# che consente di testare facilmente le interazioni con il bot prima di creare un'applicazione client personalizzata.

Lo strumento può accettare un ID applicazione di comando personalizzato. Consente di testare il completamento delle attività o lo scenario di comando e controllo ospitato nel servizio comandi personalizzati.

Per configurare il client, eseguire il checkout di [Windows Voice Assistant client](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![WVAC Crea profilo](media/custom-commands/conversation.png)

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Eseguire test con applicazioni client abilitate per l'SDK vocale 
Il Software Development Kit vocale (SDK) espone molte delle funzionalità del servizio vocale, che consente di sviluppare applicazioni abilitate per la sintesi vocale. È disponibile anche in molti linguaggi di programmazione e in tutte le piattaforme.

Per configurare un'applicazione client di piattaforma UWP (Universal Windows Platform) (UWP) con l'SDK di riconoscimento vocale e integrarla con l'applicazione di comando personalizzata:  
- [Procedura: eseguire l'integrazione con un'applicazione client tramite Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)
- [Procedura: inviare attività all'applicazione client](./how-to-custom-commands-send-activity-to-client.md)
- [Procedura: configurare endpoint Web](./how-to-custom-commands-setup-web-endpoints.md)

Per altri linguaggi e piattaforme di programmazione:
- [Linguaggi di programmazione dell'SDK vocale, piattaforme, capacità dello scenario](./speech-sdk.md)
- [Codici di esempio per Assistente vocale](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere gli esempi in GitHub](https://aka.ms/speech/cc-samples)