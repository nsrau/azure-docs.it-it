---
title: "Procedura: completare comandi personalizzati sul client con l'SDK di riconoscimento vocale (anteprima)"
titleSuffix: Azure Cognitive Services
description: In questo articolo si gestiscono le attività dei comandi personalizzati sul client con l'SDK di riconoscimento vocale
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: a986da74a668075457e28a9a37b6a11fd04a84e4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507022"
---
# <a name="how-to-fulfill-commands-on-the-client-with-the-speech-sdk-preview"></a>Procedura: eseguire l'evasione dei comandi sul client con l'SDK di riconoscimento vocale (anteprima)

Per completare le attività usando un'applicazione di comandi personalizzati è possibile inviare payload personalizzati a un dispositivo client connesso.

In questo articolo verranno illustrate le operazioni seguenti:

- Definire e inviare un payload JSON personalizzato dall'applicazione comandi personalizzati
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un' C# applicazione client UWP Speech SDK

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Una chiave di sottoscrizione di Azure per servizi vocali
   - [Ottenerne uno](get-started.md) gratuitamente o crearlo nel [portale di Azure](https://portal.azure.com)
- App comandi personalizzati creata in precedenza
   - [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
- Applicazione client abilitata per l'SDK vocale
   - [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con Speech SDK (anteprima)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Facoltativo: iniziare rapidamente

Questo articolo descrive in modo dettagliato come creare un'applicazione client per comunicare con l'applicazione dei comandi personalizzati. Se si preferisce iniziare subito, il codice sorgente completo e pronto per la compilazione usato in questo articolo è disponibile negli [esempi di riconoscimento vocale](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Soddisfare il payload JSON

1. Aprire l'applicazione dei comandi personalizzati creata in precedenza da [speech studio](https://speech.microsoft.com/)
1. Controllare la sezione **regole di completamento** per assicurarsi che la regola creata in precedenza risponda all'utente
1. Per inviare un payload direttamente al client, creare una nuova regola con un'azione Invia attività
   > [!div class="mx-imgBorder"]
   > ![regola di completamento dell'attività di invio](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Impostazione    | Valore consigliato                                  | Descrizione                                        |
   | ---------- | ------------------------------------------------ | -------------------------------------------------- |
   | Nome regola  | UpdateDeviceState                                | Nome che descrive lo scopo della regola          |
   | Condizioni | Parametro obbligatorio-`OnOff` e `SubjectDevice` | Condizioni che determinano quando la regola può essere eseguita    |
   | Azioni    | `SendActivity` (vedere di seguito)                        | Azione da eseguire quando la condizione della regola è true |

   > [!div class="mx-imgBorder"]
   > payload dell'attività di invio ![](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Creare oggetti visivi per lo stato del dispositivo

In [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con Speech SDK (anteprima)](./quickstart-custom-speech-commands-speech-sdk.md) è stata creata un'applicazione client di riconoscimento vocale che gestiva i comandi, ad esempio `turn on the tv``turn off the fan`. Aggiungere ora alcuni oggetti visivi in modo che sia possibile visualizzare il risultato di tali comandi.

Aggiungere caselle con etichetta con testo che indica **on** o **off** usando il codice XML seguente aggiunto a `MainPage.xaml.cs`

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

Ora che è stato creato un payload JSON, è possibile aggiungere un riferimento alla libreria [JSON.NET](https://www.newtonsoft.com/json) per gestire la deserializzazione.

> [!div class="mx-imgBorder"]
> payload dell'attività di invio ![](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

In `InitializeDialogServiceConnector` aggiungere quanto segue al gestore dell'evento `ActivityReceived`. Il codice aggiuntivo estrae il payload dall'attività e modifica di conseguenza lo stato di visualizzazione del televisore o della ventola.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var payload = activity?.Value;

    if(payload?.name == "SetDeviceState")
    {
        var state = payload?.state;
        var device = payload?.device;
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

## <a name="try-it-out"></a>Prova

1. Avviare l'applicazione
1. Selezionare Abilita microfono
1. Selezionare il pulsante Talk
1. Pronuncia `turn on the tv`
1. Lo stato di visualizzazione della TV deve cambiare in "on"

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)](./how-to-custom-speech-commands-validations.md)

