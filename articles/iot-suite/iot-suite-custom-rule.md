---
title: Creare una regola personalizzata in Azure IoT Suite | Documentazione Microsoft
description: Come creare una regola personalizzata in una soluzione IoT Suite preconfigurata.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/14/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 3d24c0c29da8c97e5fcff524dc88746982fda77c
ms.openlocfilehash: a34a30a301e213ad227c21196a0b6299dbc4d357
ms.lasthandoff: 12/15/2016


---
# <a name="create-a-custom-rule-in-the-remote-monitoring-preconfigured-solution"></a>Creare una regola personalizzata nella soluzione di monitoraggio remota preconfigurata

## <a name="introduction"></a>Introduzione

Nelle soluzioni preconfigurate è possibile configurare [regole che si attivano quando un valore di telemetria per un dispositivo raggiunge una soglia specifica][lnk-builtin-rule]. [Usare i dati di telemetria dinamica con la soluzione di monitoraggio remoto preconfigurata][lnk-dynamic-telemetry] descrive come aggiungere alla soluzione valori di telemetria personalizzati, ad esempio *ExternalTemperature*. Questo articolo illustra come creare regole personalizzate per tipi di dati di telemetria dinamica nella soluzione.

Questa esercitazione usa un semplice dispositivo simulato Node.js per generare dati di telemetria dinamica da inviare al back-end della soluzione preconfigurata. Si aggiungeranno quindi regole personalizzate nella soluzione **RemoteMonitoring** di Visual Studio e si distribuirà questo back-end personalizzato alla sottoscrizione di Azure.

Per completare questa esercitazione, sono necessari:

* Una sottoscrizione di Azure attiva. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][lnk_free_trial].
* [Node.js][lnk-node] versione 0.12.x o successiva per creare un dispositivo simulato.
* Microsoft Visual Studio 2015 per modificare nuovamente il back-end della soluzione preconfigurata con le nuove regole.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

Prendere nota del nome della soluzione che si è scelto per la distribuzione. Il nome della soluzione sarà necessario più avanti nell'esercitazione.

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

È possibile arrestare l'applicazione console Node.js dopo avere verificato che sta inviando i dati di telemetria **ExternalTemperature** alla soluzione preconfigurata. Mantenere aperta la finestra della console in quanto si eseguirà nuovamente l'applicazione console Node.js dopo aver aggiunto la regola personalizzata alla soluzione.

## <a name="rule-storage-locations"></a>Posizioni di archiviazione delle regole

Le informazioni sulle regole vengono mantenute in due posizioni:

* Tabella **DeviceRulesNormalizedTable**: questa tabella archivia un riferimento normalizzato alle regole definite dal portale della soluzione. Quando il portale della soluzione visualizza le regole del dispositivo, interroga questa tabella per trovare le definizioni delle regole.
* BLOB **DeviceRules**: questo BLOB archivia tutte le regole definite per tutti i dispositivi registrati ed è definito come input di riferimento per i processi di analisi di flusso di Azure.
 
Quando si aggiorna una regola esistente o si definisce una nuova regola nel portale della soluzione, sia la tabella sia il BLOB vengono aggiornati per riflettere le modifiche. La definizione della regola visualizzata nel portale proviene dall'archivio tabelle e la definizione della regola a cui fanno riferimento i processi di analisi di flusso proviene dal BLOB. 

## <a name="update-the-remotemonitoring-visual-studio-solution"></a>Aggiornare la soluzione RemoteMonitoring di Visual Studio

La procedura seguente mostra come modificare la soluzione RemoteMonitoring di Visual Studio per includere una nuova regola che usa i dati di telemetria **ExternalTemperature** inviati dal dispositivo simulato:

1. Se non è ancora stato fatto, clonare il repository **azure-iot-remote-monitoring** in un percorso appropriato nel computer locale usando il comando Git seguente:

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. In Visual Studio aprire il file RemoteMonitoring.sln dalla copia locale del repository **azure-iot-remote-monitoring**.

3. Aprire il file Infrastructure\Models\DeviceRuleBlobEntity.cs e aggiungere una proprietà **ExternalTemperature** come segue:

    ```csharp
    public double? Temperature { get; set; }
    public double? Humidity { get; set; }
    public double? ExternalTemperature { get; set; }
    ```

4. Nello stesso file aggiungere una proprietà **ExternalTemperatureRuleOutput** come indicato di seguito:

    ```csharp
    public string TemperatureRuleOutput { get; set; }
    public string HumidityRuleOutput { get; set; }
    public string ExternalTemperatureRuleOutput { get; set; }
    ```

5. Aprire il file Infrastructure\Models\DeviceRuleDataFields.cs e aggiungere la seguente proprietà **ExternalTemperature** dopo la proprietà **Humidity** esistente:

    ```csharp
    public static string ExternalTemperature
    {
        get { return "ExternalTemperature"; }
    }
    ```

6. Nello stesso file aggiornare il metodo **_availableDataFields** in modo che includa **ExternalTemperature** come segue:

    ```csharp
    private static List<string> _availableDataFields = new List<string>
    {                    
        Temperature, Humidity, ExternalTemperature
    };
    ```

