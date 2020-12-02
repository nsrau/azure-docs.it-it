---
title: Monitorare e risolvere i problemi di disconnessione per l'hub IoT di Azure
description: Informazioni su come monitorare e risolvere gli errori comuni di connettività dei dispositivi per l'hub IoT di Azure
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 81558526500f3c01e975d9a963b4a6a98ec6d753
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446405"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorare, diagnosticare e risolvere i problemi di disconnessione con l'hub IoT di Azure

I problemi di connettività per i dispositivi IoT possono essere difficili da risolvere poiché sono presenti diversi possibili punti di errore. La logica dell'applicazione, le reti fisiche, i protocolli, l'hardware, l'hub IoT e altri servizi cloud sono tutti elementi che possono causare problemi. La capacità di rilevare e individuare l'origine di un problema è fondamentale. Tuttavia, una soluzione IoT su larga scala potrebbe avere migliaia di dispositivi, pertanto controllare manualmente ogni singolo dispositivo non sarebbe pratico. L'hub Internet delle cose si integra con due servizi di Azure che consentono di:

* **Monitoraggio di Azure** Per individuare, diagnosticare e risolvere questi problemi su larga scala, usare l'hub informazioni sulle funzionalità di monitoraggio disponibile tramite monitoraggio di Azure. Ciò include la configurazione di avvisi per attivare notifiche e azioni quando si verificano disconnessioni e la configurazione di log che è possibile usare per individuare le condizioni che hanno causato le disconnessioni.

* **Griglia di eventi di Azure** Per la disconnessione dell'infrastruttura critica e per i dispositivi, usare griglia di eventi di Azure per sottoscrivere gli eventi di connessione e disconnessione del dispositivo emessi dall'hub.

In entrambi i casi, queste funzionalità sono limitate a quanto può essere osservato dall'hub Internet, quindi si consiglia di seguire le procedure consigliate per il monitoraggio dei dispositivi e di altri servizi di Azure.

## <a name="event-grid-vs-azure-monitor"></a>Griglia di eventi e monitoraggio di Azure

Griglia di eventi offre una soluzione di monitoraggio per dispositivo a bassa latenza che è possibile usare per tenere traccia delle connessioni dei dispositivi e dell'infrastruttura critiche. Monitoraggio di Azure fornisce una metrica, *dispositivi connessi*, che è possibile usare per monitorare il numero di dispositivi connessi all'hub Internet e attivare un avviso quando tale numero scende al di sotto di una soglia statica.

Quando si decide se usare griglia di eventi o monitoraggio di Azure per uno scenario specifico, tenere presente quanto segue:

* Latenza degli avvisi: gli eventi di connessione dell'hub Internet vengono recapitati molto più rapidamente tramite griglia di eventi. In questo modo, griglia di eventi è una scelta migliore per gli scenari in cui è auspicabile una notifica rapida.

* Notifiche per dispositivo: griglia di eventi consente di rilevare le connessioni e le disconnessioni per i singoli dispositivi. Questa operazione rende griglia di eventi una scelta migliore per gli scenari in cui è necessario monitorare le connessioni per i dispositivi critici.

* Installazione Lightweight: gli avvisi delle metriche di monitoraggio di Azure offrono un'esperienza di installazione leggera che non richiede l'integrazione con altri servizi per inviare notifiche tramite posta elettronica, SMS, voce e altre notifiche.  Con griglia di eventi è necessario eseguire l'integrazione con altri servizi di Azure per recapitare le notifiche. Entrambi i servizi possono essere integrati con altri servizi per attivare azioni più complesse.

A causa delle funzionalità di bassa latenza per dispositivo, per gli ambienti di produzione è consigliabile usare griglia di eventi per monitorare le connessioni. Naturalmente, la scelta non è esclusiva. è possibile usare sia gli avvisi della metrica di monitoraggio di Azure che griglia di eventi. Indipendentemente dalla scelta effettuata per le disconnessioni di rilevamento, è probabile che si usino i log delle risorse di monitoraggio di Azure per risolvere i motivi della disconnessione imprevista del dispositivo. Le sezioni seguenti illustrano ciascuna di queste opzioni in modo più dettagliato.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Griglia di eventi: monitorare gli eventi di connessione e disconnessione del dispositivo

Per monitorare gli eventi di connessione e disconnessione del dispositivo nell'ambiente di produzione, è consigliabile sottoscrivere gli [eventi **DeviceConnected** e **DeviceDisconnected**](iot-hub-event-grid.md#event-types) in griglia di eventi per attivare gli avvisi e monitorare lo stato di connessione del dispositivo. Griglia di eventi offre una latenza di eventi molto inferiore rispetto a monitoraggio di Azure ed è possibile monitorarli in base ai singoli dispositivi, anziché per il numero totale di dispositivi connessi. Questi fattori rendono griglia di eventi il metodo preferito per il monitoraggio di dispositivi e infrastrutture critiche.

