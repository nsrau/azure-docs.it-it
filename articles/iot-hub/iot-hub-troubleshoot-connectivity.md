---
title: Monitorare e risolvere i problemi di disconnessione per l'hub IoT di Azure
description: Informazioni su come monitorare e risolvere gli errori comuni di connettività dei dispositivi per l'hub IoT di Azure
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 17fb1bf8aebe1bd114f970aed997e77ce8a07af1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150772"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorare, diagnosticare e risolvere i problemi di disconnessione con l'hub IoT di Azure

I problemi di connettività per i dispositivi IoT possono essere difficili da risolvere poiché sono presenti diversi possibili punti di errore. La logica dell'applicazione, le reti fisiche, i protocolli, l'hardware, l'hub IoT e altri servizi cloud sono tutti elementi che possono causare problemi. La capacità di rilevare e individuare l'origine di un problema è fondamentale. Tuttavia, una soluzione IoT su larga scala potrebbe avere migliaia di dispositivi, pertanto controllare manualmente ogni singolo dispositivo non sarebbe pratico. Per agevolare l'individuazione, la diagnosi e la risoluzione di questi problemi su larga scala, è possibile usare le funzionalità di monitoraggio offerte dall'hub IoT tramite Monitoraggio di Azure. Queste funzionalità sono limitate alle capacità di osservazione dell'hub IoT, quindi è opportuno seguire anche le procedure consigliate per il monitoraggio dei dispositivi e di altri servizi di Azure.

## <a name="get-alerts-and-error-logs"></a>Ottenere avvisi e log degli errori

Usare Monitoraggio di Azure per ricevere avvisi e scrivere i log in caso di disconnessione dei dispositivi.

### <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica

Per registrare gli eventi e gli errori di connessione dei dispositivi, attivare la diagnostica per l'hub IoT. È consigliabile attivare questi log il prima possibile, perché se i log di diagnostica non sono abilitati, in caso di disconnessione del dispositivo non si avrà a disposizione alcuna informazione utile per la risoluzione del problema.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Impostazioni di diagnostica**.

4. Selezionare **Attiva diagnostica**.

5. Abilitare la raccolta dei log **Connessioni**.

6. Per semplificare l'analisi, è necessario attivare **Invia a Log Analytics** ([vedere i prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)). Vedere l'esempio sotto [Risolvere gli errori di connettività](#resolve-connectivity-errors).

   ![Impostazioni consigliate](./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png)

Per altre informazioni, vedere [Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-device-disconnect-at-scale"></a>Configurare gli avvisi per la disconnessione dei dispositivi su larga scala

Per ricevere avvisi quando i dispositivi si disconnettono, configurare gli avvisi per la metrica **Dispositivi connessi (anteprima)** .

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Passare all'hub IoT.

3. Selezionare **Avvisi**.

4. Selezionare **Nuova regola di avviso**.

5. Selezionare **Aggiungi condizione**, quindi selezionare "Dispositivi connessi (anteprima)".

6. Configurare la soglia e gli avvisi seguendo i prompt.

Per altre informazioni, vedere [Cosa sono gli avvisi in Microsoft Azure?](../azure-monitor/platform/alerts-overview.md).

#### <a name="detecting-individual-device-disconnects"></a>Rilevamento delle disconnessioni di singoli dispositivi

Per rilevare le disconnessioni *per dispositivo*, ad esempio quando occorre sapere se una factory è passata offline, [configurare gli eventi di disconnessione dei dispositivi con Griglia di eventi](iot-hub-event-grid.md).

## <a name="resolve-connectivity-errors"></a>Risolvere gli errori di connettività

Quando si attivano i log di diagnostica e gli avvisi per i dispositivi connessi, si ricevono avvisi in caso di problemi. In questa sezione viene descritto come cercare i problemi comuni quando si riceve un avviso. La procedura seguente presuppone che sia stato configurato Monitoraggio di Azure per i log di diagnostica.

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

1. Seguire le guide alla risoluzione dei problemi relative agli errori più comuni:

    - **[404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)**
    - **[401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)**
    - **[409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)**
    - **[500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**
    - **[500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)**

## <a name="i-tried-the-steps-but-they-didnt-work"></a>L'esecuzione della procedura non ha risolto il problema

Se il problema persiste anche dopo l'esecuzione dei passaggi descritti sopra, provare le soluzioni seguenti:

* Se si ha accesso ai dispositivi che presentano problemi, fisicamente o in modalità remota (ad esempio, tramite SSH), seguire la [guida alla risoluzione dei problemi sul lato del dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) per continuare la risoluzione dei problemi.

* Verificare che i dispositivi siano **abilitati** nel portale di Azure > Hub IoT > Dispositivi IoT.

* Se il dispositivo usa il protocollo MQTT, verificare che la porta 8883 sia aperta. Per altre informazioni, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* È possibile ottenere assistenza nella [pagina delle domande di Domande e risposte Microsoft relativa a hub IoT](/answers/topics/azure-iot-hub.html), in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) o nella pagina [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

Se questa guida non è stata utile, lasciare un commento nella sezione dei commenti seguente per contribuire a migliorare la documentazione per tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi temporanei, vedere [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).

* Per informazioni su Azure IoT SDK e sulla gestione della ripetizione di tentativi, vedere [Come gestire le funzionalità di connettività e messaggistica affidabile con gli SDK per dispositivi dell'hub IoT di Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).