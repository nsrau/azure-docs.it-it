---
title: Estendere Azure IoT Central con regole e notifiche personalizzate. Documenti Microsoft
description: In qualità di sviluppatore di soluzioni, configurare un'applicazione IoT Central per l'invio di notifiche tramite posta elettronica quando un dispositivo interrompe l'invio di dati di telemetria. Questa soluzione usa Analisi di flusso di Azure, Funzioni di Azure e SendGrid.This solution uses Azure Stream Analytics, Azure Functions, and SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158147"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Estendere Azure IoT Central con regole personalizzate usando Analisi di flusso, Funzioni di Azure e SendGridExtend Azure IoT Central with custom rules using Stream Analytics, Azure Functions, and SendGrid



Questa guida alle procedure illustra, in qualità di sviluppatore di soluzioni, come estendere l'applicazione IoT Central con regole e notifiche personalizzate. L'esempio mostra l'invio di una notifica a un operatore quando un dispositivo interrompe l'invio di dati di telemetria. La soluzione usa una query di [Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/) di Azure per rilevare quando un dispositivo ha interrotto l'invio di dati di telemetria. Il processo Analisi flusso usa Funzioni di [Azure](https://docs.microsoft.com/azure/azure-functions/) per inviare messaggi di posta elettronica di notifica tramite [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Questa guida alle procedure illustra come estendere IoT Central oltre a ciò che può già fare con le regole e le azioni incorporate.

In questa guida alle procedure viene illustrato come:

* Trasmettere in flusso i dati di telemetria da un'applicazione IoT Central usando *l'esportazione continua dei dati.*
* Creare una query di Analisi di flusso che rileva quando un dispositivo ha interrotto l'invio di dati.
* Inviare una notifica tramite posta elettronica usando i servizi Funzioni di Azure e SendGrid.Send an email notification using the Azure Functions and SendGrid services.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central nel sito Web di gestione delle applicazioni di Azure IoT Central con le impostazioni seguenti:Create an IoT Central application on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Piano tariffario | Standard |
| Modello di applicazione | Analisi in negozio – monitoraggio delle condizioni |
| Nome applicazione | Accettare l'impostazione predefinita o scegliere il proprio nome |
| URL | Accettare il valore predefinito o scegliere il proprio prefisso URL univoco |
| Directory | Il tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | La regione più vicina |

Gli esempi e le schermate in questo articolo usano l'area **degli Stati Uniti.The** examples and screenshots in this article use the United States region. Scegli una posizione vicina a te e assicurati di creare tutte le tue risorse nella stessa area.

Questo modello di applicazione include due dispositivi termostato simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Resource group

