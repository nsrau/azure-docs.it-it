---
title: Invia attività comandi personalizzati all'applicazione client
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come inviare l'attività da un'applicazione di comandi personalizzati a un'applicazione client che esegue l'SDK di riconoscimento vocale.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: fc62c87fd12457c60d3eb26cba6814aa1df76f87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839215"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Invia attività comandi personalizzati all'applicazione client

Questo articolo illustra come inviare l'attività da un'applicazione di comandi personalizzati a un'applicazione client che esegue l'SDK di riconoscimento vocale.

Completare le attività seguenti:

- Definire e inviare un payload JSON personalizzato dall'applicazione comandi personalizzati
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un'applicazione client C# UWP Speech SDK

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) o versione successiva. Questa guida USA Visual Studio 2019
> * Una chiave di sottoscrizione di Azure per il servizio Voce: È possibile [ottenerne una gratuitamente](overview.md#try-the-speech-service-for-free) o crearla nel [portale di Azure](https://portal.azure.com)
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza
> * App client abilitata per l'SDK vocale: [procedura: eseguire l'integrazione con un'applicazione client tramite Speech SDK](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>Imposta attività di invio sul client 
1. Aprire l'applicazione Custom Commands creata in precedenza
1. Selezionare il comando **TurnOnOff** , selezionare **ConfirmationResponse** in regola di completamento, quindi selezionare **Aggiungi un'azione**
1. In **nuovo tipo di azione**selezionare **Invia attività al client**
1. Copiare il codice JSON seguente nel **contenuto dell'attività**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. Fare clic su **Salva** per creare una nuova regola con un'azione Invia attività, eseguire il **Training** e **pubblicare** la modifica

   > [!div class="mx-imgBorder"]
   > ![Regola di completamento dell'attività di invio](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrazione con l'applicazione client

In [procedura: configurare l'applicazione client con Speech SDK (anteprima)](./how-to-custom-commands-setup-speech-sdk.md)è stata creata un'applicazione client UWP con l'SDK di riconoscimento vocale che gestiva i comandi, ad esempio `turn on the tv` , `turn off the fan` . Con alcuni oggetti visivi aggiunti, è possibile visualizzare il risultato di tali comandi.

Per aggiungere caselle con etichetta con testo che indica **on** o **off**, aggiungere il seguente blocco XML di StackPanel a `MainPage.xaml` .

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>Aggiungi librerie di riferimento

Poiché è stato creato un payload JSON, è necessario aggiungere un riferimento alla libreria [JSON.NET](https://www.newtonsoft.com/json) per gestire la deserializzazione.

1. Right-client la soluzione.
1. Scegliere **Gestisci pacchetti NuGet per la soluzione**, selezionare **Sfoglia** 
1. Se è già stato installato **Newtonsoft.json**, assicurarsi che la versione sia almeno 12.0.3. In caso contrario, passare a **Gestisci pacchetti NuGet per la soluzione-aggiornamenti**, cercare **Newtonsoft.jssu** per aggiornarlo. Questa guida usa la versione 12.0.3.

    > [!div class="mx-imgBorder"]
    > ![Payload dell'attività Send](media/custom-commands/send-activity-to-client-json-nuget.png)

1. Assicurarsi inoltre che il pacchetto NuGet **Microsoft. NETCore. UniversalWindowsPlatform** sia almeno 6.2.10. Questa guida usa la versione 6.2.10.

In ' MainPage. XAML. cs ' aggiungere

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>Gestire il payload ricevuto

In `InitializeDialogServiceConnector` sostituire il `ActivityReceived` gestore eventi con il codice seguente. Il `ActivityReceived` gestore eventi modificato estrae il payload dall'attività e modifica lo stato di visualizzazione del televisore o della ventola rispettivamente.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Procedura

1. Avviare l'applicazione
1. Selezionare Abilita microfono
1. Selezionare il pulsante Talk (Parla)
1. Pronunciare la frase `turn on the tv`
1. Lo stato di visualizzazione della TV deve cambiare in "on"
   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra che lo stato di visualizzazione di T V è ora acceso.](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: configurare endpoint Web (anteprima)](./how-to-custom-commands-setup-web-endpoints.md)
