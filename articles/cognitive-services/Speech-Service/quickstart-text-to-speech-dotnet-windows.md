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
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 3c925e0160a4c57f7e43c3463fc1db7098a6e606
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553599"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Guida introduttiva: Eseguire la sintesi vocale con Speech SDK per .NET Framework (Windows)

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-dotnet-windows.md) e la [traduzione vocale](quickstart-translate-speech-dotnetframework-windows.md).

Usare questa guida per creare un'applicazione console di sintesi vocale con .NET Framework per Windows e Speech SDK. Al termine, è possibile sintetizzare la voce dal testo e ascoltarla dall'altoparlante in tempo reale.

Per una rapida dimostrazione (senza compilare il progetto di Visual Studio come illustrato di seguito):

Ottenere la versione più recente degli [esempi di Speech SDK di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-speech-sdk) da GitHub.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo progetto, saranno necessari:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Un altoparlante (o una cuffia) disponibile.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Apri `Program.cs` e sostituire il codice generato automaticamente con questo codice di esempio:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Individuare e sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione dei servizi Voce.

1. Individuare e sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione, ad esempio `westus` per la sottoscrizione di valutazione gratuita.

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilazione completata")

1. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Viene visualizzata una finestra della console che chiede di digitare un testo. Digitare alcune parole o una frase. Il testo digitato viene trasmesso ai servizi Voce e sintetizzato in voce, che viene riprodotta dall'altoparlante.

    ![Risultato sulla console dopo un riconoscimento riuscito](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Risultato sulla console dopo un riconoscimento riuscito")

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
