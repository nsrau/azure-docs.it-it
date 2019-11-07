---
title: 'Guida introduttiva: Sintesi vocale, C# (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di sintesi vocale con .NET Framework per Windows e Speech SDK. Al termine, è possibile sintetizzare la voce dal testo e ascoltarla dall'altoparlante in tempo reale.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: af101862e824d6527c08c232812913565536bb4a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505156"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire **Program.cs** e sostituire il codice generato automaticamente con questo codice di esempio:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/text-to-speech/helloworld/Program.cs#code)]

1. Trovare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione dei servizi Voce.

1. Trovare la stringa `YourServiceRegion` e sostituirla con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, per la sottoscrizione di valutazione gratuita l'area è `westus`.

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o selezionare **F5** per avviare l'applicazione **helloworld**.

1. Immettere una frase o un'espressione in inglese. L'applicazione trasmette il testo ai servizi Voce, che inviano la sintesi vocale all'applicazione per la riproduzione con l'altoparlante.

   ![Interfaccia utente di sintesi vocale](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
