---
title: Estendere IoT Central di Azure con regole e notifiche personalizzate | Microsoft Docs
description: Per gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per inviare notifiche tramite posta elettronica quando un dispositivo smette di inviare dati di telemetria. Questa soluzione USA analisi di flusso di Azure, funzioni di Azure e SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 19978fbaf0a817de6356c6b5da41691c42c4c172
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588486"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid-preview-features"></a>Estendi IoT Central di Azure con regole personalizzate usando analisi di flusso, funzioni di Azure e SendGrid (funzionalità di anteprima)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa guida illustra come uno sviluppatore di soluzioni, come estendere l'applicazione IoT Central con regole e notifiche personalizzate. Nell'esempio viene illustrato l'invio di una notifica a un operatore quando un dispositivo interrompe l'invio di dati di telemetria. La soluzione USA una query di [analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/) per rilevare quando un dispositivo ha interrotto l'invio della telemetria. Il processo di analisi di flusso usa [funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) per inviare messaggi di posta elettronica di notifica usando [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Questa guida dettagliata illustra come estendere IoT Central oltre quello che può già fare con le regole e le azioni predefinite.

In questa guida dettagliata si apprenderà come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare una query di analisi di flusso che rilevi quando un dispositivo ha interrotto l'invio dei dati.
* Inviare una notifica tramite posta elettronica usando funzioni di Azure e i servizi SendGrid.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire la procedura descritta in questa guida è necessaria una sottoscrizione di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Piano di pagamento | Pagamento in base al consumo |
| Modello di applicazione | Analisi in-Store-monitoraggio delle condizioni |
| Nome dell'applicazione | Accetta il nome predefinito o scegli il tuo nome |
| URL | Accettare l'impostazione predefinita o scegliere il prefisso URL univoco |
| Directory | Tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | Area geografica più vicina |

Gli esempi e le schermate in questo articolo usano l'area **Stati Uniti** . Scegliere una località vicina e assicurarsi di creare tutte le risorse nella stessa area.

Questo modello di applicazione include due dispositivi termotermostati simulati che inviano dati di telemetria.

### <a name="resource-group"></a>Resource group

