---
title: Informazioni su come il runtime gestisce i dispositivi - Azure IoT Edge | Microsoft Docs
description: Informazioni sul modo in cui il runtime di IoT Edge gestisce i moduli, la sicurezza, la comunicazione e la creazione di report nei dispositivi
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 133be436853ee8c2b04df2f943368513108b226b
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444308"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Informazioni sul runtime di Azure IoT Edge e sulla relativa architettura

Il runtime Azure IoT Edge è una raccolta di programmi che trasformano un dispositivo in un dispositivo IoT Edge. Collettivamente, i componenti di runtime IoT Edge consentono IoT Edge ai dispositivi di ricevere il codice per l'esecuzione al perimetro e di comunicare i risultati.

Il runtime di IoT Edge è responsabile delle funzioni seguenti nei dispositivi IoT Edge:

* Installare e aggiornare i carichi di lavoro nel dispositivo.

* Mantenere gli standard di sicurezza di Azure IoT Edge sul dispositivo.

* Verificare che i [moduli IOT Edge](iot-edge-modules.md) siano sempre in esecuzione.

* Segnalare l'integrità dei moduli al cloud per il monitoraggio remoto.

* Gestire la comunicazione tra dispositivi downstream e dispositivi IoT Edge.

* Gestire la comunicazione tra i moduli in un dispositivo IoT Edge.

* Gestire la comunicazione tra un dispositivo IoT Edge e il cloud.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Gestire la comunicazione tra dispositivi IoT Edge.
::: moniker-end

![Il runtime comunica informazioni dettagliate e l'integrità del modulo all'hub IoT](./media/iot-edge-runtime/Pipeline.png)

Le responsabilità del runtime di IoT Edge rientrano in due categorie: comunicazione e gestione dei moduli. Questi due ruoli vengono eseguiti da due componenti che fanno parte del runtime di IoT Edge. L' *agente IOT Edge* distribuisce e monitora i moduli, mentre l' *Hub IOT Edge* è responsabile della comunicazione.

Sia l'agente IoT Edge che l'hub IoT Edge sono moduli, esattamente come qualsiasi altro modulo eseguito in un dispositivo IoT Edge. Sono talvolta definiti *moduli di runtime*.

## <a name="iot-edge-agent"></a>Agente di IoT Edge

IoT Edge Agent è uno dei due moduli che costituiscono il runtime di Azure IoT Edge. È responsabile della creazione di istanze per i moduli, ne garantisce la continua esecuzione e segnala lo stato dei moduli all'hub IoT. I dati di configurazione vengono scritti come proprietà del modulo gemello di IoT Edge Agent.

Il [daemon di sicurezza di IoT Edge](iot-edge-security-manager.md) avvia l'agente di IoT Edge all'avvio del dispositivo. L'agente recupera il modulo gemello dall'hub IoT e controlla il manifesto della distribuzione. Il manifesto della distribuzione è un file JSON che dichiara i moduli da avviare.

