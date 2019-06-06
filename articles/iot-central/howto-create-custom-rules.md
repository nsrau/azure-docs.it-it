---
title: Estendere Azure IoT Central con regole personalizzate e le notifiche | Microsoft Docs
description: Gli sviluppatori di soluzioni, configurare un'applicazione IoT Central per inviare notifiche tramite posta elettronica quando un dispositivo interrompe l'invio di dati di telemetria. Questa soluzione Usa Azure Stream Analitica e funzioni di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742421"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Estendere Azure IoT Central con regole personalizzate che inviano notifiche

Questa guida illustra, gli sviluppatori di soluzioni, come estendere l'applicazione IoT Central con regole personalizzate e le notifiche. Nell'esempio viene illustrato come inviare una notifica a un operatore quando un dispositivo si arresta l'invio di dati di telemetria. La soluzione Usa un' [Azure Stream Analitica](https://docs.microsoft.com/azure/stream-analytics/) query per rilevare quando un dispositivo ha interrotto l'invio di dati di telemetria. Utilizzi del processo di Analitica Stream [funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) inviare notifica di posta elettronica usando [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Questa guida illustra come estendere IoT Central oltre già cosa con le regole predefinite e le azioni.

In questa guida illustra come:

* Stream i dati di telemetria da un'applicazione che usa IoT Central *esportazione continua dei dati*.
* Creare una query Analitica Stream che consente di rilevare quando un dispositivo ha interrotto l'invio dei dati.
* Invia una notifica di posta elettronica usando le funzioni di Azure e servizi forniti da SendGrid.

## <a name="prerequisites"></a>Prerequisiti

Per completare la procedura descritta in questa guida pratica, è necessaria una sottoscrizione di Azure attiva.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

### <a name="iot-central-application"></a>Applicazione IoT Central

Creare un'applicazione IoT Central dal [Azure IoT Central - applicazioni personali](https://aka.ms/iotcentral) pagina con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| Piano di pagamento | Pagamento in base al consumo |
| Modello di applicazione | Esempio Contoso |
| Nome dell'applicazione | Accettare il valore predefinito oppure scegliere il proprio nome |
| URL | Accettare il valore predefinito oppure scegliere il proprio prefisso URL univoco |
| Directory | Il tenant di Azure Active Directory |
| Sottoscrizione di Azure | Sottoscrizione di Azure |
| Region | Stati Uniti orientali |

Gli esempi e schermate in questo articolo usano il **Stati Uniti orientali** area. Scegliere una località vicina e assicurarsi di che creare tutte le risorse nella stessa area.

### <a name="resource-group"></a>Gruppo di risorse

Usare la [portale di Azure per creare un gruppo di risorse](https://portal.azure.com/#create/Microsoft.ResourceGroup) chiamato **DetectStoppedDevices** per contenere le altre risorse create. Creare le risorse di Azure nella stessa posizione dell'applicazione IoT Central.

### <a name="event-hubs-namespace"></a>Spazio dei nomi di Hub eventi

Usare la [portale di Azure per creare uno spazio dei nomi di hub eventi](https://portal.azure.com/#create/Microsoft.EventHub) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| NOME    | Scegliere il nome dello spazio dei nomi |
| Piano tariffario | Basic |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | DetectStoppedDevices |
| Località | Stati Uniti orientali |
| Unità elaborate | 1 |

### <a name="stream-analytics-job"></a>Processo di Stream Analitica

Usare la [portale di Azure per creare un processo di Stream Analitica](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Name    | Scegliere il nome del processo |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | DetectStoppedDevices |
| Località | Stati Uniti orientali |
| Ambiente di hosting | Cloud |
| Unità di streaming | 3 |

### <a name="function-app"></a>App per le funzioni

Usare la [portale di Azure per creare un'app per le funzioni](https://portal.azure.com/#create/Microsoft.FunctionApp) con le impostazioni seguenti:

| Impostazione | Value |
| ------- | ----- |
| Nome app    | Scegliere il nome dell'app (funzione) |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | DetectStoppedDevices |
| OS | Windows |
| Piano di hosting | Piano a consumo |
| Località | Stati Uniti orientali |
| Stack di runtime | .NET |
| Archiviazione | Creare un nuovo gruppo di risorse |

### <a name="sendgrid-account"></a>Account SendGrid

Usare la [portale di Azure per creare un account SendGrid](https://portal.azure.com/#create/Sendgrid.sendgrid) con le impostazioni seguenti:

| Impostazione | Valore |
| ------- | ----- |
| Name    | Scegliere il nome dell'account SendGrid |
| Password | Creare una password |
| Sottoscrizione | Sottoscrizione in uso |
| Gruppo di risorse | DetectStoppedDevices |
| Piano tariffario | F1 Gratuito |
| Informazioni contatto | Fornisci le informazioni necessarie |

Dopo aver creato tutte le risorse necessarie, il **DetectStoppedDevices** gruppo di risorse è simile alla seguente:

![Rileva gruppo di risorse dispositivi arrestato](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Creare un hub eventi

È possibile configurare un'applicazione IoT Central per esportare continuamente dati di telemetria a un hub eventi. In questa sezione si crea un hub eventi per ricevere i dati di telemetria dall'applicazione IoT Central. L'hub eventi fornisce i dati di telemetria al processo di Stream Analitica per l'elaborazione.

1. Nel portale di Azure, passare allo spazio dei nomi di hub eventi e selezionare **+ Hub eventi**.
1. Nome dell'hub eventi **centralexport**e selezionare **crea**.

Lo spazio dei nomi di hub eventi è simile alla seguente:

![Spazio dei nomi di Hub eventi](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>Ottenere la chiave API SendGrid

App per le funzioni richiede una chiave API SendGrid per inviare messaggi di posta elettronica. Per creare una chiave API SendGrid:

1. Nel portale di Azure, passare all'account di SendGrid. Quindi scegliere **Gestisci** per accedere all'account di SendGrid.
1. Nell'account di SendGrid, scegliere **le impostazioni**, quindi **le chiavi API**. Scegli **Crea chiave API**:

    ![Crea chiave API SendGrid](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Nel **Crea chiave API** pagina, creare una chiave denominata **AzureFunctionAccess** con **accesso completo** autorizzazioni.
1. Prendere nota della chiave API, è necessario quando si configura l'app per le funzioni.

## <a name="define-the-function"></a>Definire la funzione

Questa soluzione Usa un'app per le funzioni di Azure per inviare una notifica di posta elettronica quando il processo di Stream Analitica rileva un dispositivo arrestato. Per creare app per le funzioni:

1. Nel portale di Azure, passare al **servizio App** dell'istanza nel **DetectStoppedDevices** gruppo di risorse.
1. Selezionare **+** per creare una nuova funzione.
1. Nel **ambiente di sviluppo A scegliere** pagina, scegliere **nel portale** e quindi selezionare **continua**.
1. Nel **CREATE A FUNCTION** pagina, scegliere **Webhook e API** e quindi selezionare **Create**.

Il portale crea una funzione predefinita denominata **HttpTrigger1**:

![Funzione trigger HTTP predefinito](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Configurare le associazioni di funzione

Per inviare messaggi di posta elettronica con SendGrid, è necessario configurare i binding per la funzione come indicato di seguito:

1. Selezionare **integra**, scegliere l'output **HTTP ($return)** , quindi selezionare **Elimina**.
1. Scegli **+ nuovo Output**, quindi scegliere **SendGrid**, quindi scegliere **selezionare**. Scegli **installare** per installare l'estensione di SendGrid.
1. Al termine dell'installazione, selezionare **Usa valore restituito della funzione**. Aggiungere un valore valido **all'indirizzo** per ricevere notifiche tramite posta elettronica.  Aggiungere un valore valido **dall'indirizzo** da utilizzare come il mittente del messaggio di posta elettronica.
1. Selezionare **nuove** accanto a **impostazione dell'App chiave API SendGrid**. Immettere **SendGridAPIKey** come chiave e la chiave API SendGrid annotato in precedenza come valore. Selezionare quindi **Crea**.
1. Scegli **salvare** per salvare le associazioni di SendGrid per la funzione.

Le impostazioni di integrazione di aspetto simile allo screenshot seguente:

![Integrazione di app (funzione)](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>Aggiungere il codice della funzione

Per implementare la funzione, aggiungere il C# codice per analizzare la richiesta HTTP in ingresso e inviare messaggi di posta elettronica come indicato di seguito:

1. Scegliere il **HttpTrigger1** funzione nell'app per la funzione e sostituire la C# codice con il codice seguente:

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

    Si può vedere un messaggio di errore fino a quando non si salva il nuovo codice.

1. Selezionare **salvare** per salvare la funzione.

### <a name="test-the-function-works"></a>Testare il funzionamento della funzione

Per testare la funzione nel portale, scegliere **registri** nella parte inferiore dell'editor del codice. Quindi scegliere **Test** a destra dell'editor del codice. Usare il codice JSON seguente come le **corpo della richiesta**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

I messaggi di log di funzione vengono visualizzati nei **registri** pannello:

![Output del log (funzione)](media/howto-create-custom-rules/function-app-logs.png)

Dopo alcuni minuti, il **a** indirizzo di posta elettronica riceve un messaggio di posta elettronica con il contenuto seguente:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Aggiungere query Analitica di Stream

Questa soluzione Usa una query Analitica Stream consente di rilevare quando un dispositivo interrompe l'invio di dati di telemetria per più di 120 secondi. La query Usa i dati di telemetria dall'hub eventi come input. Il processo invia i risultati della query per app per le funzioni. In questa sezione si configura il processo di Stream Analitica:

1. Nel portale di Azure, passare al processo di analitica di Stream, in **topologia processi** selezionare **input**, scegliere **+ Aggiungi input del flusso**, quindi scegliere **evento Hub**.
1. Usare le informazioni nella tabella seguente per configurare l'input usando l'hub eventi creato in precedenza, quindi scegliere **salvare**:

    | Impostazione | Value |
    | ------- | ----- |
    | Alias di input | centraltelemetry |
    | Sottoscrizione | Sottoscrizione in uso |
    | Spazio dei nomi dell'hub eventi | Lo spazio dei nomi di Hub eventi |
    | Nome dell'hub eventi | Usa esistente - **centralexport** |

1. Sotto **topologia processi**, selezionare **output**, scegliere **+ Aggiungi**, quindi scegliere **funzioni di Azure**.
1. Usare le informazioni nella tabella seguente per configurare l'output, quindi scegliere **salvare**:

    | Impostazione | Value |
    | ------- | ----- |
    | Alias di output | EmailNotification |
    | Sottoscrizione | Sottoscrizione in uso |
    | App per le funzioni | App per le funzioni |
    | Funzione  | HttpTrigger1 |

1. Sotto **topologia processi**, selezionare **Query** e sostituire la query esistente con il codice SQL seguente:

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
1. Per avviare il processo di Stream Analitica, scegli **Overview**, quindi **avviare**, quindi **ora**e quindi **avviare**:

    ![Analisi di flusso](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Configurare l'esportazione in IoT Central

Passare il [applicazione IoT Central](https://aka.ms/iotcentral) creato dal modello di Contoso. In questa sezione configurare l'applicazione per trasmettere i dati di telemetria dai relativi dispositivi simulati all'hub eventi. Per configurare l'esportazione:

1. Passare il **esportazione continua dei dati** pagina, selezionare **+ nuovo**e quindi **hub eventi di Azure**.
1. Usare le impostazioni seguenti per configurare l'esportazione e quindi selezionare **salvare**:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome visualizzato | Esporta in hub eventi |
    | Enabled | Attivato |
    | Spazio dei nomi di Hub eventi | Il nome dello spazio dei nomi di hub eventi |
    | Hub eventi | centralexport |
    | Misure | Attivato |
    | Dispositivi | Off |
    | Modelli di dispositivo | Off |

![Configurazione per l'esportazione continua dei dati](media/howto-create-custom-rules/cde-configuration.png)

Attendere fino a ottenere lo stato di esportazione **in esecuzione** prima di continuare.

## <a name="test"></a>Test

Per testare la soluzione, è possibile disabilitare l'esportazione continua dei dati da IoT Central per i dispositivi simulati di arrestati:

1. Nell'applicazione IoT Central, passare al **esportazione continua dei dati** pagina e selezionare il **Esporta in hub eventi** esportare la configurazione.
1. Impostare **Enabled** al **Off** e scegliere **Salva**.
1. Dopo avere almeno due minuti, il **a** indirizzo di posta elettronica riceve uno o più messaggi di posta elettronica con un aspetto simile al seguente contenuti:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Operazioni finali

Per ripulire dopo questa procedura ed evitare i costi non necessari, eliminare il **DetectStoppedDevices** gruppo di risorse nel portale di Azure.

È possibile eliminare l'applicazione IoT Central dal **gestione** pagina all'interno dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

In questa guida procedurale si è appreso come:

* Stream i dati di telemetria da un'applicazione che usa IoT Central *esportazione continua dei dati*.
* Creare una query Analitica Stream che consente di rilevare quando un dispositivo ha interrotto l'invio dei dati.
* Invia una notifica di posta elettronica usando le funzioni di Azure e servizi forniti da SendGrid.

Dopo aver appreso come creare regole personalizzate e le notifiche, il passaggio successivo consigliato consiste informazioni su come [estendere Azure IoT Central con analitica personalizzato](howto-create-custom-analytics.md).
