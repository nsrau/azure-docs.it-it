---
title: 'Avvio rapido: Traduzione vocale C# (UWP)-servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si creerà una semplice applicazione di piattaforma UWP (Universal Windows Platform) per acquisire i contenuti vocali dell'utente, convertirli in un'altra lingua e restituire il testo nella riga di comando. Questa guida è destinata agli utenti di Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 07/23/2019
ms.author: erhopf
ms.openlocfilehash: e73f15525aa679a3ba0242dab897adf999fe7e09
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559257"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Avvio rapido: Traduzione vocale con Speech SDK per C# (UWP)

Sono disponibili guide di avvio rapido anche per la [sintesi vocale](quickstart-csharp-uwp.md), la [traduzione con sintesi vocale](quickstart-text-to-speech-csharp-uwp.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-csharp-uwp.md).

In questa guida introduttiva si creerà una semplice applicazione di piattaforma UWP (Universal Windows Platform) per acquisire i contenuti vocali dell'utente dal microfono del computer, tradurli e trascrivere il testo tradotto nella riga di comando in tempo reale. L'applicazione è progettata per l'esecuzione in Windows a 64 bit e si basa sul pacchetto [NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017.

Per un elenco completo di lingue disponibili per la traduzione vocale, vedere [Supporto per le lingue](language-support.md).

> [!NOTE]
> UWP consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungi codice di esempio

1. L'interfaccia utente dell'applicazione viene definita tramite XAML. Aprire `MainPage.xaml` in Esplora soluzioni. Nella visualizzazione della finestra di progettazione XAML inserire il frammento di codice XAML seguente tra `<Grid>` e `</Grid>`.

    [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Aprire il file di origine code-behind `MainPage.xaml.cs` (lo si trova raggruppato sotto `MainPage.xaml`). Sostituire tutto il codice in questo file con quanto segue.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechTranslationFromMicrophone_ButtonClicked` di questo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Nel gestore `SpeechTranslationFromMicrophone_ButtonClicked` sostituire la stringa `YourServiceRegion`con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare tutte le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-uwp-08-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-uwp-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Viene visualizzata una finestra. Selezionare **Attiva microfono** e confermare la richiesta di autorizzazione che viene visualizzata.

    ![Screenshot della richiesta di autorizzazione](media/sdk/qs-csharp-uwp-10-access-prompt.png "Avviare l'applicazione per eseguire il debug")

1. Selezionare **Speech recognition with microphone input** (Riconoscimento vocale con l'input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale e trascritto in formato testo, che appare nella finestra.

    ![Screenshot dell'interfaccia utente di riconoscimento vocale](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
