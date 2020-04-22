---
title: Monitorare e risolvere i problemi di disconnessione con l'hub IoT di AzureMonitor and troubleshoot disconnects with Azure IoT Hub
description: Informazioni su come monitorare e risolvere gli errori comuni con la connettività dei dispositivi per l'hub IoT di AzureLearn to monitor and troubleshoot common errors with device connectivity for Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 82139eef9708ff8d76e1087c71aa5445ba898385
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759602"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorare, diagnosticare e risolvere i problemi di disconnessione con l'hub IoT di AzureMonitor, diagnose, and troubleshoot disconnects with Azure IoT Hub

I problemi di connettività per i dispositivi IoT possono essere difficili da risolvere poiché sono presenti diversi possibili punti di errore. La logica dell'applicazione, le reti fisiche, i protocolli, l'hardware, l'hub IoT e altri servizi cloud possono causare problemi. La capacità di rilevare e individuare l'origine di un problema è fondamentale. Tuttavia, una soluzione IoT su larga scala potrebbe avere migliaia di dispositivi, quindi non è pratico controllare i singoli dispositivi manualmente. Per aiutarti a rilevare, diagnosticare e risolvere questi problemi su larga scala, usa le funzionalità di monitoraggio fornite dall'Hub IoT tramite Monitoraggio di Azure.To help you detect, diagnose, and troubleshoot these issues at scale, use the monitoring capabilities IoT Hub provides through Azure Monitor. Queste funzionalità sono limitate a ciò che l'hub IoT può osservare, pertanto è consigliabile seguire le procedure consigliate di monitoraggio per i dispositivi e altri servizi di Azure.These capabilities are limited to what IoT Hub can observe, so also recommend that you follow monitoring best practices for your devices and other Azure services.

## <a name="get-alerts-and-error-logs"></a>Ottenere avvisi e log degli errori

Usare Monitoraggio di Azure per ricevere avvisi e scrivere log quando i dispositivi si disconnettono.

### <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica

Per registrare gli eventi e gli errori di connessione dei dispositivi, attivare la diagnostica per l'hub IoT. È consigliabile attivare questi log il prima possibile, perché se i log di diagnostica non sono abilitati, quando si disconnettono il dispositivo, non si dia alcuna informazione per risolvere il problema.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Impostazioni di diagnostica**.

4. Selezionare **Attiva diagnostica**.

5. Abilitare la raccolta dei log **Connessioni**.

6. Per semplificare l'analisi, è necessario attivare **Invia a Log Analytics** ([vedere i prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)). Vedere l'esempio sotto [Risolvere gli errori di connettività](#resolve-connectivity-errors).

   ![Impostazioni consigliate](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Per altre informazioni, vedere [Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configurare gli avvisi per la disconnessione del dispositivo in fase di scalabilitàSet up alerts for device disconnect at scale

Per ricevere avvisi quando i dispositivi si disconnettono, configurare gli avvisi nella metrica **Dispositivi connessi (anteprima).**

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Avvisi**.

4. Selezionare **Nuova regola di avviso**.

5. Selezionare **Aggiungi condizione**, quindi selezionare "Dispositivi connessi (anteprima)".

6. Impostare la soglia e gli avvisi seguendo i seguenti messaggi di richiesta.

Per altre informazioni, vedere [Che cosa sono gli avvisi in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Rilevamento delle disconnessioni di singoli dispositivi

Per rilevare le disconnessioni *per dispositivo,* ad esempio quando è necessario sapere che una factory è appena stata disconnessa, configurare gli eventi di [disconnessione](iot-hub-event-grid.md)del dispositivo con Griglia di eventi .

## <a name="resolve-connectivity-errors"></a>Risolvere gli errori di connettività

Quando si attivano i log di diagnostica e gli avvisi per i dispositivi connessi, si ricevono avvisi in caso di problemi. In questa sezione viene descritto come cercare problemi comuni quando si riceve un avviso. I passaggi seguenti presuppongono che siano stati impostati i log di Monitoraggio di Azure per i log di diagnostica.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare all'hub IoT.

1. Selezionare **Registri**.

1. Per isolare i log degli errori di connettività per l'hub IoT, immettere la query seguente e selezionare **Esegui**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se sono presenti risultati, cercare `OperationName`, `ResultType` (codice di errore) e `ResultDescription` (messaggio di errore) per ottenere altri dettagli sull'errore.

   ![Esempio di log degli errori](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

1. Seguire le guide alla risoluzione dei problemi per gli errori più comuni:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Ho provato i passi, ma non hanno funzionato

Se i passaggi precedenti non consentono di risolvere il messaggio, prova a eseguire le operazioni seguenti:

* Se si ha accesso ai dispositivi che presentano problemi, fisicamente o in modalità remota (ad esempio, tramite SSH), seguire la [guida alla risoluzione dei problemi sul lato del dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) per continuare la risoluzione dei problemi.

* Verificare che i dispositivi siano abilitati nel portale di Azure > l'hub IoT > i dispositivi IoT.Verify that your devices are **Enabled** in the Azure portal > your IoT hub > IoT devices.

* Se il dispositivo utilizza il protocollo MQTT, verificare che la porta 8883 sia aperta. Per ulteriori informazioni, vedere [Connessione all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Ottenere assistenza dal [forum dedicato all'hub IoT di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), da [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) o dal [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

Se questa guida non è stata utile, lasciare un commento nella sezione dei commenti seguente per contribuire a migliorare la documentazione per tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi temporanei, vedere [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

* Per informazioni su Azure IoT SDK e sulla gestione della ripetizione di tentativi, vedere [Come gestire le funzionalità di connettività e messaggistica affidabile con gli SDK per dispositivi dell'hub IoT di Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