Usare il [portale di Azure per creare un gruppo di risorse](https://portal.azure.com/#create/Microsoft.ResourceGroup) denominato **DetectStoppedDevices** per contenere le altre risorse create. Creare le risorse di Azure nello stesso percorso dell'applicazione IoT Central.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare il [portale di Azure per creare uno spazio dei nomi di hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Name    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Sottoscrizione | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Percorso | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="stream-analytics-job"></a>Processo di analisi di flusso

Usare il [portale di Azure per creare un processo di analisi di flusso](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Name    | Scegliere il nome del processo |
| Sottoscrizione | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Percorso | Stati Uniti orientali |
| Ambiente di hosting | Cloud |
| Unità di streaming | 3 |

### <a name="function-app"></a>App per le funzioni

Usare il [portale di Azure per creare un'app per le funzioni](https://portal.azure.com/#create/Microsoft.FunctionApp) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Nome app    | Scegliere il nome dell'app per le funzioni |
| Sottoscrizione | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| SO | Windows |
| Piano di hosting | Piano a consumo |
| Percorso | Stati Uniti orientali |
| Stack di runtime | .NET |
| Archiviazione | Creare un nuovo gruppo di risorse |

### <a name="sendgrid-account"></a>Account SendGrid

Usare il [portale di Azure per creare un account SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Name    | Scegliere il nome dell'account SendGrid |
| Password | Creare una password |
| Sottoscrizione | Sottoscrizione in uso |
| Resource group | DetectStoppedDevices |
| Piano tariffario | F1 Gratuito |
| Informazioni contatto | Fornisci le informazioni necessarie |

Quando sono state create tutte le risorse necessarie, il gruppo di risorse **DetectStoppedDevices** è simile allo screenshot seguente:

![Rileva il gruppo di risorse dei dispositivi arrestati](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente i dati di telemetria in un hub eventi. In questa sezione viene creato un hub eventi per ricevere i dati di telemetria dall'applicazione IoT Central. L'hub eventi recapita i dati di telemetria al processo di analisi di flusso per l'elaborazione.

1. Nella portale di Azure passare allo spazio dei nomi di hub eventi e selezionare **+ Hub eventi**.
1. Denominare il **centralexport**dell'hub eventi e selezionare **Crea**.

Lo spazio dei nomi di hub eventi è simile allo screenshot seguente:

![Spazio dei nomi di Hub eventi](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Ottenere la chiave API SendGrid

Per l'app per le funzioni è necessaria una chiave API SendGrid per l'invio di messaggi di posta elettronica. Per creare una chiave API SendGrid:

1. Nella portale di Azure passare all'account SendGrid. Quindi scegliere **Gestisci** per accedere all'account SendGrid.
1. Nell'account SendGrid scegliere **Impostazioni**, quindi **chiavi API**. Scegliere **Crea chiave API**:

    ![Crea chiave API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Nella pagina **Crea chiave API** creare una chiave denominata **AzureFunctionAccess** con autorizzazioni di **accesso completo** .
1. Prendere nota della chiave API, necessaria quando si configura l'app per le funzioni.

## <a name="define-the-function"></a>Definire la funzione

Questa soluzione usa un'app funzioni di Azure per inviare una notifica di posta elettronica quando il processo di analisi di flusso rileva un dispositivo interrotto. Per creare l'app per le funzioni:

1. Nella portale di Azure passare all'istanza del **servizio app** nel gruppo di risorse **DetectStoppedDevices** .
1. Selezionare **+** per creare una nuova funzione.
1. Nella pagina **scegliere un ambiente di sviluppo** scegliere **in-Portal** , quindi selezionare **continua**.
1. Nella pagina **Crea una funzione** scegliere **webhook e API** e quindi fare clic su **Crea**.

Il portale crea una funzione predefinita denominata **HttpTrigger1**:

![Funzione trigger HTTP predefinita](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configura associazioni di funzioni

Per inviare messaggi di posta elettronica con SendGrid, è necessario configurare i binding per la funzione nel modo seguente:

1. Selezionare **integrazione**, scegliere il http di output **($Return)** , quindi selezionare **Elimina**.
1. Scegliere **+ nuovo output**, quindi scegliere **SendGrid**, quindi scegliere **Seleziona**. Scegliere **Installa** per installare l'estensione SendGrid.
1. Al termine dell'installazione, selezionare **Usa valore restituito della funzione**. Aggiungere un **indirizzo valido per** ricevere le notifiche tramite posta elettronica.  Aggiungere un **Indirizzo from valido da** usare come mittente del messaggio di posta elettronica.
1. Selezionare **nuovo** accanto a **impostazione app chiave API SendGrid**. Immettere **SendGridAPIKey** come chiave e la chiave API SendGrid annotata in precedenza come valore. Selezionare quindi **Crea**.
1. Scegliere **Salva** per salvare le associazioni SendGrid per la funzione.

Le impostazioni di integrazione hanno un aspetto simile allo screenshot seguente:

![Integrazioni di app per le funzioni](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Aggiungere il codice della funzione

Per implementare la funzione, aggiungere il C# codice per analizzare la richiesta HTTP in ingresso e inviare i messaggi di posta elettronica come indicato di seguito:

1. Scegliere la funzione **HttpTrigger1** nell'app per le funzioni e sostituire C# il codice con il codice seguente:

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

1. Selezionare **Save (Salva** ) per salvare la funzione.

### <a name="test-the-function-works"></a>Testare la funzione funziona

Per testare la funzione nel portale, scegliere innanzitutto **log** nella parte inferiore dell'editor di codice. Quindi scegliere **test** a destra dell'editor di codice. Usare il codice JSON seguente come **corpo della richiesta**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

I messaggi del log delle funzioni vengono visualizzati nel pannello **logs** :

![Output del log delle funzioni](media/howto-create-custom-rules/function-app-logs.png)

Dopo alcuni minuti, l'indirizzo **di** posta elettronica a riceve un messaggio di posta elettronica con il contenuto seguente:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Aggiungere una query di analisi di flusso

Questa soluzione USA una query di analisi di flusso per rilevare quando un dispositivo smette di inviare dati di telemetria per più di 120 secondi. La query usa i dati di telemetria dell'hub eventi come input. Il processo invia i risultati della query all'app per le funzioni. In questa sezione si configura il processo di analisi di flusso:

1. Nel portale di Azure passare al processo di analisi di flusso, in **topologia processi** selezionare **input**, scegliere **+ Aggiungi input flusso**, quindi scegliere **Hub eventi**.
1. Usare le informazioni nella tabella seguente per configurare l'input usando l'hub eventi creato in precedenza, quindi scegliere **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Alias di input | centraltelemetry |
    | Sottoscrizione | Sottoscrizione in uso |
    | Spazio dei nomi dell'hub eventi | Spazio dei nomi dell'hub eventi |
    | Nome dell'hub eventi | USA **centralexport** esistente |

1. In **topologia processi**selezionare **output**, fare clic su **+ Aggiungi**, quindi scegliere **funzione di Azure**.
1. Usare le informazioni nella tabella seguente per configurare l'output, quindi scegliere **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Alias di output | emailnotification |
    | Sottoscrizione | Sottoscrizione in uso |
    | App per le funzioni | App per le funzioni |
    | Funzione  | HttpTrigger1 |

1. In **topologia processi**selezionare **query** e sostituire la query esistente con la seguente SQL:

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
1. Per avviare il processo di analisi di flusso, scegliere **Panoramica**, **Avvia**, quindi **ora**, quindi **Avvia**:

    ![Analisi di flusso](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

Nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) passare all'applicazione IoT Central creata dal modello contoso. In questa sezione l'applicazione viene configurata in modo da trasmettere i dati di telemetria dai dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare alla pagina **esportazione dati** , selezionare **+ nuovo**, quindi **Hub eventi di Azure**.
1. Usare le impostazioni seguenti per configurare l'esportazione, quindi selezionare **Salva**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome visualizzato | Esporta in hub eventi |
    | Enabled | Attivato |
    | Spazio dei nomi di Hub eventi | Nome dello spazio dei nomi di hub eventi |
    | Hub eventi | centralexport |
    | Misure | Attivato |
    | Dispositivi | Off |
    | Modelli di dispositivo | Off |

![Configurazione esportazione dati continui](media/howto-create-custom-rules/cde-configuration.png)

Prima di continuare, attendere che lo stato di esportazione sia **in esecuzione** .

## <a name="test"></a>Test

Per testare la soluzione, è possibile disabilitare l'esportazione dei dati continui da IoT Central a dispositivi arrestati simulati:

1. Nell'applicazione IoT Central passare alla pagina **esportazione dati** e selezionare la configurazione Esporta **in hub eventi** .
1. Impostare **abilitato** su **disattivato** e scegliere **Salva**.
1. Dopo almeno due minuti, l'indirizzo **di** posta elettronica a riceve uno o più messaggi di posta elettronica simili al contenuto di esempio seguente:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Operazioni finali

Per riordinare dopo questa procedura ed evitare i costi non necessari, eliminare il gruppo di risorse **DetectStoppedDevices** nel portale di Azure.

È possibile eliminare l'applicazione IoT Central dalla pagina di **gestione** all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Trasmettere i dati di telemetria da un'applicazione IoT Central usando l' *esportazione continua dei dati*.
* Creare una query di analisi di flusso che rilevi quando un dispositivo ha interrotto l'invio dei dati.
* Inviare una notifica tramite posta elettronica usando funzioni di Azure e i servizi SendGrid.

Ora che si è appreso come creare regole e notifiche personalizzate, il passaggio successivo suggerito consiste nell'apprendere come [estendere IOT Central di Azure con l'analisi personalizzata](howto-create-custom-analytics-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