Quando si usa griglia di eventi per monitorare o attivare gli avvisi per le disconnessioni del dispositivo, assicurarsi di creare un metodo per filtrare le disconnessioni periodiche a causa del rinnovo dei token SAS nei dispositivi che usano gli SDK Azure. Per altre informazioni, vedere [comportamento di disconnessione del dispositivo MQTT con gli SDK Azure](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

Esaminare gli argomenti seguenti per ulteriori informazioni sul monitoraggio degli eventi di connessione del dispositivo con griglia di eventi:

* Per una panoramica dell'uso di griglia di eventi con l'hub [Internet.](iot-hub-event-grid.md) Prestare particolare attenzione alle limitazioni per la sezione relativa [agli eventi di connessione dispositivo e disconnessione del dispositivo](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* Per un'esercitazione sull'ordinamento degli eventi di connessione del dispositivo, vedere [ordinare gli eventi di connessione del dispositivo dall'hub degli eventi di Azure usando Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Per un'esercitazione sull'invio di notifiche tramite posta elettronica, vedere [inviare notifiche tramite posta elettronica sugli eventi dell'hub Azure Internet tramite griglia di eventi e app](../event-grid/publish-iot-hub-events-to-logic-apps.md) per la logica nella documentazione di griglia di eventi.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Monitoraggio di Azure: indirizzare gli eventi di connessione ai log

L'hub tutto emette continuamente log delle risorse per diverse categorie di operazioni. Per raccogliere i dati di log, tuttavia, è necessario creare un'impostazione di diagnostica per instradarla a una destinazione in cui possa essere analizzata o archiviata. Una di queste destinazioni è rappresentata dai log di monitoraggio di Azure tramite un'area di lavoro Log Analytics ([vedere i prezzi](https://azure.microsoft.com/pricing/details/log-analytics/)), in cui è possibile analizzare i dati usando le query kusto.

La [categoria connessioni log risorse](monitor-iot-hub-reference.md#connections) hub Internet genera operazioni ed errori che è necessario eseguire con le connessioni del dispositivo. Lo screenshot seguente mostra un'impostazione di diagnostica per indirizzare questi log a un'area di lavoro Log Analytics:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Impostazione consigliata per inviare i log di connettività all'area di lavoro Log Analytics.":::

Si consiglia di creare un'impostazione di diagnostica il prima possibile dopo aver creato l'hub di Internet delle cose, perché, sebbene l'hub tutto emetta sempre i log delle risorse, questi non vengono raccolti da monitoraggio di Azure finché non vengono indirizzati a una destinazione.

Per ulteriori informazioni sul routing dei log a una destinazione, vedere [raccolta e routing](monitor-iot-hub.md#collection-and-routing). Per istruzioni dettagliate su come creare un'impostazione di diagnostica, vedere l' [esercitazione usare metriche e log](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Monitoraggio di Azure: impostare gli avvisi sulle metriche per la disconnessione dei dispositivi su larga scala

È possibile configurare gli avvisi in base alle metriche della piattaforma emesse dall'hub. Con gli avvisi delle metriche è possibile notificare agli utenti che si è verificata una condizione di interesse e attivare anche azioni che possono rispondere automaticamente a tale condizione.

La metrica [*dispositivi connessi (anteprima)*](monitor-iot-hub-reference.md#device-metrics) indica il numero di dispositivi connessi all'hub Internet. È possibile creare avvisi da attivare se questa metrica scende al di sotto di un valore di soglia:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Impostazioni della logica di avviso per la metrica dei dispositivi connessi.":::

È possibile usare le regole di avviso delle metriche per monitorare le anomalie di disconnessione dei dispositivi su larga scala. Ovvero, quando un numero significativo di dispositivi si disconnette in modo imprevisto. Quando viene rilevata una tale occorrenza, è possibile esaminare i log per risolvere il problema. Per monitorare le disconnessioni per ogni dispositivo e le disconnessioni per i dispositivi critici; Tuttavia, è necessario usare griglia di eventi. Griglia di eventi offre anche una maggiore esperienza in tempo reale rispetto alle metriche di Azure.

Per altre informazioni sugli avvisi con l'hub Internet, vedere [avvisi nell'hub di monitoraggio](monitor-iot-hub.md#alerts). Per una procedura dettagliata per la creazione di avvisi nell'hub Internet, vedere l' [esercitazione usare metriche e log](tutorial-use-metrics-and-diags.md). Per una panoramica più dettagliata degli avvisi, vedere [Panoramica degli avvisi in Microsoft Azure](../azure-monitor/platform/alerts-overview.md) nella documentazione di monitoraggio di Azure.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Monitoraggio di Azure: usare i log per risolvere gli errori di connettività

Quando si rilevano disconnessioni del dispositivo, sia che si tratti di avvisi delle metriche di monitoraggio di Azure o di griglia di eventi, è possibile usare i log per risolvere il motivo. Questa sezione descrive come cercare problemi comuni nei log di monitoraggio di Azure. I passaggi seguenti presuppongono che sia già stata creata un' [impostazione di diagnostica](#azure-monitor-route-connection-events-to-logs) per inviare i log delle connessioni dell'hub Internet a un'area di lavoro log Analytics.

Dopo aver creato un'impostazione di diagnostica per indirizzare i log delle risorse dell'hub Internet ai log di monitoraggio di Azure, seguire questa procedura per visualizzare i log in portale di Azure.

1. Passare all'hub Internet in [portale di Azure](https://portal.azure.com).

1. In **monitoraggio** nel riquadro sinistro dell'hub Internet, selezionare **log**.

1. Per isolare i log degli errori di connettività per l'hub tutto, immettere la query seguente nell'editor di query e quindi selezionare **Esegui**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Se sono presenti risultati, cercare `OperationName`, `ResultType` (codice di errore) e `ResultDescription` (messaggio di errore) per ottenere altri dettagli sull'errore.

   ![Esempio di log degli errori](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Dopo aver identificato l'errore, seguire le guide alla risoluzione dei problemi per informazioni sugli errori più comuni:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Comportamento di disconnessione del dispositivo MQTT con gli SDK Azure

Gli SDK per dispositivi Azure Internet si disconnettono dall'hub Internet e quindi riconnettono quando rinnovano i token SAS tramite il protocollo MQTT (e MQTT over WebSocket). Nei log, questo viene visualizzato come disconnessione del dispositivo informativo ed eventi di connessione talvolta accompagnati da eventi di errore.

Per impostazione predefinita, la durata del token è di 60 minuti per tutti gli SDK. Tuttavia, può essere modificato dagli sviluppatori in alcuni degli SDK. La tabella seguente riepiloga il comportamento per la durata dei token, il rinnovo dei token e il rinnovo del token per ogni SDK:

| SDK | Durata token | Rinnovo del token | Comportamento di rinnovo |
|-----|----------|---------------------|---------|
| .NET | 60 minuti, configurabile | 85% di durata, configurabile | SDK si connette e si disconnette alla durata del token, oltre a un periodo di tolleranza di 10 minuti. Eventi informativi ed errori generati nei log. |
| Java | 60 minuti, configurabile | 85% di durata, non configurabile | SDK si connette e si disconnette alla durata del token, oltre a un periodo di tolleranza di 10 minuti. Eventi informativi ed errori generati nei log. |
| Node.js | 60 minuti, configurabile | configurabile | SDK si connette e si disconnette al rinnovo del token. Nei log vengono generati solo gli eventi informativi. |
| Python | 60 minuti, non configurabile | -- | L'SDK si connette e si disconnette alla durata del token. |

Gli screenshot seguenti mostrano il comportamento del rinnovo dei token nei log di monitoraggio di Azure per diversi SDK. La durata del token e la soglia di rinnovo sono state modificate rispetto ai valori predefiniti, come indicato.

* .NET Device SDK con una durata del token di 1200 secondi (20 minuti) e un rinnovo impostati per la durata del 90% della durata. le disconnessioni avvengono ogni 30 minuti:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Comportamento dell'errore per il rinnovo del token su MQTT nei log di monitoraggio di Azure con .NET SDK.":::

* Java SDK con una durata del token di 300 secondi (5 minuti) e il 85% predefinito del rinnovo della durata. Le disconnessioni avvengono ogni 15 minuti:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Comportamento dell'errore per il rinnovo del token su MQTT nei log di monitoraggio di Azure con Java SDK.":::

* Node SDK con una durata del token di 300 secondi (5 minuti) e il rinnovo del token impostati per essere eseguita in 3 minuti. Le disconnessioni si verificano durante il rinnovo del token. Inoltre, non sono presenti errori, vengono emessi solo eventi di connessione/disconnessione informativi:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Comportamento dell'errore per il rinnovo del token su MQTT nei log di monitoraggio di Azure con node SDK.":::

Per raccogliere i risultati, è stata utilizzata la query seguente. La query estrae il nome e la versione dell'SDK dal contenitore delle proprietà. per altre informazioni, vedere [versione SDK nei log dell'hub](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)Internet.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

In qualità di sviluppatore o operatore di soluzioni Internet, è necessario essere a conoscenza di questo comportamento per interpretare gli eventi di connessione/disconnessione e gli errori correlati nei log. Se si vuole modificare la durata del token o il comportamento di rinnovo per i dispositivi, verificare se il dispositivo implementa un'impostazione del dispositivo gemello o un metodo del dispositivo che lo rende possibile.

Se si esegue il monitoraggio delle connessioni del dispositivo con hub eventi, assicurarsi di compilare in un modo per filtrare le disconnessioni periodiche a causa del rinnovo del token SAS. Se, ad esempio, non si attivano le azioni in base alle disconnessioni, purché l'evento di disconnessione sia seguito da un evento Connect entro un determinato intervallo di tempo.

> [!NOTE]
> L'hub IoT supporta solo una connessione MQTT attiva per ogni dispositivo. Qualsiasi nuova connessione MQTT per conto dello stesso ID di dispositivo causa la perdita della connessione esistente da parte dell'hub IoT.
>
> 400027 ConnectionForcefullyClosedOnNewConnection verrà registrato nei log dell'hub degli eventi

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