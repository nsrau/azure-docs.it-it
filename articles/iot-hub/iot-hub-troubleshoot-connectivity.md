---
title: Monitorare e risolvere i problemi relativi alle disconnessioni con l'hub Azure
description: Informazioni su come monitorare e risolvere gli errori comuni con la connettività dei dispositivi per l'hub Azure
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed1abe3565805810a6a3fe383e1ddfa209950469
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935382"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorare, diagnosticare e risolvere i problemi di disconnessione con l'hub Azure

I problemi di connettività per i dispositivi IoT possono essere difficili da risolvere poiché sono presenti diversi possibili punti di errore. La logica dell'applicazione, le reti fisiche, i protocolli, l'hardware, l'hub Internet e altri servizi cloud possono causare problemi. La possibilità di rilevare e individuare l'origine di un problema è fondamentale. Tuttavia, una soluzione Internet delle cose su larga scala potrebbe avere migliaia di dispositivi, pertanto non è pratico controllare manualmente i singoli dispositivi. Per individuare, diagnosticare e risolvere questi problemi su larga scala, usare l'hub informazioni sulle funzionalità di monitoraggio disponibile tramite monitoraggio di Azure. Queste funzionalità sono limitate a ciò che l'hub Internet può osservare, quindi si consiglia di seguire le procedure consigliate per il monitoraggio dei dispositivi e di altri servizi di Azure.

## <a name="get-alerts-and-error-logs"></a>Ottenere avvisi e log degli errori

Usare monitoraggio di Azure per ottenere gli avvisi e scrivere i log quando i dispositivi si disconnettono.

### <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica

Per registrare gli eventi e gli errori di connessione dei dispositivi, attivare la diagnostica per l'hub IoT. Si consiglia di attivare questi log il prima possibile, perché se i log di diagnostica non sono abilitati, quando si verifica la disconnessione del dispositivo non sono disponibili informazioni per la risoluzione del problema con.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Impostazioni di diagnostica**.

4. Selezionare **Attiva diagnostica**.

5. Abilitare la raccolta dei log **Connessioni**.

6. Per semplificare l'analisi, è necessario attivare **Invia a Log Analytics** ([vedere i prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)). Vedere l'esempio sotto [Risolvere gli errori di connettività](#resolve-connectivity-errors).

   ![Impostazioni consigliate](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Per altre informazioni, vedere [Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configurare gli avvisi per la disconnessione del dispositivo su larga scala

Per ottenere gli avvisi quando i dispositivi si disconnettono, configurare gli avvisi per la metrica **dispositivi connessi (anteprima)** .

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Avvisi**.

4. Selezionare **nuova regola di avviso**.

5. Selezionare **Aggiungi condizione**, quindi selezionare "dispositivi connessi (anteprima)".

6. Configurare la soglia e gli avvisi seguendo le istruzioni riportate di seguito.

Per altre informazioni, vedere [che cosa sono gli avvisi in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Rilevamento della disconnessione dei singoli dispositivi

Per rilevare le disconnessioni *per ogni dispositivo* , ad esempio quando è necessario avere una factory appena portata offline, [configurare gli eventi di disconnessione del dispositivo con griglia di eventi](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Risolvere gli errori di connettività

Quando si attivano i log di diagnostica e gli avvisi per i dispositivi connessi, si ricevono avvisi in caso di problemi. In questa sezione viene descritto come cercare problemi comuni quando si riceve un avviso. La procedura seguente presuppone che siano stati configurati i log di monitoraggio di Azure per i log di diagnostica.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Passare all'hub IoT.

1. Selezionare **Log**.

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

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Ho provato a eseguire i passaggi, ma non funzionavano

Se la procedura precedente non è stata utile, provare a:

* Se si ha accesso ai dispositivi che presentano problemi, fisicamente o in modalità remota (ad esempio, tramite SSH), seguire la [guida alla risoluzione dei problemi sul lato del dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) per continuare la risoluzione dei problemi.

* Verificare che i dispositivi siano **abilitati** nel portale di Azure > Hub IoT > Dispositivi IoT.

* Ottenere assistenza dal [forum dedicato all'hub IoT di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), da [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) o dal [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

Se questa guida non è stata utile, lasciare un commento nella sezione dei commenti seguente per contribuire a migliorare la documentazione per tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi temporanei, vedere [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

* Per informazioni su Azure IoT SDK e sulla gestione della ripetizione di tentativi, vedere [Come gestire le funzionalità di connettività e messaggistica affidabile con gli SDK per dispositivi dell'hub IoT di Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).