Ogni elemento nel manifesto di distribuzione contiene informazioni specifiche su un modulo e viene usato dall'agente di IoT Edge per controllare il ciclo di vita del modulo. Per altre informazioni su tutte le proprietà usate dall'agente di IoT Edge per controllare i moduli, vedere le [proprietà dell'agente IOT Edge e dei dispositivi gemelli del modulo hub IOT Edge](module-edgeagent-edgehub.md).

L'agente di IoT Edge invia la risposta runtime all'hub IoT. Ecco un elenco di risposte possibili:
  
* 200 - OK
* 400 - La configurazione della distribuzione è in formato non corretto o non valida.
* 417: il dispositivo non ha un set di configurazione della distribuzione.
* 412 - La versione dello schema nella configurazione della distribuzione non è valida.
* 406 - Il dispositivo è offline o non invia segnalazioni sullo stato.
* 500 - Si è verificato un errore nel runtime di IoT Edge.

Per ulteriori informazioni sulla creazione di manifesti di distribuzione, vedere [informazioni su come distribuire moduli e stabilire route in IOT Edge](module-composition.md).

### <a name="security"></a>Sicurezza

L'agente di IoT Edge svolge un ruolo fondamentale nella protezione di un dispositivo di IoT Edge. Ad esempio, esegue azioni come la verifica dell'immagine di un modulo prima di avviarlo.

Per ulteriori informazioni sul Framework di sicurezza di Azure IoT Edge, vedere [IOT Edge Security Manager](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>Hub di IoT Edge

L'hub IoT Edge è l'altro modulo che costituisce l'Azure IoT Edge Runtime. Opera come un proxy locale per l'hub IoT esponendo gli stessi endpoint del protocollo dell'hub IoT. Questa coerenza significa che i client possono connettersi al runtime di IoT Edge come per l'hub.

L'hub IoT Edge non è una versione completa dell'hub Internet in esecuzione in locale. IoT Edge Hub delega in modo invisibile all'utente alcune attività nell'hub. Ad esempio, IoT Edge Hub Scarica automaticamente le informazioni di autorizzazione dall'hub Internet alla prima connessione per consentire a un dispositivo di connettersi. Una volta stabilita la prima connessione, le informazioni di autorizzazione vengono memorizzate nella cache in locale dall'hub IoT Edge. Le connessioni future da tale dispositivo sono autorizzate senza dover scaricare di nuovo le informazioni di autorizzazione dal cloud.

### <a name="cloud-communication"></a>Comunicazione cloud

Per ridurre la larghezza di banda usata dalla soluzione IoT Edge, l'hub IoT Edge Ottimizza il numero di connessioni effettive effettuate al cloud. IoT Edge Hub prende le connessioni logiche da moduli o dispositivi downstream e li combina per una singola connessione fisica al cloud. I dettagli di questo processo sono trasparenti per il resto della soluzione. Ai client sembra di avere ognuno la propria la connessione al cloud ma in realtà usano tutti la stessa connessione. L'hub IoT Edge può usare il protocollo AMQP o MQTT per comunicare upstream con il cloud, indipendentemente dai protocolli usati dai dispositivi downstream. Tuttavia, l'hub IoT Edge supporta attualmente solo la combinazione di connessioni logiche in una singola connessione fisica usando AMQP come protocollo upstream e le sue funzionalità di multiplexing. AMQP è il protocollo upstream predefinito.

![L'hub di IoT Edge è un gateway tra dispositivi fisici e l'hub IoT](./media/iot-edge-runtime/Gateway.png)

L'hub di IoT Edge può determinare se è connesso all'hub IoT. Se la connessione viene persa, l'hub di IoT Edge salva i messaggi o gli aggiornamenti gemelli in locale. Quando viene ristabilita la connessione, tutti i dati vengono sincronizzati. Il percorso usato per questa cache temporanea è determinato da una proprietà del gemello del modulo dell'hub IoT Edge. Le dimensioni della cache non sono limitate e aumentano fino a quando il dispositivo ha capacità di archiviazione. Per ulteriori informazioni, vedere [funzionalità offline](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Comunicazione del modulo

L'hub di IoT Edge facilita la comunicazione tra moduli. L'uso dell'hub di IoT Edge come broker di messaggi consente ai moduli di rimanere indipendenti. Per i moduli è sufficiente specificare gli input su cui accettano i messaggi e gli output su cui scrivono i messaggi. Uno sviluppatore di soluzioni può unire questi input e output in modo che i moduli elaborino i dati nell'ordine specifico della soluzione.

![L'hub di IoT Edge facilita la comunicazione tra moduli](./media/iot-edge-runtime/module-endpoints.png)

Per inviare i dati all'hub di IoT Edge, un modulo chiama il metodo SendEventAsync. Il primo argomento specifica su quale output inviare il messaggio. Lo pseudocodice seguente invia un messaggio in **output1** :

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Per ricevere un messaggio, registrare un callback che elabori i messaggi in arrivo in un input specifico. Lo pseudocodice seguente registra la funzione messageProcessor da usare per l'elaborazione di tutti i messaggi ricevuti in **INPUT1** :

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Per altre informazioni sulla classe ModuleClient e sui relativi metodi di comunicazione, vedere le informazioni di riferimento sulle API per il linguaggio SDK preferito: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)o [Node.js](/javascript/api/azure-iot-device/moduleclient).

Lo sviluppatore di soluzioni è responsabile della definizione delle regole che determinano il modo in cui l'hub di IoT Edge passa i messaggi tra i moduli. Le regole di routing sono definite nel cloud ed è stato eseguito il push all'hub IoT Edge nel gemello del modulo. La stessa sintassi per le route dell'hub IoT viene usata per definire le route tra i moduli di Azure IoT Edge. Per altre informazioni, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

![Le route tra i moduli passano attraverso l'hub di IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Comunicazione locale

IoT Edge Hub facilita la comunicazione locale. Consente le comunicazioni da dispositivo a modulo, da modulo a modulo e da dispositivo a dispositivo tramite l'intermediazione dei messaggi per proteggere i dispositivi e i moduli in modo indipendente.

>[!NOTE]
> La funzionalità MQTT Broker è in anteprima pubblica con IoT Edge versione 1,2. Deve essere abilitata in modo esplicito.

L'hub IoT Edge supporta due meccanismi di Service Broker:

1. [Funzionalità di routing dei messaggi supportate dall'hub](../iot-hub/iot-hub-devguide-messages-d2c.md) Internet e
2. Un broker MQTT per utilizzo generico che soddisfa lo [standard MQTT v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Uso del routing

Il primo meccanismo di Service Broker sfrutta le stesse funzionalità di routing dell'hub Internet per specificare il modo in cui i messaggi vengono passati tra dispositivi o moduli. I primi dispositivi o moduli specificano gli input sui quali accettano i messaggi e gli output in cui scrivono i messaggi. Uno sviluppatore di soluzioni può quindi instradare i messaggi tra un'origine, ad esempio gli output, e una destinazione, ad esempio gli input, con filtri potenziali.

![Le route tra i moduli passano attraverso l'hub di IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

Il routing può essere usato da dispositivi o moduli compilati con gli SDK per dispositivi Azure Internet, tramite il protocollo AMQP o MQTT. Sono supportate tutte le primitive dell'hub degli elementi di messaggistica, ad esempio i dati di telemetria, i metodi diretti, C2D, i gemelli, ma la comunicazione sugli argomenti definiti dall'utente non è supportata.

Per ulteriori informazioni sulle route, vedere [informazioni su come distribuire moduli e definire route in IOT Edge](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>Uso di MQTT broker

Il secondo meccanismo di Service Broker si basa su un broker MQTT standard. MQTT è un protocollo di trasferimento di messaggi leggero che garantisce prestazioni ottimali nei dispositivi con vincoli di risorse ed è un diffuso standard di pubblicazione e sottoscrizione. I dispositivi o i moduli sottoscrivono gli argomenti per ricevere i messaggi pubblicati da altri dispositivi o moduli. IoT Edge Hub implementa il proprio broker MQTT che segue [le specifiche di MQTT versione 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

MQTT Broker consente due modelli di comunicazione aggiuntivi rispetto al routing: trasmissione locale e comunicazione Point-to-Point. La trasmissione locale è utile quando un dispositivo o un modulo deve inviare avvisi localmente a più dispositivi o moduli. La comunicazione punto a punto consente a due dispositivi IoT Edge o due dispositivi Internet per comunicare localmente senza round trip al cloud.

![Pubblicare e sottoscrivere localmente con IoT Edge Hub](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

MQTT broker può essere usato da dispositivi o moduli compilati con gli SDK per dispositivi Azure per dispositivi che comunicano tramite il protocollo MQTT o qualsiasi client MQTT per utilizzo generico. Ad eccezione di C2D tutte le primitive dell'hub degli argomenti di messaggistica, ad esempio i dati di telemetria, i metodi diretti e i gemelli sono supportati. Gli argomenti speciali dell'hub tutto usati dalle primitive dell'hub Internet sono supportati e pertanto sono argomenti definiti dall'utente.
Questo argomento può essere un argomento speciale dell'hub Internet o un argomento definito dall'utente.

Diversamente dal meccanismo di routing, l'ordine dei messaggi è solo il massimo sforzo e non è garantito e il filtraggio dei messaggi non è supportato dal broker. La mancanza di queste funzionalità consente tuttavia al broker di MQTT di essere più veloce del routing.

Per ulteriori informazioni su MQTT broker, vedere [pubblicare e sottoscrivere con IOT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Confronto tra meccanismi di Service Broker

Di seguito sono riportate le funzionalità disponibili in ogni meccanismo di Service Broker:

|Funzionalità  | Routing  | Broker MQTT  |
|---------|---------|---------|
|Telemetria D2C    |     &#10004;    |         |
|Telemetria locale     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Gemello     |    &#10004;     |    &#10004;     |
|C2D per i dispositivi     |   &#10004;      |         |
|Ordering     |    &#10004;     |         |
|Filtro     |     &#10004;    |         |
|Argomenti definiti dall'utente     |         |    &#10004;     |
|Da dispositivo a dispositivo     |         |    &#10004;     |
|Broadcast locale     |         |    &#10004;     |
|Prestazioni     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Connessione all'hub IoT Edge

L'hub IoT Edge accetta connessioni da client di dispositivi o moduli, tramite il protocollo MQTT o il protocollo AMQP.

>[!NOTE]
> IoT Edge Hub supporta i client che si connettono tramite MQTT o AMQP. Non supporta i client che usano HTTP.

Quando un client si connette all'hub IoT Edge, si verifica quanto segue:

1. Se si usa Transport Layer Security (TLS) (scelta consigliata), viene creato un canale TLS per stabilire una comunicazione crittografata tra il client e l'hub IoT Edge.
2. Le informazioni di autenticazione vengono inviate dal client all'hub IoT Edge per identificarsi.
3. IoT Edge Hub autorizza o rifiuta la connessione in base ai criteri di autorizzazione.

#### <a name="secure-connections-tls"></a>Connessioni protette (TLS)

Per impostazione predefinita, l'hub IoT Edge accetta solo connessioni protette con Transport Layer Security (TLS), ad esempio connessioni crittografate che non possono essere decrittografate da terze parti.

Se un client si connette sulla porta 8883 (MQTTS) o 5671 (AMQPS) all'hub IoT Edge, è necessario compilare un canale TLS. Durante l'handshake TLS, l'hub IoT Edge invia la relativa catena di certificati che deve essere convalidata dal client. Per convalidare la catena di certificati, è necessario installare il certificato radice dell'hub IoT Edge come certificato attendibile nel client. Se il certificato radice non è attendibile, la libreria client verrà rifiutata dall'hub IoT Edge con un errore di verifica del certificato.

I passaggi da seguire per installare il certificato radice del Broker nei client dei dispositivi sono descritti in [gateway trasparente](how-to-create-transparent-gateway.md) e nella documentazione [preparare un dispositivo downstream](how-to-connect-downstream-device.md#prepare-a-downstream-device) . I moduli possono usare lo stesso certificato radice dell'hub IoT Edge sfruttando l'API daemon di IoT Edge.

#### <a name="authentication"></a>Authentication

L'hub IoT Edge accetta solo connessioni da dispositivi o moduli che hanno un'identità dell'hub Internet, ad esempio, che sono stati registrati nell'hub Internet e hanno uno dei tre metodi di autenticazione client supportati dall'hub Internet per dimostrare la propria identità: [autenticazione con chiavi simmetriche](how-to-authenticate-downstream-device.md#symmetric-key-authentication), autenticazione [autofirmata x](how-to-authenticate-downstream-device.md#x509-self-signed-authentication). 509, [autenticazione con firma CA x. 509](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Queste identità dell'hub delle cose possono essere verificate localmente dall'hub IoT Edge in modo che le connessioni possano essere ancora effettuate in modalità offline.

Note:

* I moduli IoT Edge attualmente supportano solo l'autenticazione con chiave simmetrica.
* I client MQTT con solo nome utente e password locali non sono accettati dal broker MQTT Hub IoT Edge, ma devono usare le identità dell'hub Internet.

#### <a name="authorization"></a>Autorizzazione

Una volta eseguita l'autenticazione, l'hub IoT Edge dispone di due modi per autorizzare le connessioni client:

* Verificando che un client appartenga al proprio set di client attendibili definiti nell'hub Internet. Il set di client attendibili viene specificato impostando le relazioni padre/figlio o dispositivo/modulo nell'hub Internet. Quando viene creato un modulo in IoT Edge, viene stabilita automaticamente una relazione di trust tra questo modulo e il relativo dispositivo IoT Edge. Si tratta dell'unico modello di autorizzazione supportato dal meccanismo di broker di routing.

* Impostando un criterio di autorizzazione. Questo criterio di autorizzazione è un documento che elenca tutte le identità client autorizzate che possono accedere alle risorse nell'hub IoT Edge. Questo è il modello di autorizzazione principale usato da IoT Edge broker MQTT Hub, sebbene le relazioni padre/figlio e del dispositivo/modulo possano essere riconosciute anche dagli argomenti dell'hub MQTT broker for Internet.

### <a name="remote-configuration"></a>Configurazione remota

L'hub IoT Edge è interamente controllato dal cloud. Ottiene la configurazione dall'hub di Internet delle cose tramite il [modulo gemello](iot-edge-modules.md#module-twins). Sono inclusi:

* Configurazione Route
* I Criteri di autorizzazione
* Configurazione del Bridge MQTT

Inoltre, è possibile eseguire diverse configurazioni impostando [le variabili di ambiente nell'hub IOT Edge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetria di qualità Runtime

IoT Edge raccoglie i dati di telemetria anonimi dal runtime host e dai moduli di sistema per migliorare la qualità del prodotto. Queste informazioni sono denominate telemetria di qualità Runtime. I dati di telemetria raccolti vengono inviati periodicamente come messaggi da dispositivo a cloud all'hub delle cose dall'agente IoT Edge. Questi messaggi non vengono visualizzati nei dati di telemetria regolari del cliente e non utilizzano alcuna quota di messaggi.

L'agente IoT Edge e l'hub generano metriche che è possibile raccogliere per comprendere le prestazioni del dispositivo. Un subset di queste metriche viene raccolto dall'agente di IoT Edge come parte dei dati di telemetria di qualità Runtime. Le metriche raccolte per la telemetria di qualità runtime sono contrassegnate con il tag `ms_telemetry` . Per informazioni su tutte le metriche disponibili, vedere [accedere alle metriche predefinite](how-to-access-built-in-metrics.md).

Tutte le informazioni personali o organizzative, ad esempio i nomi di dispositivi e moduli, vengono rimosse prima del caricamento per garantire la natura anonima dei dati di telemetria di qualità Runtime.

L'agente di IoT Edge raccoglie i dati di telemetria ogni ora e invia un messaggio all'hub Internet ogni 24 ore.

Se si vuole rifiutare esplicitamente l'invio dei dati di telemetria di runtime dai dispositivi, è possibile procedere in due modi:

* Impostare la `SendRuntimeQualityTelemetry` variabile di ambiente su `false` per **edgeAgent** o
* Deselezionare l'opzione nel portale di Azure durante la distribuzione.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)
* [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md)
* [Informazioni su come pubblicare e sottoscrivere con IoT Edge](how-to-publish-subscribe.md)
* [Informazioni sulle metriche di runtime IoT Edge](how-to-access-built-in-metrics.md)
