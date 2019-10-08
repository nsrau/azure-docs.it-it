---
title: 'Guida introduttiva: Sintesi vocale, .NET Framework (Windows) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di sintesi vocale con .NET Framework per Windows e Speech SDK. Al termine, è possibile sintetizzare la voce dal testo e ascoltarla dall'altoparlante in tempo reale.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: yinhew
ms.openlocfilehash: 1a411455e4a6dea22e092cdfc8e70ee23b656435
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327441"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Guida introduttiva: Eseguire la sintesi vocale con Speech SDK per .NET Framework (Windows)

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-dotnet-windows.md) e la [traduzione vocale](quickstart-translate-speech-dotnetframework-windows.md).

Usare questa guida per creare un'applicazione console di sintesi vocale con .NET Framework per Windows e Speech SDK. Al termine, è possibile sintetizzare la voce dal testo e ascoltarla dall'altoparlante in tempo reale.

Per una dimostrazione rapida (senza compilare il progetto di Visual Studio, come descritto in questo articolo), ottenere la versione più recente degli [esempi di Speech SDK di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-speech-sdk) da GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Un altoparlante (o una cuffia) disponibile.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire **Program.cs** e sostituire il codice generato automaticamente con questo codice di esempio:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Trovare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione dei servizi Voce.

1. Trovare la stringa `YourServiceRegion` e sostituirla con l'[area](regions.md) associata alla sottoscrizione. Ad esempio, per la sottoscrizione di valutazione gratuita l'area è `westus`.

1. Dalla barra dei menu scegliere **File** > **Salva tutto**.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o selezionare **F5** per avviare l'applicazione **helloworld**.

1. Immettere una frase o un'espressione in inglese. L'applicazione trasmette il testo ai servizi Voce, che inviano la sintesi vocale all'applicazione per la riproduzione con l'altoparlante.

   ![Interfaccia utente di sintesi vocale](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](record-custom-voice-samples.md)
