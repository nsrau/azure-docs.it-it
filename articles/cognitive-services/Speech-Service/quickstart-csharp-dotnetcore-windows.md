---
title: 'Guida introduttiva: Riconoscimento vocale, C# (.NET Core) - Servizi Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in C# per .NET Core in Windows o macOS con Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 873145cf9d418433ba241ce06d7d594fb3e6322b
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465728"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Guida introduttiva: Riconoscimento vocale con Speech SDK per .NET Core

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-dotnetcore.md) e la [traduzione vocale](quickstart-translate-speech-dotnetcore-windows.md).

Se si vuole, è possibile scegliere un linguaggio di programmazione e/o un ambiente diverso:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo, si crea un'applicazione console C# per .NET Core su Windows o macOS usando [Speech SDK](speech-sdk.md) di Servizi cognitivi. Avviene la trascrizione del riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 (qualsiasi edizione).

> [!NOTE]
> .NET core è una multipiattaforma open source, multipiattaforma di .NET che implementa la specifica [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

È necessaria una chiave di sottoscrizione del Servizio di riconoscimento vocale per completare l'Avvio rapido. È possibile ottenerne una gratuitamente. Per informazioni dettagliate, vedere [Provare gratuitamente il servizio Voce](get-started.md).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [ASP.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `Program.cs` e sostituire tutto il codice in questo file con quanto segue.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Inoltre, sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Si apre una finestra della console che chiede di dire qualcosa. Pronunciare una frase o un'espressione in inglese. I contenuti vocali vengono trasmessi al Servizio di riconoscimento vocale e trascritti in formato testo, che viene visualizzato nella stessa finestra.

    ![Risultato sulla console dopo un riconoscimento riuscito](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Risultato sulla console dopo un riconoscimento riuscito")

## <a name="next-steps"></a>Passaggi successivi

Esempi aggiuntivi, ad esempio per eseguire il riconoscimento vocale da un file audio, sono disponibili su GitHub.

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
