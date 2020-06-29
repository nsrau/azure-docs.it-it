---
title: Eseguire l'integrazione con un'app client tramite Speech SDK
titleSuffix: Azure Cognitive Services
description: In questo articolo si apprenderà come eseguire richieste a un'applicazione di comandi personalizzati pubblicati dall'SDK di riconoscimento vocale in esecuzione in un'applicazione UWP.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 6aa63c49328848ca707e938dada6ce3af9f75694
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414361"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Eseguire l'integrazione con un'applicazione client tramite Speech SDK

In questo articolo si apprenderà come eseguire richieste a un'applicazione di comandi personalizzati pubblicati dall'SDK di riconoscimento vocale in esecuzione in un'applicazione UWP. Per stabilire una connessione all'applicazione Custom Commands, effettuare le operazioni seguenti:

- Pubblicare un'applicazione di comandi personalizzati e ottenere un identificatore dell'applicazione (ID app)
- Creare un'app client di piattaforma UWP (Universal Windows Platform) (UWP) usando l'SDK per la sintesi vocale per consentire la comunicazione con l'applicazione dei comandi personalizzati

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, è necessaria un'applicazione comandi personalizzata. Se non è stata creata un'applicazione comandi personalizzata, è possibile eseguire questa operazione seguendo le guide introduttive:
> [!div class = "checklist"]
> * [Creare un'applicazione di comandi personalizzati](quickstart-custom-commands-application.md)

Sono necessari anche:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Una chiave di sottoscrizione di Azure per i servizi Voce. [Ottenerne uno](get-started.md) gratuitamente o crearlo nel [portale di Azure](https://portal.azure.com)
> * [Abilitare il dispositivo per lo sviluppo](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Passaggio 1: pubblicare l'applicazione comandi personalizzati

1. Aprire l'applicazione comandi personalizzati creati in precedenza e selezionare **pubblica**

   > [!div class="mx-imgBorder"]
   > ![Pubblicare l'applicazione](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Copiare l'ID app dalla notifica di pubblicazione per un uso successivo
1. Copiare la chiave della risorsa vocale per un uso successivo

## <a name="step-2-create-a-visual-studio-project"></a>Passaggio 2: creare un progetto di Visual Studio

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Passaggio 3: aggiungere il codice di esempio

In questo passaggio viene aggiunto il codice XAML che definisce l'interfaccia utente dell'applicazione e aggiunge l'implementazione code-behind C#.

### <a name="xaml-code"></a>Codice XAML

Creare l'interfaccia utente dell'applicazione aggiungendo il codice XAML.

1. In **Esplora soluzioni**aprire`MainPage.xaml`

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
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
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

Aggiungere l'origine code-behind in modo che l'applicazione funzioni come previsto. Il file di origine code-behind include quanto segue:

- `using`Istruzioni obbligatorie per gli `Speech` `Speech.Dialog` spazi dei nomi e
- Un'implementazione semplice per garantire l'accesso al microfono, collegato a un gestore del pulsante
- Helper di base dell'interfaccia utente per presentare i messaggi e gli errori nell'applicazione
- Un punto di destinazione per il percorso del codice di inizializzazione che verrà popolato più avanti
- Un helper per riprodurre la sintesi vocale (senza supporto per lo streaming)
- Un gestore del pulsante vuoto per iniziare ad ascoltare che verrà popolato più avanti

Aggiungere l'origine code-behind come indicato di seguito:

1. In **Esplora soluzioni**aprire il file di origine code-behind `MainPage.xaml.cs` (raggruppato in `MainPage.xaml` )

1. Sostituire il contenuto del file con il codice seguente: 

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
    > [!NOTE]
    > Se viene visualizzato l'errore: "il tipo ' Object ' è definito in un assembly a cui non viene fatto riferimento"
    > 1. Right-client la soluzione.
    > 1. Scegliere **Gestisci pacchetti NuGet per la soluzione**, selezionare **aggiornamenti** 
    > 1. Se viene visualizzato **Microsoft. NETCore. UniversalWindowsPlatform** nell'elenco degli aggiornamenti, aggiornare **Microsoft. NETCore. UniversalWindowsPlatform** alla versione più recente

1. Aggiungere il codice seguente al corpo del metodo di`InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Sostituire le stringhe `YourApplicationId` , `YourSpeechSubscriptionKey` e `YourServiceRegion` con i valori personalizzati per l'app, la sottoscrizione vocale e l' [area](regions.md)

1. Aggiungere il frammento di codice seguente alla fine del corpo del metodo di`InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
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

1. Aggiungere il frammento di codice seguente al corpo del `ListenButton_ButtonClicked` metodo nella `MainPage` classe

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Dalla barra dei menu scegliere **file**  >  **Salva tutto** per salvare le modifiche

## <a name="try-it-out"></a>Procedura

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere **F5** per avviare l'applicazione. Verrà visualizzata la finestra **helloworld**.

   ![Applicazione Assistente virtuale UWP di esempio in C# - Avvio rapido](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Selezionare **Abilita microfono**. Se viene visualizzata la richiesta di autorizzazione di accesso, selezionare **Sì**.

   ![Richiesta di autorizzazione di accesso al microfono](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Selezionare **Talk**e pronunciare una frase o una frase in inglese nel microfono del dispositivo. Il contenuto vocale viene trasmesso al canale Direct Line Speech e trascritto come testo che viene visualizzato nella finestra.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: inviare l'attività all'applicazione client (anteprima)](./how-to-custom-commands-send-activity-to-client.md)
