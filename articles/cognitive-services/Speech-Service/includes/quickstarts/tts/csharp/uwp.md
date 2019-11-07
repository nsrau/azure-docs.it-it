---
title: 'Guida introduttiva: Eseguire la sintesi vocale, C# (piattaforma UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando Speech SDK di Servizi cognitivi. La sintesi vocale dal testo viene eseguita in tempo reale nell'altoparlante del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2019.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/19/2019
ms.author: yinhew
ms.openlocfilehash: 3a88f4fc14286a60feb9d72676827b0cce91eb25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505132"
---
> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=uwp)

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

A questo punto aggiungere il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiungere l'implementazione code-behind C#.

1. In **Esplora soluzioni** aprire `MainPage.xaml`.

1. Nella visualizzazione XAML della finestra di progettazione inserire il frammento di codice XAML seguente nel tag **Grid** (tra `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml#StackPanel)]

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/text-to-speech/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `Speak_ButtonClicked` del file di origine individuare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.

1. Nel gestore `Speak_ButtonClicked` individuare la stringa `YourServiceRegion` e sostituirla con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

1. Dalla barra dei menu scegliere **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

A questo punto è possibile compilare e testare l'applicazione.

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione di sintesi vocale UWP di esempio in C# - Avvio rapido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-text-to-speech-uwp-helloworld-window.png)

1. Immettere il testo nella casella di testo e fare clic su **Speak** (Parla). Il testo viene trasmesso ai servizi Voce e sintetizzato in voce, che viene riprodotta dall'altoparlante.

    ![Interfaccia utente di sintesi vocale](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
