---
title: 'Guida introduttiva: Assistente vocale personalizzato, C# (UWP) - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando Speech SDK (Speech Software Development Kit) di Servizi cognitivi. L'applicazione client viene connessa a un bot di Bot Framework creato in precedenza e configurato per l'uso del canale Direct Line Speech. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2019.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 25d74b640f1bf567792c317626da0178ead7e34f
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816238"
---
# <a name="quickstart-create-a-voice-assistant-with-the-speech-sdk-uwp"></a>Guida introduttiva: Creare un assistente vocale con Speech SDK, piattaforma UWP

Sono disponibili argomenti di avvio rapido anche per il [riconoscimento vocale](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp), la [sintesi vocale](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=uwp) e la [traduzione vocale](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp).

In questo articolo viene sviluppata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando [Speech SDK](speech-sdk.md). Il programma si connetterà a un bot creato e configurato in precedenza per abilitare un'esperienza di assistente vocale dall'applicazione client. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2019 (qualsiasi edizione).

> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md) o crearla nel [portale di Azure](https://portal.azure.com).
* Un bot creato in precedenza e configurato per l'uso del [canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

  > [!NOTE]
  > Consultare [l'elenco delle aree supportate per gli assistenti vocali](regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

## <a name="optional-get-started-fast"></a>Facoltativo: Introduzione rapida

Questo argomento di avvio rapido descrive in dettaglio come creare un'applicazione client per connettersi al bot abilitato al riconoscimento vocale. Se si preferisce iniziare subito, il codice sorgente completo e pronto per la compilazione usato in questa guida di avvio rapido è disponibile nella cartella `quickstart` negli [esempi di SDK Speech](https://aka.ms/csspeech/samples).

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="add-sample-code"></a>Aggiungere il codice di esempio

A questo punto aggiungere il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiungere l'implementazione code-behind C#.

### <a name="xaml-code"></a>Codice XAML

Sarà innanzitutto necessario creare l'interfaccia utente dell'applicazione aggiungendo il codice XAML:

1. In **Esplora soluzioni** aprire `MainPage.xaml`.

1. Nella visualizzazione XAML della finestra di progettazione sostituire l'intero contenuto con il frammento di codice seguente:

    ```xml
    <Page
        x:Class="helloworld.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:helloworld"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <Grid>
            <StackPanel Orientation="Vertical" HorizontalAlignment="Center"  
                        Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
                <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"  
                        Margin="0,0,10,0" Click="EnableMicrophone_ButtonClicked" 
                        Height="35"/>
                <Button x:Name="ListenButton" Content="Talk to your bot" 
                        Margin="0,10,10,0" Click="ListenButton_ButtonClicked" 
                        Height="35"/>
                <StackPanel x:Name="StatusPanel" Orientation="Vertical" 
                            RelativePanel.AlignBottomWithPanel="True" 
                            RelativePanel.AlignRightWithPanel="True" 
                            RelativePanel.AlignLeftWithPanel="True">
                    <TextBlock x:Name="StatusLabel" Margin="0,10,10,0" 
                               TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                    <Border x:Name="StatusBorder" Margin="0,0,0,0">
                        <ScrollViewer VerticalScrollMode="Auto"  
                                      VerticalScrollBarVisibility="Auto" MaxHeight="200">
                            <!-- Use LiveSetting to enable screen readers to announce 
                                 the status update. -->
                            <TextBlock 
                                x:Name="StatusBlock" FontWeight="Bold" 
                                AutomationProperties.LiveSetting="Assertive"
                                MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}" 
                                Margin="10,10,10,20" TextWrapping="Wrap"  />
                        </ScrollViewer>
                    </Border>
                </StackPanel>
            </StackPanel>
            <MediaElement x:Name="mediaElement"/>
        </Grid>
    </Page>
    ```

La visualizzazione Progettazione viene aggiornata per mostrare l'interfaccia utente dell'applicazione.

### <a name="c-code-behind-source"></a>File di origine code-behind C#

È quindi necessario aggiungere il file di origine code-behind in modo che l'applicazione funzioni come previsto. Il file di origine code-behind include quanto segue:

- Istruzioni `using` per gli spazi dei nomi `Speech` e `Speech.Dialog`
- Un'implementazione semplice per garantire l'accesso al microfono, collegato a un gestore del pulsante
- Helper di base dell'interfaccia utente per presentare i messaggi e gli errori nell'applicazione
- Un punto di destinazione per il percorso del codice di inizializzazione che verrà popolato più avanti
- Un helper per riprodurre la sintesi vocale (senza supporto per lo streaming)
- Un gestore del pulsante vuoto per iniziare ad ascoltare che verrà popolato più avanti

Per aggiungere il file di origine code-behind, seguire questa procedura:

1. In **Esplora soluzioni** aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml`).

1. Sostituire il contenuto del file con il frammento di codice seguente:

    ```csharp
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Dialog;
    using System;
    using System.Diagnostics;
    using System.IO;
    using System.Text;
    using Windows.Foundation;
    using Windows.Storage.Streams;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Media;

    namespace helloworld
    {
        public sealed partial class MainPage : Page
        {
            private DialogServiceConnector connector;

            private enum NotifyType
            {
                StatusMessage,
                ErrorMessage
            };

            public MainPage()
            {
                this.InitializeComponent();
            }

            private async void EnableMicrophone_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                bool isMicAvailable = true;
                try
                {
                    var mediaCapture = new Windows.Media.Capture.MediaCapture();
                    var settings = 
                        new Windows.Media.Capture.MediaCaptureInitializationSettings();
                    settings.StreamingCaptureMode = 
                        Windows.Media.Capture.StreamingCaptureMode.Audio;
                    await mediaCapture.InitializeAsync(settings);
                }
                catch (Exception)
                {
                    isMicAvailable = false;
                }
                if (!isMicAvailable)
                {
                    await Windows.System.Launcher.LaunchUriAsync(
                        new Uri("ms-settings:privacy-microphone"));
                }
                else
                {
                    NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
                }
            }

            private void NotifyUser(
                string strMessage, NotifyType type = NotifyType.StatusMessage)
            {
                // If called from the UI thread, then update immediately.
                // Otherwise, schedule a task on the UI thread to perform the update.
                if (Dispatcher.HasThreadAccess)
                {
                    UpdateStatus(strMessage, type);
                }
                else
                {
                    var task = Dispatcher.RunAsync(
                        Windows.UI.Core.CoreDispatcherPriority.Normal, 
                        () => UpdateStatus(strMessage, type));
                }
            }

            private void UpdateStatus(string strMessage, NotifyType type)
            {
                switch (type)
                {
                    case NotifyType.StatusMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Green);
                        break;
                    case NotifyType.ErrorMessage:
                        StatusBorder.Background = new SolidColorBrush(
                            Windows.UI.Colors.Red);
                        break;
                }
                StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text) 
                    ? strMessage : "\n" + strMessage;

                if (!string.IsNullOrEmpty(StatusBlock.Text))
                {
                    StatusBorder.Visibility = Visibility.Visible;
                    StatusPanel.Visibility = Visibility.Visible;
                }
                else
                {
                    StatusBorder.Visibility = Visibility.Collapsed;
                    StatusPanel.Visibility = Visibility.Collapsed;
                }
                // Raise an event if necessary to enable a screen reader 
                // to announce the status update.
                var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
                if (peer != null)
                {
                    peer.RaiseAutomationEvent(
                        Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
                }
            }

            // Waits for and accumulates all audio associated with a given 
            // PullAudioOutputStream and then plays it to the MediaElement. Long spoken 
            // audio will create extra latency and a streaming playback solution 
            // (that plays audio while it continues to be received) should be used -- 
            // see the samples for examples of this.
            private void SynchronouslyPlayActivityAudio(
                PullAudioOutputStream activityAudio)
            {
                var playbackStreamWithHeader = new MemoryStream();
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
                playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
                playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
                playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
                playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
                playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
                playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

                byte[] pullBuffer = new byte[2056];

                uint lastRead = 0;
                do
                {
                    lastRead = activityAudio.Read(pullBuffer);
                    playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
                }
                while (lastRead == pullBuffer.Length);

                var task = Dispatcher.RunAsync(
                    Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    mediaElement.SetSource(
                        playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                    mediaElement.Play();
                });
            }

            private void InitializeDialogServiceConnector()
            {
                // New code will go here
            }

            private async void ListenButton_ButtonClicked(
                object sender, RoutedEventArgs e)
            {
                // New code will go here
            }
        }
    }
    ```

1. Aggiungere il frammento di codice seguente al corpo del metodo di `InitializeDialogServiceConnector`. Tale codice crea l'elemento `DialogServiceConnector` con le informazioni della sottoscrizione.

    ```csharp
    // Create a BotFrameworkConfig by providing a Speech service subscription key
    // the RecoLanguage property is optional (default en-US)
    const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
    const string region = "YourServiceRegion"; // Your subscription service region.

    var botConfig = BotFrameworkConfig.FromSubscription(speechSubscriptionKey, region);
    botConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-US");
    connector = new DialogServiceConnector(botConfig);
    ```

   > [!NOTE]
   > Consultare [l'elenco delle aree supportate per gli assistenti vocali](regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

   > [!NOTE]
   > Per informazioni sulla configurazione del bot e sul recupero di un segreto del canale, vedere la documentazione di Bot Framework relativa al [canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

1. Sostituire le stringhe `YourChannelSecret`, `YourSpeechSubscriptionKey` e `YourServiceRegion` con i valori personalizzati relativi al bot, alla sottoscrizione dei servizi Voce e all'[area](regions.md).

1. Aggiungere il frammento di codice seguente alla fine del corpo del metodo di `InitializeDialogServiceConnector`. Tale codice imposta i gestori per gli eventi su cui si basa `DialogServiceConnector` per comunicare le attività del bot, i risultati del riconoscimento vocale e altre informazioni.

    ```csharp
    // ActivityReceived is the main way your bot will communicate with the client 
    // and uses bot framework activities
    connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
    {
        NotifyUser(
            $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

        if (activityReceivedEventArgs.HasAudio)
        {
            SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
        }
    };

    // Canceled will be signaled when a turn is aborted or experiences an error condition
    connector.Canceled += (sender, canceledEventArgs) =>
    {
        NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
        if (canceledEventArgs.Reason == CancellationReason.Error)
        {
            NotifyUser(
                $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
        }
    };

    // Recognizing (not 'Recognized') will provide the intermediate recognized text 
    // while an audio stream is being processed
    connector.Recognizing += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
    };

    // Recognized (not 'Recognizing') will provide the final recognized text 
    // once audio capture is completed
    connector.Recognized += (sender, recognitionEventArgs) =>
    {
        NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
    };

    // SessionStarted will notify when audio begins flowing to the service for a turn
    connector.SessionStarted += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
    };

    // SessionStopped will notify when a turn is complete and 
    // it's safe to begin listening again
    connector.SessionStopped += (sender, sessionEventArgs) =>
    {
        NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
    };
    ```

1. Aggiungere il frammento di codice seguente al corpo del metodo `ListenButton_ButtonClicked` nella classe `MainPage`. Tale codice imposta `DialogServiceConnector` per l'ascolto, dal momento che la configurazione è già stata stabilita e i gestori degli eventi sono già stati registrati.

    ```csharp
    if (connector == null)
    {
        InitializeDialogServiceConnector();
        // Optional step to speed up first interaction: if not called, 
        // connection happens automatically on first use
        var connectTask = connector.ConnectAsync();
    }

    try
    {
        // Start sending audio to your speech-enabled bot
        var listenTask = connector.ListenOnceAsync();

        // You can also send activities to your bot as JSON strings -- 
        // Microsoft.Bot.Schema can simplify this
        string speakActivity = 
            @"{""type"":""message"",""text"":""Greeting Message"", ""speak"":""Hello there!""}";
        await connector.SendActivityAsync(speakActivity);

    }
    catch (Exception ex)
    {
        NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
    }
    ```

1. Dalla barra dei menu scegliere **File** > **Salva tutto** per salvare le modifiche.

## <a name="build-and-run-the-application"></a>Compilare ed eseguire l'applicazione

A questo punto è possibile compilare e testare l'applicazione.

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione di assistente virtuale UWP di esempio in C# - Avvio rapido](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Fare clic su **Enable Microphone** (Abilita microfono) e, quando viene visualizzata la richiesta di autorizzazione di accesso, fare clic su **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selezionare **Talk to your bot** (Parla al bot) e pronunciare una frase o un'espressione nel microfono del dispositivo. Il contenuto vocale viene trasmesso al canale Direct Line Speech e trascritto come testo che viene visualizzato nella finestra.
<!--
    ![Successful bot response](media/voice-assistants/quickstart-cs-uwp-bot-successful-turn.png)
-->
## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e distribuire un bot di base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Vedere anche

- [Informazioni sugli assistenti vocali](voice-assistants.md)
- [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
- [Parole chiave personalizzate](speech-devices-sdk-create-kws.md)
- [Collegare Direct Line Speech al bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Esaminare gli esempi di codice C# su GitHub](https://aka.ms/csspeech/samples)
