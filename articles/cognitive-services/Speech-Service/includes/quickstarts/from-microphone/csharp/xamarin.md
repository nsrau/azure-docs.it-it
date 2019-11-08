---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, C# (Xamarin) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione C# Xamarin multipiattaforma per la piattaforma UWP (Universal Windows Platform) Windows, Android e iOS usando Speech SDK di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del simulatore o del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 1f98e4ac6bdc0a9b7a5982b58677732ede1911f7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505676"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md?tabs=xamarin)

Se queste operazioni sono già state eseguite, procedere come segue.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Aggiungere il codice di esempio per il progetto helloworld comune

Il progetto helloworld comune contiene implementazioni indipendenti dalla piattaforma per l'applicazione multipiattaforma. A questo punto aggiungere il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiungere l'implementazione code-behind C#.

1. In **Esplora soluzioni** nel progetto helloworld comune aprire `MainPage.xaml`.

1. Nella visualizzazione XAML della finestra di progettazione inserire il frammento di codice XAML seguente nel tag **Grid**, tra `<StackLayout>` e `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` raggruppato in `MainPage.xaml`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Nel gestore `OnRecognitionButtonClicked` del file di origine individuare la stringa `YourSubscriptionKey` e sostituirla con la chiave di sottoscrizione.


1. Nel gestore `OnRecognitionButtonClicked` individuare la stringa `YourServiceRegion` e sostituirla con l'[area](~/articles/cognitive-services/Speech-Service/regions.md) associata alla sottoscrizione. Ad esempio, usare `westus` per la sottoscrizione di valutazione gratuita.

1. È quindi necessario creare un [servizio Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), che viene usato per eseguire una query sulle autorizzazioni del microfono da diversi progetti di piattaforma, ad esempio UWP, Android e iOS. A tale scopo, aggiungere una nuova cartella denominata *Services* nel progetto helloworld e creare un nuovo file di origine C# al suo interno. È possibile fare clic con il pulsante destro del mouse sulla cartella *Services*, quindi scegliere **Aggiungi** > **Nuovo elemento** > **File di codice**. Rinominare il file `IMicrophoneService.cs` e inserirvi tutto il codice del frammento seguente:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabandroid"></a>[Android](#tab/android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Aggiungere il codice di esempio per il progetto `helloworld.Android`

Aggiungere ora il codice C# che definisce la parte specifica di Android dell'applicazione.

1. In **Esplora soluzioni** nel progetto helloworld.Android aprire `MainActivity.cs`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Aggiungere quindi l'implementazione specifica di Android per `MicrophoneService` creando una nuova cartella *Services* nel progetto helloworld.Android. Successivamente, creare un nuovo file di origine C# al suo interno. Rinominare il file `MicrophoneService.cs`. Copiare e incollare il frammento di codice seguente in tale file:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Successivamente, aprire `AndroidManifest.xml` nella cartella *Properties*. Aggiungere le seguenti impostazioni di autorizzazione d'uso per il microfono tra `<manifest>` e `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Aggiungere il codice di esempio per il progetto `helloworld.iOS`

Aggiungere ora il codice C# che definisce la parte specifica di iOS dell'applicazione. Creare inoltre configurazioni specifiche del dispositivo Apple nel progetto helloworld.iOS.

1. In **Esplora soluzioni** nel progetto helloworld.iOS aprire `AppDelegate.cs`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Aggiungere quindi l'implementazione specifica di iOS per `MicrophoneService` creando una nuova cartella *Services* nel progetto helloworld.iOS. Successivamente, creare un nuovo file di origine C# al suo interno. Rinominare il file `MicrophoneService.cs`. Copiare e incollare il frammento di codice seguente in tale file:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Aprire `Info.plist` nel progetto helloworld.iOS nell'editor di testo. Aggiungere la coppia chiave-valore seguente nella sezione dict:

   <key>NSMicrophoneUsageDescription</key>
   <string>Questa app di esempio richiede l'accesso al microfono</string>

   > [!NOTE]
   > Se la compilazione è per iPhone, assicurarsi che `Bundle Identifier` corrisponda all'ID app del profilo di provisioning del dispositivo. In caso contrario, la compilazione avrà esito negativo. Con iPhoneSimulator è possibile lasciare l'impostazione invariata.

1. Se la compilazione è per PC Windows, è necessario stabilire una connessione con il dispositivo Mac tramite **Strumenti** > **iOS** > **Associa a Mac**. Per abilitare la connessione con il dispositivo Mac, seguire le istruzioni guidate fornite da Visual Studio.

#### <a name="uwptabhelloworlduwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Aggiungere il codice di esempio per il progetto `helloworld.UWP`

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Aggiungere il codice di esempio per il progetto helloworld.UWP

Aggiungere ora il codice C# che definisce la parte specifica di UWP dell'applicazione.

1. In **Esplora soluzioni** nel progetto helloworld.UWP aprire `MainPage.xaml.cs`.

1. Sostituire tutto il codice in esso contenuto con il frammento di codice seguente:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Aggiungere quindi l'implementazione specifica di UWP per `MicrophoneService` creando una nuova cartella *Services* nel progetto helloworld.UWP. Successivamente, creare un nuovo file di origine C# al suo interno. Rinominare il file `MicrophoneService.cs`. Copiare e incollare il frammento di codice seguente in tale file:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Fare quindi doppio clic sul file `Package.appxmanifest` nel progetto helloworld.UWP all'interno di Visual Studio. In **Funzionalità** verificare che sia selezionata l'opzione **Microfono**, quindi salvare il file.

1. Fare doppio clic sul file `Package.appxmanifest` nel progetto `helloworld.UWP` all'interno di Visual Studio e controllare che sia selezionato **Funzionalità** > **Microfono** e salvare il file.
   > Note: se viene visualizzato l'avviso: Il file del certificato non esiste: helloworld.UWP_TemporaryKey.pfx, vedere l'esempio del [riconoscimento vocale](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) per altre informazioni.

1. Sulla barra dei menu selezionare **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-uwp-application"></a>Compilare ed eseguire l'applicazione UWP

1. Impostare helloworld.UWP come progetto di avvio. Fare clic con il pulsante destro del mouse sul progetto helloworld.UWP e scegliere **Compila** per compilare l'applicazione.

1. Selezionare **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione di riconoscimento vocale UWP di esempio in C# - Avvio rapido](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Selezionare **Abilita microfono**. Quando viene visualizzata la richiesta di autorizzazione di accesso, selezionare **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Selezionare **Avvia riconoscimento vocale**  e pronunciare una frase o espressione nel microfono del dispositivo. Il contenuto vocale viene trasmesso ai servizi Voce e trascritto come testo, che viene visualizzato nella finestra.

   ![Interfaccia utente di riconoscimento vocale](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Compilare ed eseguire le applicazioni Android e iOS

La compilazione e l'esecuzione di applicazioni Android e iOS nel dispositivo o nel simulatore avvengono in modo analogo a UWP. Verificare che tutti gli SDK presenti nella sezione "Prerequisiti" di questo documento siano installati correttamente.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
