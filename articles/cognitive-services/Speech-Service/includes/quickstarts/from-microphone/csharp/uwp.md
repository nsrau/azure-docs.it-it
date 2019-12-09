---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, C# (UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Da definire
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 5f83f249228c96021979edc14c092eded03b96e4
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818717"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=uwp)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=uwp)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

Se queste operazioni sono già state eseguite, procedere come segue.

## <a name="open-your-project-in-visual-studio"></a>Aprire il progetto in Visual Studio

Il primo passaggio consiste nel verificare che il progetto sia aperto in Visual Studio.

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate

Aggiungere stringhe di codice che svolgano la funzione di scheletro del progetto.

1. In **Esplora soluzioni** aprire `MainPage.xaml`.

2. Nella visualizzazione XAML della finestra di progettazione inserire il frammento di codice XAML seguente nel tag **Grid** (tra `<Grid>` e `</Grid>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml#StackPanel)]

3. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

4. Sostituire il codice con il codice di base seguente:

   [!code-csharp[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=6-50,55-56,94-154)]

## <a name="create-a-speech-configuration"></a>Creare una configurazione di Voce

Prima di inizializzare un oggetto `SpeechRecognizer`, è necessario creare una configurazione che usi la chiave e l'area di sottoscrizione. Inserire questo codice nel metodo `RecognizeSpeechAsync()`.

> [!NOTE]
> Questo esempio usa il metodo `FromSubscription()` per creare `SpeechConfig`. Per un elenco completo dei metodi disponibili, vedere [Classe SpeechConfig](https://docs.microsoft.com/dotnet/api/) [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=51-53)]

## <a name="initialize-a-speechrecognizer"></a>Inizializzare uno SpeechRecognizer

Creare ora un oggetto `SpeechRecognizer`. Questo oggetto viene creato all'interno di un'istruzione using per garantire il corretto rilascio delle risorse non gestite. Inserire questo codice nel metodo `RecognizeSpeechAsync()`, immediatamente sotto la configurazione di Voce.
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=58,59,93)]

## <a name="recognize-a-phrase"></a>Riconoscere una frase

Dall'oggetto `SpeechRecognizer` chiamare il metodo `RecognizeOnceAsync()`. Questo metodo consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase.

All'interno dell'istruzione using aggiungere questo codice: [!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=66)]

## <a name="display-the-recognition-results-or-errors"></a>Visualizzare i risultati (o gli errori) del riconoscimento

Quando il servizio Voce restituisce il risultato del riconoscimento, si vorrà usarlo per qualche scopo. Per semplicità, in questo caso il risultato verrà stampato sul pannello di stato.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/uwp/from-microphone/helloworld/MainPage.xaml.cs?range=68-93)]

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

A questo punto è possibile compilare e testare l'applicazione.

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione di riconoscimento vocale UWP di esempio in C# - Avvio rapido](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-helloworld-window.png)

1. Fare clic su **Enable Microphone** (Abilita microfono) e, quando viene visualizzata la richiesta di autorizzazione di accesso, fare clic su **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selezionare **Speech recognition with microphone input** (Riconoscimento vocale con l'input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale e trascritto in formato testo, che appare nella finestra.

   ![Interfaccia utente di riconoscimento vocale](~/articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-11-ui-result.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
