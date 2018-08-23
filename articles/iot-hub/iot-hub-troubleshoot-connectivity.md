---
title: Diagnosticare e risolvere i problemi di disconnessione per l'hub IoT di Azure
description: Informazioni su come diagnosticare e risolvere gli errori comuni di connettività dei dispositivi per l'hub IoT di Azure
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: jlian
ms.openlocfilehash: 5bd66e3cb3902665aab9245a524a2bec6f57dc8c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2018
ms.locfileid: "42146237"
---
# <a name="detect-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Rilevare e risolvere i problemi di disconnessione per l'hub IoT di Azure

I problemi di connettività per i dispositivi IoT possono essere difficili da risolvere poiché sono presenti diversi possibili punti di errore. La logica dell'applicazione sul lato del dispositivo, le reti fisiche, i protocolli, l'hardware e l'hub IoT di Azure: tutti questi elementi possono causare problemi. Questo documento fornisce suggerimenti su come rilevare e risolvere i problemi di connettività dei dispositivi sul lato cloud (anziché sul lato del dispositivo).

## <a name="get-alerts-and-error-logs"></a>Ottenere avvisi e log degli errori

Usare Monitoraggio di Azure per ricevere avvisi e scrivere i log in caso di interruzione delle connessioni dei dispositivi.

### <a name="turn-on-diagnostic-logs"></a>Attivare i log di diagnostica 

Per registrare gli eventi e gli errori di connessione dei dispositivi, attivare la diagnostica per l'hub IoT. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare all'hub IoT.
1. Selezionare **Impostazioni di diagnostica**.
1. Selezionare quindi **Attiva diagnostica**.
1. Assicurarsi di abilitare la raccolta dei log **Connessioni**. 
1. Per semplificare l'analisi, è necessario attivare **Invia a Log Analytics** ([vedere i prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)). Log Analytics viene usato in un esempio riportato più avanti in questo articolo.

   ![Impostazioni consigliate][2]

Per altre informazioni, vedere [Monitorare l'integrità dell'hub IoT di Azure ed eseguire la diagnostica rapida dei problemi](iot-hub-monitor-resource-health.md).

### <a name="set-up-alerts-for-the-connected-devices-count-metric"></a>Impostare avvisi per la metrica di conteggio dei dispositivi connessi

Per ottenere avvisi quando i dispositivi si disconnettono, configurare gli avvisi per la metrica *Dispositivi connessi*. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare all'hub IoT.
1. Selezionare **Avvisi (versione classica)**.
1. Fare clic su **Aggiungi avviso per la metrica (versione classica)**.
1. Compilare il modulo e selezionare **OK**. 

   ![Avviso per la metrica consigliato][3]

