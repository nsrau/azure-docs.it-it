---
title: Come eseguire i comandi da un client con l'SDK di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come gestire le attività dei comandi personalizzati in un client con l'SDK di riconoscimento vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: trbye
ms.openlocfilehash: f11f5f3c2ad4c9f0241d34edeb664f739f88d15c
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871772"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Soddisfare i comandi da un client con l'SDK di riconoscimento vocale (anteprima)

Per completare le attività usando un'applicazione di comandi personalizzati è possibile inviare payload personalizzati a un dispositivo client connesso.

In questo articolo verranno illustrate le operazioni seguenti:

- Definire e inviare un payload JSON personalizzato dall'applicazione comandi personalizzati
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un'applicazione client C# UWP Speech SDK

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Una chiave di sottoscrizione di Azure per il servizio di riconoscimento vocale: [ottenerne una gratuita](get-started.md) o crearla nel [portale di Azure](https://portal.azure.com)
> * App comandi personalizzati creata in precedenza: [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
> * Applicazione client abilitata per l'SDK di riconoscimento vocale: [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con Speech SDK (anteprima)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Facoltativo: iniziare rapidamente

Questo articolo descrive in modo dettagliato come creare un'applicazione client per comunicare con l'applicazione dei comandi personalizzati. Se si preferisce iniziare subito, il codice sorgente completo e pronto per la compilazione usato in questo articolo è disponibile negli [esempi di riconoscimento vocale](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Soddisfare il payload JSON

1. Aprire l'applicazione comandi personalizzati creata in precedenza dalle [guide introduttive: creare un comando personalizzato con i parametri](./quickstart-custom-speech-commands-create-parameters.md)
1. Controllare la sezione **regole di completamento** per assicurarsi che la regola creata in precedenza risponda all'utente
1. Per inviare un payload direttamente al client, creare una nuova regola con un'azione Invia attività

   > [!div class="mx-imgBorder"]
   > ![Regola di completamento dell'attività di invio](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Nome regola | UpdateDeviceState | Nome che descrive lo scopo della regola |
   | Condizioni | Parametro obbligatorio- `OnOff` e`SubjectDevice` | Condizioni che determinano quando la regola può essere eseguita |
   | Azioni | `SendActivity`(vedere di seguito) | Azione da eseguire quando la condizione della regola è true |

1. Copiare il codice JSON seguente nel **contenuto dell'attività**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
   > [!div class="mx-imgBorder"]
   > ![Payload dell'attività Send](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

## <a name="create-visuals-for-device-on-or-off-state"></a>Creare oggetti visivi per lo stato del dispositivo

In [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con l'SDK di riconoscimento vocale](./quickstart-custom-speech-commands-speech-sdk.md), è stata creata un'applicazione client di `turn on the tv`riconoscimento `turn off the fan`vocale che gestiva i comandi, ad esempio,. Con alcuni oggetti visivi aggiunti, è possibile visualizzare il risultato di tali comandi.

Aggiungere caselle con etichetta con testo che indica **on** o **off** usando il codice XML seguente aggiunto a`MainPage.xaml`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Gestisci payload personalizzabile
### <a name="add-reference-libraries"></a>Aggiungi librerie di riferimento

Poiché è stato creato un payload JSON, è necessario aggiungere un riferimento alla libreria [JSON.NET](https://www.newtonsoft.com/json) per gestire la deserializzazione.
- Right-client la soluzione.
- Scegliere **Gestisci pacchetti NuGet per la soluzione**, selezionare **Installa** 
- Cercare **Newtonsoft. JSON** nell'elenco di aggiornamento, aggiornare **Microsoft. NETCore. UniversalWindowsPlatform** alla versione più recente

> [!div class="mx-imgBorder"]
> ![Payload dell'attività Send](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

In ' MainPage. XAML. cs ' aggiungere
- `using Newtonsoft.Json;` 
- `using Windows.ApplicationModel.Core;`

### <a name="handle-received-payload"></a>Handle payload ricevuto

In `InitializeDialogServiceConnector`sostituire il gestore `ActivityReceived` eventi con il codice seguente. Il gestore `ActivityReceived` eventi modificato estrae il payload dall'attività e modifica di conseguenza lo stato di visualizzazione del televisore o della ventola.

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
   > ![Payload dell'attività Send](media/custom-speech-commands/fulfill-sdk-turn-on-tv.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)](./how-to-custom-speech-commands-validations.md)
