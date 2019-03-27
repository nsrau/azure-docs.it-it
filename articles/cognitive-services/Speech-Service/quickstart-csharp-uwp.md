---
title: 'Avvio rapido: Riconoscere i contenuti vocali, C# (UWP) - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando Speech SDK di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2017.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 26d6e5e53968a88eaccff863b4affee6057f5952
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57903301"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Avvio rapido: Riconoscere i contenuti vocali in un'app UWP con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo viene sviluppata un'applicazione della piattaforma UWP (Universal Windows Platform; Windows versione 1709 o successiva) C# usando [Speech SDK](speech-sdk.md) di Servizi cognitivi. Il programma trascriverà in tempo reale i contenuti vocali dal microfono del dispositivo. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 (qualsiasi edizione).

> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

1. L'interfaccia utente dell'applicazione viene definita tramite XAML. Aprire `MainPage.xaml` in Esplora soluzioni. Nella visualizzazione della finestra di progettazione XAML inserire il frammento di codice XAML seguente nel tag Grid (tra `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Aprire il file di origine code-behind `MainPage.xaml.cs` (lo si trova raggruppato sotto `MainPage.xaml`). Sostituire tutto il codice in questo file con quanto segue.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` di questo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` sostituire la stringa `YourServiceRegion`con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare tutte le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-uwp-08-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-uwp-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Viene visualizzata una finestra. Selezionare **Attiva microfono** e confermare la richiesta di autorizzazione che viene visualizzata.

    ![Screenshot della richiesta di autorizzazione](media/sdk/qs-csharp-uwp-10-access-prompt.png "Avviare l'applicazione per eseguire il debug")

1. Selezionare **Speech recognition with microphone input** (Riconoscimento vocale con l'input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo che viene visualizzato nella finestra.

    ![Screenshot dell'interfaccia utente di riconoscimento vocale](media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche 

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
