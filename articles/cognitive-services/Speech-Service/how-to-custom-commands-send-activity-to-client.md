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
ms.openlocfilehash: 0a3e3455615006c0e93cf32eebcdaedac9960a79
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307737"
---
# <a name="send-custom-commands-activity-to-client-application"></a>Invia attività comandi personalizzati all'applicazione client

Questo articolo illustra come inviare l'attività da un'applicazione di comandi personalizzati a un'applicazione client che esegue l'SDK di riconoscimento vocale.

Completare le attività seguenti:

- Definire e inviare un payload JSON personalizzato dall'applicazione comandi personalizzati
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un'applicazione client C# UWP Speech SDK

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Una chiave di sottoscrizione di Azure per il servizio di riconoscimento vocale: [ottenerne una gratuita](get-started.md) o crearla nel [portale di Azure](https://portal.azure.com)
> * [App comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza
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
1. Fare clic su **Salva** per creare una nuova regola con un'azione Invia attività

   > [!div class="mx-imgBorder"]
   > ![Regola di completamento dell'attività di invio](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>Integrazione con l'applicazione client

In [procedura: configurare l'applicazione client con Speech SDK (anteprima)](./how-to-custom-commands-setup-speech-sdk.md)è stata creata un'applicazione client UWP con l'SDK di riconoscimento vocale che gestiva i comandi, ad esempio `turn on the tv` , `turn off the fan` . Con alcuni oggetti visivi aggiunti, è possibile visualizzare il risultato di tali comandi.

Aggiungere caselle con etichetta con testo che indica **on** o **off** usando il codice XML seguente aggiunto a`MainPage.xaml`

```xml
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
```

### <a name="add-reference-libraries"></a>Aggiungi librerie di riferimento

Poiché è stato creato un payload JSON, è necessario aggiungere un riferimento alla libreria [JSON.NET](https://www.newtonsoft.com/json) per gestire la deserializzazione.

1. Right-client la soluzione.
1. Scegliere **Gestisci pacchetti NuGet per la soluzione**, selezionare **Installa** 
1. Cercare **Newtonsoft.js** nell'elenco di aggiornamento, aggiornare **Microsoft. NETCore. UniversalWindowsPlatform** alla versione più recente

> [!div class="mx-imgBorder"]
> ![Payload dell'attività Send](media/custom-commands/send-activity-to-client-json-nuget.png)

In ' MainPage. XAML. cs ' aggiungere
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

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
1. Selezionare il pulsante Talk
1. Ad esempio`turn on the tv`
1. Lo stato di visualizzazione della TV deve cambiare in "on"
   > [!div class="mx-imgBorder"]
   > ![Payload dell'attività Send](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: configurare endpoint Web (anteprima)](./how-to-custom-commands-setup-web-endpoints.md)