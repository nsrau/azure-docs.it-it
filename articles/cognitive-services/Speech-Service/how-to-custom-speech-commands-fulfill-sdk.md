---
title: Come soddisfare i comandi da un client con Speech SDK
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come gestire le attività dei comandi personalizzati in un client con Speech SDK.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367748"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Eseguire il completamento dei comandi da un client con Speech SDK (Anteprima)Fulfill commands from a client with the Speech SDK (Preview)

Per completare le attività usando un'applicazione Comandi personalizzati, è possibile inviare payload personalizzati a un dispositivo client connesso.

In questo articolo:

- Definire e inviare un payload JSON personalizzato dall'applicazione Comandi personalizzatiDefine and send a custom JSON payload from your Custom Commands application
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un'applicazione client dell'SDK di riconoscimento UWP di C

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Una chiave di sottoscrizione di Azure per il servizio di riconoscimento vocaleAn Azure subscription key for Speech service
  - [Ottienine uno gratuitamente](get-started.md) o crealo nel portale di [Azure](https://portal.azure.com)
- Un'app Comandi personalizzati creata in precedenza
  - [Guida introduttiva: Creare un comando personalizzato con parametri (anteprima)Quickstart: Create a Custom Command with Parameters (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
- Un'applicazione client abilitata per Speech SDK
  - [Guida introduttiva: Connettersi a un'applicazione di comando personalizzato con Speech SDK (Anteprima)Quickstart: Connect to a Custom Command application with the Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Facoltativo: iniziare velocemente

In questo articolo viene descritto, passo dopo passo, come creare un'applicazione client per comunicare con l'applicazione comandi personalizzati. Se si preferisce immergersi direttamente, il codice sorgente completo e pronto per la compilazione utilizzato in questo articolo è disponibile in [Speech SDK Samples](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Soddisfare con payload JSON

1. Aprire l'applicazione Comandi personalizzati creata in precedenza da [Speech Studio](https://speech.microsoft.com/)
1. Controlla la sezione Regole di **completamento** per assicurarti di avere la regola creata in precedenza che risponde all'utente
1. Per inviare un payload direttamente al client, creare una nuova regola con un'azione Invia attivitàTo send a payload directly to the client, create a new rule with a Send Activity action

   > [!div class="mx-imgBorder"]
   > ![Regola di completamento Invia attività](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Nome regola | UpdateDeviceState (Stati) di UpdateDevice | Un nome che descriva lo scopo della regola |
   | Condizioni | Parametro `OnOff` obbligatorio - e`SubjectDevice` | Condizioni che determinano quando la regola può essere eseguita |
   | Azioni | `SendActivity`(vedi sotto) | Azione da eseguire quando la condizione della regola è vera |

   > [!div class="mx-imgBorder"]
   > ![Payload attività di invio](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Creare oggetti visivi per lo stato attivo o disattivato del dispositivoCreate visuals for device on or off state

In [Guida introduttiva: Connettersi a un'applicazione di comandi personalizzati con Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md) è stata creata un'applicazione client Speech SDK che gestisce comandi come `turn on the tv`, `turn off the fan`. Ora aggiungi alcuni oggetti visivi in modo da poter vedere il risultato di questi comandi.

Aggiungere caselle con etichetta con testo che indica **On** o **Off** utilizzando il seguente codice XML aggiunto a`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>Gestire il carico utile personalizzabile

Dopo aver creato un payload JSON, è possibile aggiungere un riferimento alla libreria [JSON.NET](https://www.newtonsoft.com/json) per gestire la deserializzazione.

> [!div class="mx-imgBorder"]
> ![Payload attività di invio](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

In `InitializeDialogServiceConnector` aggiungere quanto `ActivityReceived` segue al gestore eventi. Il codice aggiuntivo estrarrà il payload dall'attività e modificherà lo stato di visualizzazione della tv o del ventilatore di conseguenza.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Provare il servizio

1. Avviare l'applicazione
1. Selezionare Abilita microfono
1. Seleziona il pulsante Parla
1. Dire`turn on the tv`
1. Lo stato di visualizzazione della tv dovrebbe cambiare in "On"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere convalide ai parametri di comando personalizzato (anteprima)How to: Add convalids to Custom Command parameters (preview)](./how-to-custom-speech-commands-validations.md)