7. Aprire il file Infrastructure\Repository\DeviceRulesRepository.cs e modificare il metodo **BuildBlobEntityListFromTableRows** come segue:

    ```csharp
    else if (rule.DataField == DeviceRuleDataFields.Humidity)
    {
        entity.Humidity = rule.Threshold;
        entity.HumidityRuleOutput = rule.RuleOutput;
    }
    else if (rule.DataField == DeviceRuleDataFields.ExternalTemperature)
    {
      entity.ExternalTemperature = rule.Threshold;
      entity.ExternalTemperatureRuleOutput = rule.RuleOutput;
    }
    ```

## <a name="rebuild-and-redeploy-the-solution"></a>Ricompilare e ridistribuire la soluzione.

È ora possibile distribuire la soluzione aggiornata alla sottoscrizione di Azure.

1. Aprire un prompt dei comandi con privilegi elevati e passare alla radice della copia locale del repository azure-iot-remote-monitoring.

2. Per distribuire la soluzione aggiornata, eseguire il comando seguente sostituendo **{nome distribuzione}** con il nome della distribuzione della soluzione preconfigurata annotato in precedenza:

    ```
    build.cmd cloud release {deployment name}
    ```

## <a name="update-the-stream-analytics-job"></a>Aggiornare il processo di analisi di flusso

Dopo aver completato la distribuzione, è possibile aggiornare il processo di analisi di flusso in modo che usi le nuove definizioni delle regole.

1. Nel portale di Azure passare al gruppo di risorse che contiene le risorse della soluzione preconfigurata. Questo gruppo di risorse ha lo stesso nome specificato per la soluzione durante la distribuzione.

2. Passare al processo di analisi di flusso {nome distribuzione}-Regole. 

3. Fare clic su **Stop** per arrestare l'esecuzione del processo di analisi di flusso. È necessario attendere che il processo di analisi di flusso si arresti prima di poter modificare la query.

4. Fare clic su **Query**. Modificare la query aggiungendo l'istruzione **SELECT** per **ExternalTemperature**. L'esempio seguente illustra la query completa con la nuova istruzione **SELECT**:

    ```
    WITH AlarmsData AS 
    (
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Temperature' as ReadingType,
         Stream.Temperature as Reading,
         Ref.Temperature as Threshold,
         Ref.TemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'Humidity' as ReadingType,
         Stream.Humidity as Reading,
         Ref.Humidity as Threshold,
         Ref.HumidityRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
     
    UNION ALL
     
    SELECT
         Stream.IoTHub.ConnectionDeviceId AS DeviceId,
         'ExternalTemperature' as ReadingType,
         Stream.ExternalTemperature as Reading,
         Ref.ExternalTemperature as Threshold,
         Ref.ExternalTemperatureRuleOutput as RuleOutput,
         Stream.EventEnqueuedUtcTime AS [Time]
    FROM IoTTelemetryStream Stream
    JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
    WHERE
         Ref.ExternalTemperature IS NOT null AND Stream.ExternalTemperature > Ref.ExternalTemperature
    )
     
    SELECT *
    INTO DeviceRulesMonitoring
    FROM AlarmsData
     
    SELECT *
    INTO DeviceRulesHub
    FROM AlarmsData
    ```

5. Fare clic su **Salva** per modificare la query di regola aggiornata.

6. Fare clic su **Avvia** per avviare nuovamente il processo di analisi di flusso.

## <a name="add-your-new-rule-in-the-dashboard"></a>Aggiungere la nuova regola nel dashboard

È ora possibile aggiungere la regola **ExternalTemperature** a un dispositivo nel dashboard della soluzione.

1. Passare al portale della soluzione.

2. Passare al pannello **Dispositivi**.

3. Individuare il dispositivo personalizzato che è stato creato che invia i dati di telemetria **ExternalTemperature** e nel pannello **Dettagli dispositivo** fare clic su **Aggiungi regola**.

4. Selezionare **ExternalTemperature** in **Campo dati**.

5. Impostare **Soglia** su 56. Quindi fare clic su **Salva e visualizza regole**.

6. Tornare al dashboard per visualizzare la cronologia avvisi.

7. Nella finestra della console che è stata lasciata aperta avviare l'applicazione console Node.js per iniziare a inviare i dati di telemetria **ExternalTemperature**.

8. Si noti che la tabella **Cronologia avvisi** mostra nuovi avvisi quando viene attivata la nuova regola.
 
## <a name="additional-information"></a>Informazioni aggiuntive

Modificare l'operatore ** > ** è più complesso ed esula dalla procedura descritta in questa esercitazione. Mentre è possibile modificare il processo di analisi di flusso perché usi qualsiasi operatore desiderato, riflettere tale operatore nel portale della soluzione è un'attività più complessa. 

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come creare regole personalizzate, è possibile vedere altre informazioni sulle soluzioni preconfigurate:

- [Connettere l'app per la logica alla soluzione preconfigurata per il monitoraggio remoto Azure IoT Suite][lnk-logic-app]
- [Metadati di informazioni sul dispositivo nella soluzione preconfigurata per il monitoraggio remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[lnk-builtin-rule]: iot-suite-getstarted-preconfigured-solutions.md#view-alarms
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md
[lnk-logic-app]: iot-suite-logic-apps-tutorial.md