Usare il portale di [Azure per creare un gruppo](https://portal.azure.com/#create/Microsoft.ResourceGroup) di risorse denominato **DetectStoppedDevices** per contenere le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central.Create your Azure resources in the same location as your IoT Central application.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il portale di Azure per creare uno spazio dei nomi Hub eventi con le impostazioni seguenti:Use the [Azure portal to create an Event Hubs namespace](https://portal.azure.com/#create/Microsoft.EventHub) with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Nome    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Subscription | Sottoscrizione in uso |
| Resource group | Dispositivi DetectStopped |
| Location | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="stream-analytics-job"></a>Processo di Analisi di flusso

Usare il portale di [Azure per creare un processo di Analisi di flusso](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) con le impostazioni seguenti:Use the Azure portal to create a Stream Analytics job with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Nome    | Scegli il tuo nome di lavoro |
| Subscription | Sottoscrizione in uso |
| Resource group | Dispositivi DetectStopped |
| Location | Stati Uniti orientali |
| Ambiente di hosting | Cloud |
| Unità di streaming | 3 |

### <a name="function-app"></a>App per le funzioni

Usare il portale di [Azure per creare un'app per](https://portal.azure.com/#create/Microsoft.FunctionApp) le funzioni con le impostazioni seguenti:Use the Azure portal to create a function app with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Nome app    | Scegliere il nome dell'app per le funzioni |
| Subscription | Sottoscrizione in uso |
| Resource group | Dispositivi DetectStopped |
| OS | WINDOWS |
| Piano di hosting | Piano a consumo |
| Location | Stati Uniti orientali |
| Stack di runtime | .NET |
| Archiviazione | Creare un nuovo gruppo di risorse |

### <a name="sendgrid-account"></a>Account SendGrid

Usare il portale di Azure per creare un account SendGrid con le impostazioni seguenti:Use the [Azure portal to create a SendGrid account](https://portal.azure.com/#create/Sendgrid.sendgrid) with the following settings:

| Impostazione | valore |
| ------- | ----- |
| Nome    | Scegliere il nome dell'account SendGrid |
| Password | Creare una password |
| Subscription | Sottoscrizione in uso |
| Resource group | Dispositivi DetectStopped |
| Piano tariffario | F1 Gratuito |
| Informazioni contatto | Compilare le informazioni richieste |

Dopo aver creato tutte le risorse necessarie, il gruppo di risorse DetectStoppedDevices è simile alla schermata seguente:When you've created all the required resources, your **DetectStoppedDevices** resource group looks like the following screenshot:

![Gruppo di risorse Rileva dispositivi arrestati](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere dati di telemetria dall'applicazione IoT Central.In this section, you create an event hub to receive telemetry from your IoT Central application. L'hub eventi fornisce i dati di telemetria al processo di Analisi di flusso per l'elaborazione.

1. Nel portale di Azure passare allo spazio dei nomi Hub eventi e selezionare **Hub eventi.**
1. Denominare l'hub eventi **centralexport**e selezionare **Crea**.

Lo spazio dei nomi Hub eventi è simile alla schermata seguente:Your Event Hubs namespace looks like the following screenshot:

![Spazio dei nomi di Hub eventi](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Ottenere la chiave dell'API SendGridGet SendGrid API key

L'app per le funzioni richiede una chiave dell'API SendGrid per inviare messaggi di posta elettronica. Per creare una chiave API SendGrid:To create a SendGrid API key:

1. Nel portale di Azure passare all'account SendGrid.In the Azure portal, navigate to your SendGrid account. Quindi scegliere **Gestisci** per accedere all'account SendGrid.
1. Nell'account SendGrid scegliere **Impostazioni**, quindi **Chiavi API**. Scegliere **Crea chiave API:**

    ![Creare la chiave dell'API SendGridCreate SendGrid API key](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Nella pagina **Crea chiave API** creare una chiave denominata **AzureFunctionAccess** con autorizzazioni **di accesso completo.**
1. Prendere nota della chiave API, è necessario quando si configura l'app per le funzioni.

## <a name="define-the-function"></a>Definire la funzione

Questa soluzione usa un'app Funzioni di Azure per inviare una notifica tramite posta elettronica quando il processo analisi di flusso rileva un dispositivo arrestato. Per creare l'app per le funzioni:

1. Nel portale di Azure passare all'istanza del **servizio app** nel gruppo di risorse DetectStoppedDevices.In the Azure portal, navigate to the App Service instance in the **DetectStoppedDevices** resource group.
1. Selezionare **+** per creare una nuova funzione.
1. Nella pagina **CHOOSE A DEVELOPMENT ENVIRONMENT** scegliere **In-portal (In-portal),** quindi selezionare **Continue (Continua).**
1. Nella pagina **CREATE A FUNCTION** scegliere **Webhook e API,** quindi selezionare **Create**.

Il portale crea una funzione predefinita denominata **HttpTrigger1**:

![Funzione trigger HTTP predefinita](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurare le associazioni di funzioni

Per inviare messaggi di posta elettronica con SendGrid, è necessario configurare i binding per la funzione come segue:

1. Selezionare **Integra**, scegliere l'output **HTTP ($return)**, quindi selezionare **Elimina**.
1. Fare clic su **Nuovo output**, quindi su **SendGrid**e infine scegliere **Seleziona**. Scegliere **Installa** per installare l'estensione SendGrid.
1. Al termine dell'installazione, selezionare **Usa valore restituito funzione**. Aggiungere un **indirizzo A** valido per ricevere le notifiche tramite posta elettronica.  Aggiungere un **indirizzo mittente** valido da utilizzare come mittente dell'e-mail.
1. Selezionare **Nuovo** accanto a **SendGrid API Key App Setting**. Immettere **SendGridAPIKey** come chiave e la chiave API SendGrid annotata in precedenza come valore. Quindi selezionare **Crea**.
1. Scegliere **Salva** per salvare le associazioni SendGrid per la funzione.

Le impostazioni di integrazione simile alla schermata seguente:The integrate settings look like the following screenshot:

![Integrazioni di app per le funzioni](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Aggiungere il codice funzione

Per implementare la funzione, aggiungere il codice C , per analizzare la richiesta HTTP in ingresso e inviare i messaggi di posta elettronica come segue:To implement your function, add the C's code to parse the incoming HTTP request and send the emails as follows:

1. Scegliere la funzione **HttpTrigger1** nell'app per le funzioni e sostituire il codice C' con il codice seguente:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    È possibile che venga visualizzato un messaggio di errore fino a quando non si salva il nuovo codice.

1. Selezionare **Salva** per salvare la funzione.

### <a name="test-the-function-works"></a>Testare il funzionamento della funzione

Per testare la funzione nel portale, scegliere innanzitutto **Log** nella parte inferiore dell'editor di codice. Scegliere **quindi Test** a destra dell'editor di codice. Usare il codice JSON seguente come corpo della **richiesta:Use**the following JSON as the Request body:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

I messaggi del registro funzioni vengono visualizzati nel pannello **Registri:**

![Output del log delle funzioni](media/howto-create-custom-rules/function-app-logs.png)

Dopo alcuni minuti, l'indirizzo di posta elettronica **A** riceve un messaggio di posta elettronica con il contenuto seguente:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Aggiungi query di Analisi di flusso

Questa soluzione usa una query di Analisi di flusso per rilevare quando un dispositivo interrompe l'invio di dati di telemetria per più di 120 secondi. La query usa i dati di telemetria dall'hub eventi come input. Il processo invia i risultati della query all'app per le funzioni. In questa sezione viene configurato il processo Analisi di flusso:In this section, you configure the Stream Analytics job:

1. Nel portale di Azure passare al processo Analisi di flusso, in **Topologia processi** selezionare **Input**, scegliere **Aggiungi input flusso**e quindi Hub **eventi**.
1. Usare le informazioni nella tabella seguente per configurare l'input usando l'hub eventi creato in precedenza, quindi scegliere **Salva:**

    | Impostazione | valore |
    | ------- | ----- |
    | Alias di input | centraltelemetry |
    | Subscription | Sottoscrizione in uso |
    | Spazio dei nomi dell'hub eventi | Lo spazio dei nomi dell'hub eventi |
    | Nome dell'hub eventi | Usa esistente - **centralexport** |

1. In **Topologia processi**, selezionare **Output**, scegliere **Aggiungi**e quindi Scegliere Funzione **di Azure**.
1. Usare le informazioni nella tabella seguente per configurare l'output, quindi scegliere **Salva:**

    | Impostazione | valore |
    | ------- | ----- |
    | Alias di output | emailnotification (notifica e-mail) |
    | Subscription | Sottoscrizione in uso |
    | App per le funzioni | La tua app per le funzioni |
    | Funzione  | HttpTrigger1 (informazioni in due) |

1. In **Topologia processi**selezionare **Query** e sostituire la query esistente con il codice SQL seguente:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Selezionare **Salva**.
1. Per avviare il processo analisi di flusso, scegliere **Panoramica**, quindi **Start**, **ora**, quindi **Start**:

    ![Analisi di flusso](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

Nel sito Web di gestione delle applicazioni di Azure IoT Central passare all'applicazione IoT Central creata dal modello Contoso.On the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, navigate to the IoT Central application you created from the Contoso template. In questa sezione viene configurata l'applicazione per lo streaming dei dati di telemetria dai relativi dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **Esportazione dati,** selezionare **Nuovo**e quindi **Hub eventi**di Azure .
1. Utilizzare le seguenti impostazioni per configurare l'esportazione, quindi selezionare **Salva:**

    | Impostazione | valore |
    | ------- | ----- |
    | Nome visualizzato | Esportazione in hub eventi |
    | Attivato | Attivato |
    | Spazio dei nomi di Hub eventi | Nome dello spazio dei nomi Hub per gli hub di eventi |
    | Hub eventi | centralexport |
    | Misure | Attivato |
    | Dispositivi | Disattivato |
    | Modelli di dispositivo | Disattivato |

![Configurazione dell'esportazione continua dei dati](media/howto-create-custom-rules/cde-configuration.png)

Attendere che lo stato dell'esportazione sia **In esecuzione** prima di continuare.

## <a name="test"></a>Test

Per testare la soluzione, è possibile disabilitare l'esportazione continua dei dati da IoT Central ai dispositivi arrestati simulati:

1. Nell'applicazione IoT Central passare alla pagina **Esportazione dati** e selezionare la configurazione di esportazione Esporta in **hub eventi.**
1. Impostare **Abilitato** su **Disattivato** e scegliere **Salva**.
1. Dopo almeno due minuti, l'indirizzo di posta elettronica **A** riceve uno o più messaggi di posta elettronica simili al contenuto di esempio seguente:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare costi non necessari, eliminare il gruppo di risorse DetectStoppedDevices nel portale di Azure.To tidy up after this how-to and and avoid unnecessary costs, delete the **DetectStoppedDevices** resource group in the Azure portal.

È possibile eliminare l'applicazione IoT Central dalla pagina **Gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere in flusso i dati di telemetria da un'applicazione IoT Central usando *l'esportazione continua dei dati.*
* Creare una query di Analisi di flusso che rileva quando un dispositivo ha interrotto l'invio di dati.
* Inviare una notifica tramite posta elettronica usando i servizi Funzioni di Azure e SendGrid.Send an email notification using the Azure Functions and SendGrid services.

Ora che sai come creare regole e notifiche personalizzate, il passaggio successivo suggerito consiste nell'imparare a [estendere Azure IoT Central con l'analisi personalizzata.](howto-create-custom-analytics.md)
