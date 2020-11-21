---
title: Configurare endpoint Web (anteprima)
titleSuffix: Azure Cognitive Services
description: configurare endpoint Web per comandi personalizzati
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: e50d7aba5cc5b3d5d620d844cc9ad169ad8b3bf6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025892"
---
# <a name="set-up-web-endpoints"></a>Configurare endpoint Web

Questo articolo illustra come configurare gli endpoint Web in un'applicazione di comandi personalizzati che consentono di effettuare richieste HTTP da un'applicazione client. Verranno completate le attività seguenti:

- Configurare gli endpoint Web in un'applicazione di comandi personalizzati
- Chiamare gli endpoint Web in un'applicazione di comandi personalizzati
- Ricevere la risposta degli endpoint Web 
- Integrare la risposta degli endpoint Web in un payload JSON personalizzato, inviarla e visualizzarla in un'applicazione client Speech SDK della piattaforma UWP in C#

## <a name="prerequisites"></a>Prerequisiti
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * Una chiave di sottoscrizione di Azure per il servizio Voce: È possibile [ottenerne una gratuitamente](overview.md#try-the-speech-service-for-free) o crearla nel [portale di Azure](https://portal.azure.com)
> * Un'[app di comandi personalizzati creata](quickstart-custom-commands-application.md) in precedenza
> * Un'app client abilitata per Speech SDK: [Procedura: Inviare attività a un'applicazione client](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>Configurare gli endpoint Web

1. Aprire l'applicazione di comandi personalizzati creata in precedenza. 
1. Passare a "Web endpoint" (Endpoint Web) e fare clic su "New web endpoint" (Nuovo endpoint Web).

   > [!div class="mx-imgBorder"]
   > ![Nuovo endpoint Web](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Nome | UpdateDeviceState | Il nome dell'endpoint Web. |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | L'URL dell'endpoint da mettere in comunicazione con l'app di comandi personalizzati. |
   | Metodo | POST | Le interazioni consentite, ad esempio GET o POST, con l'endpoint.|
   | Intestazioni | Chiave: app, Valore: usare le prime 8 cifre dell'ID applicazione | I parametri dell'intestazione da includere nell'intestazione della richiesta.|

    > [!NOTE]
    > - L'endpoint Web di esempio, creato con [Funzioni di Azure](../../azure-functions/index.yml), si collega al database e salva lo stato dispositivo della TV e del ventilatore
    > - L'intestazione suggerita è necessaria solo per l'endpoint di esempio
    > - Per assicurarsi che il valore dell'intestazione sia univoco nell'endpoint di esempio, usare le prime 8 cifre dell'ID applicazione
    > - Nella realtà, l'endpoint Web può essere l'endpoint per l'[hub IoT](../../iot-hub/about-iot-hub.md) che gestisce i dispositivi

1. Fare clic su **Salva**.

## <a name="call-web-endpoints"></a>Chiamare gli endpoint Web

1. Passare al comando **TurnOnOff**, selezionare **ConfirmationResponse** nella regola di completamento, quindi selezionare **Aggiungi un'azione**.
1. In **Nuova azione - Tipo** selezionare **Call web endpoint** (Chiamata a endpoint Web)
1. In **Modifica azione - Endpoint** selezionare **UpdateDeviceState**, ovvero l'endpoint Web creato.  
1. In **Configurazione** inserire i valori seguenti: 
   > [!div class="mx-imgBorder"]
   > ![Parametri dell'azione di chiamata a endpoint Web](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Endpoint | UpdateDeviceState | L'endpoint Web da chiamare in questa azione. |
   | Parametri di query | item={SubjectDevice}&&value={OnOff} | I parametri di query da aggiungere alla fine dell'URL dell'endpoint.  |
   | Contenuto del corpo | N/D | Il contenuto del corpo della richiesta. |

    > [!NOTE]
    > - I parametri di query suggeriti sono necessari solo per l'endpoint di esempio

1. Per **In caso di esito positivo - Action to execute** (Azione da eseguire) selezionare **Send speech response** (Invia risposta vocale).
    
    In **Simple editor** (Editor semplice) immettere `{SubjectDevice} is {OnOff}`.
   
   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra la schermata in seguito alla riuscita-azione da eseguire.](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Azione da eseguire | Send speech response (Invia risposta vocale) | Azione da eseguire in caso di esito positivo della richiesta effettuata all'endpoint Web |
   
   > [!NOTE]
   > - È anche possibile accedere direttamente ai campi della risposta HTTP usando `{YourWebEndpointName.FieldName}`. ad esempio `{UpdateDeviceState.TV}`

1. Per **In caso di esito negativo - Action to execute** (Azione da eseguire) selezionare **Send speech response** (Invia risposta vocale)

    In **Simple editor** (Editor semplice) immettere `Sorry, {WebEndpointErrorMessage}`.

   > [!div class="mx-imgBorder"]
   > ![Azione di chiamata a endpoint Web in caso di esito negativo](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Azione da eseguire | Send speech response (Invia risposta vocale) | Azione da eseguire in caso di esito negativo della richiesta all'endpoint Web |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` è facoltativo. Se non si vuole esporre alcun messaggio di errore, è possibile rimuoverlo.
   > - All'interno dell'endpoint di esempio viene restituita la risposta HTTP con messaggi dettagliati per gli errori comuni, ad esempio parametri di intestazione mancanti. 

### <a name="try-it-out-in-test-portal"></a>Provare nel portale di test
- Risposta in caso di esito positivo\
Salvare, eseguire il training e testare
   > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra l'esito positivo della risposta.](media/custom-commands/setup-web-endpoint-on-success-response.png)
- Risposta in caso di esito negativo\
Rimuovere uno dei parametri di query, salvare, ripetere il training e testare
   > [!div class="mx-imgBorder"]
   > ![Azione di chiamata a endpoint Web in caso di esito positivo](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>Integrazione con l'applicazione client

In [Procedura: Inviare attività all'applicazione client (anteprima)](./how-to-custom-commands-send-activity-to-client.md) è stata aggiunta un'azione **Send activity to client** (Invia attività a client). L'attività viene inviata all'applicazione client indipendentemente dall'esito positivo o negativo dell'azione **Call Web endpoint** (Chiamata a endpoint Web).
Tuttavia, nella maggior parte dei casi è preferibile che l'attività venga inviata all'applicazione client solo quando la chiamata all'endpoint Web ha esito positivo. In questo esempio si tratta del momento in cui lo stato del dispositivo viene aggiornato correttamente.

1. Eliminare l'azione **Send activity to client** (Invia attività a client) aggiunta in precedenza.
1. Modificare la chiamata all'endpoint Web: 
    1. In **Configurazione** assicurarsi che l'opzione **Parametri di query** sia impostata su `item={SubjectDevice}&&value={OnOff}`
    1. Per **In caso di esito positivo** impostare **Action to execute** (Azione da eseguire) su **Send activity to client** (Invia attività a client)
    1. Copiare il codice JSON seguente in **Activity Content** (Contenuto attività)
   ```json
   {
      "type": "event",
      "name": "UpdateDeviceState",
      "value": {
        "state": "{OnOff}",
        "device": "{SubjectDevice}"
      }
    }
   ```
   
Ora l'attività viene inviata al client solo quando la richiesta all'endpoint Web ha esito positivo.

### <a name="create-visuals-for-syncing-device-state"></a>Creare oggetti visivi per sincronizzare lo stato del dispositivo
Aggiungere il codice XML seguente a `MainPage.xaml` sopra il blocco `"EnableMicrophoneButton"`.

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>Sincronizzare lo stato del dispositivo 

In `MainPage.xaml.cs` aggiungere il riferimento `using Windows.Web.Http;`. Aggiungere il codice seguente alla classe `MainPage` . Questo metodo invierà una richiesta GET all'endpoint di esempio ed estrarrà lo stato corrente del dispositivo per l'app. Assicurarsi di sostituire `<your_app_name>` con il valore usato nell'**intestazione** nell'endpoint Web di comandi personalizzati

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
1. Fare clic su Sync Device State (Sincronizza stato dispositivo).\
Se l'app è stata testata con `turn on tv` nella sezione precedente, lo stato della TV sarà "on".
    > [!div class="mx-imgBorder"]
    > ![Sincronizzare lo stato del dispositivo](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. Selezionare Abilita microfono
1. Selezionare il pulsante Talk (Parla)
1. Pronunciare la frase `turn on the fan`
1. Lo stato visivo del ventilatore cambierà in "on"
    > [!div class="mx-imgBorder"]
    > ![Attivare il ventilatore](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esporta l'applicazione comandi personalizzati come abilità remota](./how-to-custom-commands-integrate-remote-skills.md)

