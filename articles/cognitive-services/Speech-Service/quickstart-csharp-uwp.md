---
title: 'Guida introduttiva: Riconoscimento vocale, C# (piattaforma UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando Speech SDK di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: fe5ff376a7895e2ca5246c0b9eb575752b07c7a1
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382260"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscere i contenuti vocali in un'app UWP con Speech SDK

Sono disponibili argomenti di avvio rapido anche per la [sintesi vocale](quickstart-text-to-speech-csharp-uwp.md), la [traduzione vocale](quickstart-translate-speech-uwp.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-csharp-uwp.md).

Se si vuole, è possibile scegliere un linguaggio di programmazione e/o un ambiente diverso:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo viene sviluppata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando [Speech SDK](speech-sdk.md) di Servizi cognitivi. Il programma trascrive in tempo reale i contenuti vocali dal microfono del dispositivo. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2019 (qualsiasi edizione).

> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

A questo punto aggiungere il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiungere l'implementazione code-behind C#.

1. In **Esplora soluzioni** aprire `MainPage.xaml`.

1. Nella visualizzazione XAML della finestra di progettazione inserire il frammento di codice XAML seguente nel tag **Grid** (tra `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` del file di origine individuare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` individuare la stringa `YourServiceRegion` e sostituirla con l'[area](regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

1. Dalla barra dei menu scegliere **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

A questo punto è possibile compilare e testare l'applicazione.

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione di riconoscimento vocale UWP di esempio in C# - Avvio rapido](media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Fare clic su **Enable Microphone** (Abilita microfono) e, quando viene visualizzata la richiesta di autorizzazione di accesso, fare clic su **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selezionare **Speech recognition with microphone input** (Riconoscimento vocale con l'input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo che viene visualizzato nella finestra.

   ![Interfaccia utente di riconoscimento vocale](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Guida introduttiva: Traduzione vocale con Speech SDK per C# (UWP)](quickstart-translate-speech-uwp.md)
- [Eseguire il training di un modello per Riconoscimento vocale personalizzato](how-to-custom-speech-train-model.md)
