---
title: Configurare gli endpoint Web (anteprima)
titleSuffix: Azure Cognitive Services
description: configurare gli endpoint Web per i comandi personalizzati
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: eb2a7d4f83b3d8bda0d06e14b4dab9bb4872885e
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85414284"
---
# <a name="set-up-web-endpoints"></a>Configurare endpoint Web

In questo articolo si apprenderà come configurare gli endpoint Web in un'applicazione di comandi personalizzati che consente di effettuare richieste HTTP da un'applicazione client. Si completeranno le attività seguenti:

- Configurare gli endpoint Web nell'applicazione comandi personalizzati
- Chiamare endpoint Web nell'applicazione comandi personalizzati
- Ricevere la risposta degli endpoint Web 
- Integrare la risposta degli endpoint Web in un payload JSON personalizzato, inviarlo e visualizzarlo da un'applicazione client C# UWP Speech SDK

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Una chiave di sottoscrizione di Azure per il servizio di riconoscimento vocale: [ottenerne una gratuita](get-started.md) o crearla nel [portale di Azure](https://portal.azure.com)
> * [App comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza
> * App client abilitata per l'SDK di riconoscimento vocale: [procedura: terminare l'attività nell'applicazione client](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Configurare gli endpoint Web

1. Aprire l'applicazione Custom Commands creata in precedenza. 
1. Passare a "endpoint Web" e fare clic su "nuovo endpoint Web".

   > [!div class="mx-imgBorder"]
   > ![Nuovo endpoint Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Nome | UpdateDeviceState | Nome dell'endpoint Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | URL dell'endpoint con cui si vuole comunicare l'app del comando personalizzata. |
   | Metodo | POST | Interazioni consentite, ad esempio GET, POST, con l'endpoint.|
   | Intestazioni | Chiave: app, valore: un nome univoco per l'app | Parametri dell'intestazione da includere nell'intestazione della richiesta.|

    > [!NOTE]
    > - L'endpoint Web di esempio creato con la [funzione di Azure](https://docs.microsoft.com/azure/azure-functions/), che si collega al database che salva lo stato del dispositivo del televisore e della ventola
    > - L'intestazione suggerita è necessaria solo per l'endpoint di esempio
    > - Nel mondo reale, l'endpoint Web può essere l'endpoint per l' [Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) delle cose che gestisce i dispositivi

1. Fare clic su **Save**.

## <a name="call-web-endpoints"></a>Chiama endpoint Web

1. Passare a **TurnOnOff** comando, selezionare **ConfirmationResponse** in regola di completamento, quindi selezionare **Aggiungi un'azione**.
1. In **nuovo tipo di azione**selezionare **chiama endpoint Web**
1. In **Edit action-Endpoints**selezionare **UpdateDeviceState**, che è l'endpoint Web creato.  
1. In **configurazione**inserire i valori seguenti: 
   > [!div class="mx-imgBorder"]
   > ![Chiama parametri azione endpoint Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Endpoint | UpdateDeviceState | Endpoint Web che si desidera chiamare in questa azione. |
   | Parametri di query | Item = {SubjectDevice} &&valore = {OnOff} | Parametri di query da accodare all'URL dell'endpoint Web.  |
   | Contenuto del corpo | N/D | Contenuto del corpo della richiesta. |

    > [!NOTE]
    > - I parametri di query suggeriti sono necessari solo per l'endpoint di esempio

1. In **in esito positivo-azione da eseguire**selezionare **Invia risposta vocale**.
   
   > [!div class="mx-imgBorder"]
   > ![Chiama l'azione endpoint Web per l'esito positivo](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Azione da eseguire | Invia risposta vocale | Azione da eseguire se la richiesta all'endpoint Web ha esito positivo |
   
   > [!NOTE]
   > - È anche possibile accedere direttamente ai campi nella risposta http usando `{YourWebEndpointName.FieldName}` . Ad esempio: `{UpdateDeviceState.TV}`

1. In **in caso di errore-azione da eseguire**selezionare **Invia risposta vocale**
   > [!div class="mx-imgBorder"]
   > ![Chiama l'azione endpoint Web in errore](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Azione da eseguire | Invia risposta vocale | Azione da eseguire se la richiesta all'endpoint Web ha esito negativo |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` è facoltativo. Se non si vuole esporre alcun messaggio di errore, è possibile rimuoverlo.
   > - All'interno dell'endpoint di esempio, viene restituita la risposta http con messaggi di errore dettagliati per gli errori comuni, ad esempio i parametri di intestazione mancanti. 

### <a name="try-it-out-in-test-portal"></a>Provare il portale di test
- In risposta riuscita \
Salva, Esegui il training e testa
   > [!div class="mx-imgBorder"]
   > ![Chiama l'azione endpoint Web per l'esito positivo](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Risposta non riuscita \
Rimuovere uno dei parametri di query, Save, Retrain e test
   > [!div class="mx-imgBorder"]
   > ![Chiama l'azione endpoint Web per l'esito positivo](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrazione con l'applicazione client

In [procedura: inviare l'attività all'applicazione client (anteprima)](./how-to-custom-commands-send-activity-to-client.md)è stata aggiunta un' **attività Invia all'azione client** . L'attività viene inviata all'applicazione client indipendentemente dal fatto che l'azione dell' **endpoint Web** venga eseguita o meno.
Tuttavia, nella maggior parte dei casi si desidera inviare l'attività all'applicazione client solo quando la chiamata all'endpoint Web ha esito positivo. In questo esempio, si tratta del momento in cui lo stato del dispositivo è stato aggiornato correttamente.

1. Eliminare l' **attività Send all'azione client** aggiunta in precedenza.
1. Modifica endpoint Web di chiamata: 
    1. In **configurazione**verificare che i **parametri di query** siano`item={SubjectDevice}&&value={OnOff}`
    1. In **in esito positivo**, modificare l' **azione da eseguire** per **inviare l'attività al client**
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
    > ![Invia attività in seguito all'esito positivo](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
A questo punto si invia l'attività al client solo quando la richiesta all'endpoint Web ha esito positivo.

### <a name="create-visuals-for-syncing-device-state"></a>Creare oggetti visivi per sincronizzare lo stato del dispositivo
Aggiungere il seguente codice XML a `MainPage.xaml` sopra il `"EnableMicrophoneButton"` blocco.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Sincronizza stato del dispositivo 

In `MainPage.xaml.cs` aggiungere il riferimento `using Windows.Web.Http;` . Aggiungere il codice seguente alla classe `MainPage` . Questo metodo invierà una richiesta GET all'endpoint di esempio ed estrarà lo stato corrente del dispositivo per l'app. Assicurarsi di modificare `<your_app_name>` l'elemento usato nell' **intestazione** dell'endpoint Web del comando personalizzato

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>Procedura

1. Avviare l'applicazione
1. Fare clic su Sincronizza stato del dispositivo. \
Se l'app è stata sottoposta `turn on tv` a test nella sezione precedente, la TV verrà visualizzata come "on".
    > [!div class="mx-imgBorder"]
    > ![Sincronizza stato del dispositivo](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selezionare Abilita microfono
1. Selezionare il pulsante Talk
1. Ad esempio`turn on the fan`
1. Lo stato di visualizzazione della ventola verrà modificato in "on".
    > [!div class="mx-imgBorder"]
    > ![Attiva ventola](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Abilitare un processo di integrazione continua/distribuzione continua per l'applicazione comandi personalizzati](./how-to-custom-commands-deploy-cicd.md)