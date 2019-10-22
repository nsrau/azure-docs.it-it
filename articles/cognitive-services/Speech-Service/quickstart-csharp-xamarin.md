---
title: 'Guida introduttiva: Riconoscimento vocale, C# (Xamarin) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione C# Xamarin multipiattaforma per la piattaforma UWP Windows, Android e iOS usando Speech SDK di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del simulatore o del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387394"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale tramite l'app Xamarin multipiattaforma con Speech SDK

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-csharp-uwp.md), la [sintesi vocale](quickstart-text-to-speech-csharp-uwp.md) e la [traduzione vocale](quickstart-translate-speech-uwp.md).

In questo articolo viene sviluppata un'applicazione C# multipiattaforma con Xamarin per la piattaforma UWP (Universal Windows Platform), Android e iOS usando [Speech SDK](speech-sdk.md). Il programma trascrive in tempo reale i contenuti vocali dal microfono del dispositivo. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2019 (qualsiasi edizione).

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Un PC Windows con Windows 10 Fall Creators Update (10.0; build 16299) o versione successiva e con un microfono funzionante.
* [Installazione di Xamarin in Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows).
* [Installazione di Xamarin Android in Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows).
* [Installazione di Xamarin iOS in Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows).
* Per Android: 
   * Un dispositivo Android (ARM32/64, x86; API 23: Android 6.0 Marshmallow o versione successiva) [abilitato per lo sviluppo](https://developer.android.com/studio/debug/dev-options) con un microfono funzionante.
* Per iOS: un dispositivo iOS (ARM64) o un simulatore iOS (x64) [abilitato per lo sviluppo](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) con un microfono funzionante.
* Per il supporto della compilazione Windows ARM64, installare gli [strumenti di compilazione facoltativi e Windows 10 SDK per ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Aggiungere il codice di esempio per il progetto `helloworld` comune

Il progetto `helloworld` comune contiene implementazioni indipendenti dalla piattaforma per l'applicazione multipiattaforma.
A questo punto aggiungere il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiungere l'implementazione code-behind C#.

1. In **Esplora soluzioni** nel progetto `helloworld` comune aprire `MainPage.xaml`.

1. Nella visualizzazione XAML della finestra di progettazione inserire il frammento di codice XAML seguente nel tag **Grid** (tra `<StackLayout>` e `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Nel gestore `OnRecognitionButtonClicked` del file di origine individuare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.

1. Nel gestore `OnRecognitionButtonClicked` individuare la stringa `YourServiceRegion` e sostituirla con l'[area](regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

1. È quindi necessario creare un [servizio Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), che viene usato per eseguire una query sulle autorizzazioni del microfono da diversi progetti di piattaforma (UWP, Android e iOS). A tale scopo, aggiungere una nuova cartella `Services` nel progetto `helloworld` e al suo interno creare un nuovo file di origine C# (fare clic con il pulsante destro del mouse sulla cartella `Services` e scegliere **Aggiungi** > **Nuovo elemento** > **File di codice**) e rinominarlo in `IMicrophoneService.cs`, quindi inserire in tale file tutto il codice del frammento seguente:

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Aggiungere il codice di esempio per il progetto `helloworld.Android`

Aggiungere ora il codice C# che definisce la parte specifica di Android dell'applicazione.

1. In **Esplora soluzioni** nel progetto `helloworld.Android` aprire `MainActivity.cs`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Aggiungere quindi l'implementazione specifica di Android per `MicrophoneService` creando una nuova cartella `Services` nel progetto `helloworld.Android`. Creare un nuovo file di origine C# e rinominarlo in `MicrophoneService.cs`, quindi copiare e incollare in tale file il frammento di codice seguente.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Aprire `AndroidManifest.xml` nella cartella `Properties` e aggiungere le seguenti impostazioni di autorizzazione d'uso per il microfono tra `<manifest>` e `</manifest>`.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Aggiungere il codice di esempio per il progetto `helloworld.iOS`

Aggiungere ora il codice C# che definisce la parte specifica di iOS dell'applicazione e creare anche configurazioni specifiche del dispositivo Apple nel progetto helloworld.iOS.

1. In **Esplora soluzioni** nel progetto `helloworld.iOS` aprire `AppDelegate.cs`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Aggiungere quindi l'implementazione specifica di iOS per `MicrophoneService` creando una nuova cartella `Services` nel progetto `helloworld.iOS`. Creare un nuovo file di origine C# e rinominarlo in `MicrophoneService.cs`, quindi copiare e incollare in tale file il frammento di codice seguente.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Aprire Info.plist nel progetto `helloworld.iOS` in un editor di testo e aggiungere la coppia chiave-valore seguente nella sezione dict <key>NSMicrophoneUsageDescription</key>
   <string>Questa app di esempio richiede l'accesso al microfono</string>
   > Note: se la compilazione è per un dispositivo iPhone, assicurarsi che `Bundle Identifier` corrisponda all'ID app del profilo di provisioning del dispositivo. In caso contrario, la compilazione avrà esito negativo. Con iPhoneSimulator è possibile lasciarlo invariata.

1. Se la compilazione è per un PC Windows, è necessario stabilire una connessione al dispositivo Mac per la compilazione tramite **Strumenti** > **iOS** > **Associa a Mac**. Per abilitare la connessione al dispositivo Mac, seguire le istruzioni guidate fornite da Visual Studio.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Aggiungere il codice di esempio per il progetto `helloworld.UWP`

Aggiungere ora il codice C# che definisce la parte specifica di UWP dell'applicazione.

1. In **Esplora soluzioni** nel progetto `helloworld.UWP` aprire `MainPage.xaml.cs`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Aggiungere quindi l'implementazione specifica di UWP per `MicrophoneService` creando una nuova cartella `Services` nel progetto `helloworld.UWP`. Creare un nuovo file di origine C# e rinominarlo in `MicrophoneService.cs`, quindi copiare e incollare in tale file il frammento di codice seguente.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Fare doppio clic sul file `Package.appxmanifest` nel progetto `helloworld.UWP` all'interno di Visual Studio e controllare che sia selezionato **Funzionalità** > **Microfono** e salvare il file.
   > Note: se viene visualizzato l'avviso: Il file del certificato non esiste: helloworld.UWP_TemporaryKey.pfx, vedere l'esempio del [riconoscimento vocale](quickstart-csharp-uwp.md) per altre informazioni.

1. Dalla barra dei menu scegliere **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-uwp-application"></a>Compilare ed eseguire l'applicazione UWP

1. Impostare `helloworld.UWP` come progetto di avvio e fare clic con il pulsante destro del mouse sul progetto `helloworld.UWP` e scegliere **Compila** per compilare l'applicazione. 

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione di riconoscimento vocale UWP di esempio in C# - Avvio rapido](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Fare clic su **Enable Microphone** (Abilita microfono) e, quando viene visualizzata la richiesta di autorizzazione di accesso, fare clic su **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selezionare **Avvia riconoscimento vocale**  e pronunciare una frase o espressione nel microfono del dispositivo. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo che viene visualizzato nella finestra.

   ![Interfaccia utente di riconoscimento vocale](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Compilare ed eseguire le applicazioni Android e iOS

La compilazione e l'esecuzione di applicazioni Android e iOS nel dispositivo o nel simulatore avvengono in modo analogo a UWP. L'importante è verificare che tutti gli SDK presenti nella sezione `Prerequisites` di questo documento siano installati correttamente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)
