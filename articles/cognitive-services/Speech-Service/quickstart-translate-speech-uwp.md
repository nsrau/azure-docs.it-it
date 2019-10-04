---
title: 'Guida introduttiva: Traduzione vocale, C# (piattaforma UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questa guida di avvio rapido si creerà un'applicazione di piattaforma UWP (Universal Windows Platform) per acquisire i contenuti vocali dell'utente, tradurli in un'altra lingua e restituire il testo nella riga di comando. Questa guida è destinata agli utenti di Windows.
services: cognitive-services
author: lisaweixu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 08/19/2019
ms.author: erhopf
ms.topic: quickstart
ms.openlocfilehash: e513cbbc615965ef196a830351aab8ac241c3f20
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382626"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c-uwp"></a>Guida introduttiva: Traduzione vocale con Speech SDK per C# (UWP)

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-uwp.md), la [sintesi vocale](quickstart-text-to-speech-csharp-uwp.md) e l'[assistente virtuale voice-first](quickstart-virtual-assistant-csharp-uwp.md).

In questa guida di avvio rapido si creerà un'applicazione di piattaforma UWP (Universal Windows Platform) per acquisire i contenuti vocali dell'utente dal microfono del computer, tradurli e trascrivere il testo tradotto nella riga di comando in tempo reale. L'applicazione è progettata per l'esecuzione in Windows a 64 bit e si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2019.

Per un elenco completo di lingue disponibili per la traduzione vocale, vedere [Supporto per le lingue](language-support.md).

> [!NOTE]
> UWP consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

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

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechTranslationFromMicrophone_ButtonClicked` di questo file individuare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.

1. Nel gestore `SpeechTranslationFromMicrophone_ButtonClicked` individuare la stringa `YourServiceRegion` e sostituirla con l'[area](regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

1. Dalla barra dei menu scegliere **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

A questo punto è possibile compilare e testare l'applicazione.

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Esempio di applicazione di traduzione della piattaforma UWP in C# - Avvio rapido](media/sdk/qs-translate-speech-uwp-helloworld-window.png)

1. Selezionare **Enable Microphone** (Abilita microfono) e, quando viene visualizzata la richiesta di autorizzazione di accesso, selezionare **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selezionare **Translate speech from the microphone input** (Traduzione vocale da input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. L'applicazione trasmette il parlato al servizio Voce, che effettua la traduzione in testo (in questo caso in tedesco). Il servizio Voce invia nuovamente il testo tradotto all'applicazione, che visualizza la traduzione nella finestra.

   ![Interfaccia utente di traduzione vocale](media/sdk/qs-translate-csharp-uwp-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Eseguire il training di un modello per Riconoscimento vocale personalizzato](how-to-custom-speech-train-model.md)
