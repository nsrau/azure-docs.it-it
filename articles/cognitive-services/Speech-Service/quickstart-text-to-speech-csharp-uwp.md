---
title: 'Guida introduttiva: Eseguire la sintesi vocale, C# (piattaforma UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando Speech SDK di Servizi cognitivi. La sintesi vocale dal testo viene eseguita in tempo reale nell'altoparlante del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2017.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 6/24/2019
ms.author: yinhew
ms.openlocfilehash: 5e1f8aea1b00cbba7fec6c7ca416a965458ab526
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68607749"
---
# <a name="quickstart-synthesize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Guida introduttiva: Eseguire la sintesi vocale in un'app della piattaforma UWP con Speech SDK

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-uwp.md), la [traduzione vocale](quickstart-translate-speech-uwp.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-csharp-uwp.md).

In questo articolo viene sviluppata un'applicazione della piattaforma UWP (Universal Windows Platform; Windows versione 1709 o successiva) C# usando [Speech SDK](speech-sdk.md) di Servizi cognitivi. Il programma esegue la sintesi vocale dal testo in tempo reale nell'altoparlante del dispositivo. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 o versione successiva (qualsiasi edizione).

> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) o versione successiva
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. L'interfaccia utente dell'applicazione viene definita tramite XAML. Aprire `MainPage.xaml` in Esplora soluzioni. Nella visualizzazione della finestra di progettazione XAML inserire il frammento di codice XAML seguente nel tag Grid (tra `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Aprire il file di origine code-behind `MainPage.xaml.cs` (lo si trova raggruppato sotto `MainPage.xaml`). Sostituire tutto il codice in questo file con quanto segue.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `Speak_ButtonClicked` di questo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Nel gestore `Speak_ButtonClicked` sostituire la stringa `YourServiceRegion`con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare tutte le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-uwp-08-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-uwp-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Immettere il testo nella casella di testo e fare clic su **Speak** (Parla). Il testo viene trasmesso ai servizi Voce e sintetizzato in voce, che viene riprodotta dall'altoparlante.

    ![Screenshot dell'interfaccia utente di sintesi vocale](media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