Per altre informazioni, vedere [Cosa sono gli avvisi classici in Microsoft Azure?](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="resolve-connectivity-errors"></a>Risolvere gli errori di connettività

Quando i log di diagnostica e gli avvisi per i dispositivi connessi sono attivati, si ricevono avvisi in caso di problemi. In questa sezione viene descritto come risolvere i problemi comuni quando si riceve un avviso. La procedura seguente presuppone che sia stato configurato Log Analytics per i log di diagnostica. 

1. Passare all'area di lavoro **Log Analytics** nel portale di Azure.
1. Fare clic su **Ricerca log**.
1. Per isolare i log degli errori di connettività per l'hub IoT, immettere la query nella casella, quindi fare clic su **Esegui**.

    ```
    search *
    | where ( Type == "AzureDiagnostics" and ResourceType == "IOTHUBS")
    | where ( Category == "Connections" and Level == "Error")
    ```

1. Se sono presenti risultati, cercare `OperationName`, `ResultType` (codice di errore), e `ResultDescription` (messaggio di errore) per ottenere altri dettagli sull'errore.

   ![Esempio di log degli errori][4]

1. Usare questa tabella per comprendere e risolvere gli errori comuni.

    | Tipi di errore | Causa radice | Risoluzione |
    |---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 404104 DeviceConnectionClosedRemotely | La connessione è stata chiusa dal dispositivo, ma l'hub IoT non è in grado di determinare il motivo. Le cause più comuni includono il timeout MQTT/AMQP e la perdita di connettività Internet. | Assicurarsi che il dispositivo possa connettersi all'hub IoT [eseguendo il test della connessione](tutorial-connectivity.md). Se la connessione viene stabilita ma il dispositivo si disconnette in modo intermittente, assicurarsi di implementare per i dispositivi la logica keep-alive appropriata per il protocollo scelto (MQTT/AMPQ). |
    | 401003 IoTHubUnauthorized | L'hub IoT non è riuscito ad autenticare la connessione. | Assicurarsi che la firma di accesso condiviso o altri token di sicurezza in uso non siano scaduti. Gli [Azure IoT SDK](iot-hub-devguide-sdks.md) generano automaticamente i token senza richiedere una configurazione speciale. |
    | 409002 LinkCreationConflict | Sono presenti più connessioni per lo stesso dispositivo. Quando arriva una nuova richiesta di connessione per un dispositivo, l'hub IoT chiude quella precedente con questo errore. | Nel caso più comune, un dispositivo rileva una disconnessione e tenta di ristabilire la connessione, ma per l'hub IoT non è ancora disconnesso, quindi chiude la connessione precedente e registra questo errore. In genere questo errore si verifica come effetto collaterale di un altro problema temporaneo. Per risolvere il problema, cercare l'errore nei log. In alternativa, assicurarsi di generare una nuova richiesta di connessione solo se la connessione viene interrotta. |
    | 500001 ServerError | Si è verificato un problema sul lato server nell'hub IoT. Molto probabilmente, si tratta di un problema temporaneo. Anche se il team dell'hub IoT si impegna costantemente per il mantenimento del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/iot-hub/), in piccoli subset di nodi dell'hub IoT possono occasionalmente verificarsi errori temporanei. Quando il dispositivo tenta di connettersi a un nodo in cui si sono verificati problemi, viene visualizzato questo errore. | Per attenuare il problema degli errori temporanei, eseguire un nuovo tentativo dal dispositivo. Per [gestire automaticamente i tentativi](iot-hub-reliability-features-in-sdks.md#connection-and-retry), assicurarsi di usare la versione più recente degli [Azure IoT SDK](iot-hub-devguide-sdks.md).<br><br>Per le procedure consigliate sulla gestione degli errori temporanei e la ripetizione dei tentativi, vedere [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).  <br><br>Se il problema persiste dopo la ripetizione dei tentativi, controllare [Integrità risorse](iot-hub-monitor-resource-health.md#use-azure-resource-health) e [Stato di Azure](https://azure.microsoft.com/status/history/) per verificare se nell'hub IoT si è verificato un problema noto. Se non sono presenti problemi noti e il problema persiste, [contattare il supporto tecnico](https://azure.microsoft.com/support/options/) per un'analisi più approfondita. |
    | 500008 GenericTimeout | L'hub IoT non è riuscito a completare la richiesta di connessione prima del timeout. Come 500001 ServerError, questo errore è probabilmente temporaneo. | Per individuare la causa radice e risolvere questo errore, attenersi alla procedura di risoluzione dei problemi per l'errore 500001 ServerError.|

## <a name="other-steps-to-try"></a>Altri passaggi da provare

Se i passaggi precedenti non consentono di risolvere il problema, ecco alcune altre soluzioni da provare:

* Se si ha accesso ai dispositivi che presentano problemi, fisicamente o in modalità remota (ad esempio, tramite SSH), seguire la [guida alla risoluzione dei problemi sul lato del dispositivo](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) per continuare la risoluzione dei problemi.
* Verificare che i dispositivi siano **abilitati** nel portale di Azure > Hub IoT > Dispositivi IoT.
* Ottenere assistenza dal [forum dedicato all'hub IoT di Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azureiothub), da [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) o dal [supporto tecnico di Azure](https://azure.microsoft.com/support/options/).

Se questa guida non stata utile, lasciare un commento di seguito per contribuire a migliorare la documentazione per tutti gli utenti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulla risoluzione dei problemi temporanei, vedere [Gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults).
* Per informazioni su Azure IoT SDK e sulla gestione della ripetizione di tentativi, vedere [Come gestire le funzionalità di connettività e messaggistica affidabile con gli SDK per dispositivi dell'hub IoT di Azure](iot-hub-reliability-features-in-sdks.md#connection-and-retry).

<!-- Images -->
[1]: ../../includes/media/iot-hub-diagnostics-settings/turnondiagnostics.png
[2]: ./media/iot-hub-troubleshoot-connectivity/diagnostic-settings-recommendation.png
[3]: ./media/iot-hub-troubleshoot-connectivity/metric-alert.png
[4]: ./media/iot-hub-troubleshoot-connectivity/diag-logs.png
