> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-simulated-device.md)

La procedura dettagliata dell'[esempio di caricamento nel cloud del dispositivo simulato] illustra come usare [Azure IoT Edge][lnk-sdk] per inviare dati di telemetria da dispositivo a cloud all'hub IoT da dispositivi simulati.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

* **Architettura**: informazioni sull'architettura relative all'[esempio di caricamento nel cloud del dispositivo simulato].
* **Compilazione ed esecuzione**: i passaggi richiesti per compilare ed eseguire l'esempio.

## <a name="architecture"></a>Architettura

L'[esempio di caricamento nel cloud del dispositivo simulato] mostra come creare un gateway che invia i dati di telemetria da dispositivi simulati a un hub IoT. Un dispositivo potrebbe non essere in grado di connettersi direttamente all'hub IoT perché il dispositivo:

* Non usa un protocollo di comunicazione riconosciuto dall'hub IoT.
* Non riesce a memorizzare l'identità assegnata dall'hub IoT.

Un gateway IoT Edge può risolvere questi problemi nei modi seguenti:

* Il gateway riconosce il protocollo usato dal dispositivo, riceve i dati di telemetria da dispositivo a cloud dal dispositivo e inoltra i messaggi all'hub IoT usando un protocollo riconosciuto dall'hub IoT.

* Il gateway esegue il mapping delle identità dell'hub IoT ai dispositivi e funge da proxy quando un dispositivo invia messaggi all'hub IoT.

Il diagramma seguente illustra i componenti principali dell'esempio, inclusi i moduli di IoT Edge:

![Diagramma - Il messaggio del dispositivo simulato arriva all'hub IoT attraverso il gateway][1]

Questo esempio include tre moduli che costituiscono il gateway:
1. Modulo di inserimento del protocollo
1. Modulo ID MAC &lt;-&gt; IoT Hub
1. Modulo di comunicazione dell'hub IoT

I moduli non si scambiano direttamente i messaggi, ma li pubblicano in un broker interno che li invia ad altri moduli usando un meccanismo di sottoscrizione. Per altre informazioni, vedere [Get started with Azure IoT Edge][lnk-gw-getstarted] (Introduzione ad Azure IoT Edge).

![Diagramma - I moduli del gateway comunicano con il broker][2]

### <a name="protocol-ingestion-module"></a>Modulo di inserimento del protocollo

Questo modulo è il punto di partenza per il processo di ricezione dei dati dai dispositivi e trasferimento degli stessi al cloud attraverso il gateway. 

Nell'esempio il modulo esegue le attività seguenti:

1. Crea dati di temperatura simulati. Se vengono usati dispositivi fisici, il modulo legge i dati da tali dispositivi fisici.
1. Crea un messaggio.
1. Inserisce i dati di temperatura simulati nel contenuto del messaggio.
1. Aggiunge una proprietà con un indirizzo MAC fittizio al messaggio.
1. Rende disponibile il messaggio al modulo successivo nella catena.

Il modulo di inserimento del protocollo è **simulated_device.c** nel codice sorgente.

### <a name="mac-lt-gt-iot-hub-id-module"></a>Modulo ID MAC &lt;-&gt; IoT Hub

Il modulo ID MAC &lt; - &gt; IoT Hub funge da convertitore. Questo esempio usa un indirizzo MAC come identificatore univoco del dispositivo e lo associa a un'identità dei dispositivi di un hub IoT. Tuttavia, è possibile scrivere un modulo personalizzato che usa un identificatore univoco diverso. Ad esempio, i dispositivi possono avere numeri di serie univoci o i dati di telemetria possono includere un nome univoco del dispositivo incorporato.

Nell'esempio il modulo esegue le attività seguenti:

1. Analizza i messaggi che includono una proprietà di indirizzo MAC.
1. Se è presente un indirizzo MAC, aggiunge un'altra proprietà con una chiave del dispositivo dell'hub IoT al messaggio. 
1. Rende disponibile il messaggio al modulo successivo nella catena.

Lo sviluppatore imposta il mapping tra gli indirizzi MAC e le identità dell'hub IoT per associare i dispositivi simulati alle identità del dispositivo dell'hub IoT. Lo sviluppatore aggiunge manualmente il mapping durante la configurazione del modulo.

Il modulo ID MAC &lt; - &gt; IoT Hub è **identitymap.c** nel codice sorgente. 

### <a name="iot-hub-communication-module"></a>Modulo di comunicazione dell'hub IoT

Questo modulo apre una singola connessione HTTP dal gateway all'hub IoT. HTTP è uno dei tre protocolli riconosciuti dall'hub IoT. Invece di aprire una connessione per ogni dispositivo, questo modulo apre una singola connessione sulla quale esegue il multiplexing delle connessioni da tutti i dispositivi. Questo approccio consente a un singolo gateway di connettersi a molti dispositivi. 

Nell'esempio il modulo esegue le attività seguenti:

1. Accetta i messaggi con una proprietà chiave dei dispositivi di un hub IoT assegnata dal modulo precedente. 
1. Invia il contenuto del messaggio all'hub IoT usando il protocollo HTTP. 

Il modulo di comunicazione dell'hub IoT è **iothub.c** nel codice sorgente.

## <a name="before-you-get-started"></a>Prima di iniziare

Prima di iniziare:

* [Creare un hub IoT ][lnk-create-hub] nella propria sottoscrizione di Azure. Per questa procedura dettagliata di esempio è necessario il nome dell'hub. Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* Aggiungere due dispositivi all'hub IoT e annotare i relativi ID e le chiavi di dispositivo. È possibile usare gli strumenti [Esplora dispositivi][lnk-device-explorer] o [iothub-explorer][lnk-iothub-explorer] per aggiungere i dispositivi all'hub IoT e recuperarne le chiavi.


<!-- Images -->
[1]: media/iot-hub-iot-edge-simulated-selector/image1.png
[2]: media/iot-hub-iot-edge-simulated-selector/image2.png

<!-- Links -->
[esempio di caricamento nel cloud del dispositivo simulato]: https://github.com/Azure/iot-edge/blob/master/samples/simulated_device_cloud_upload/README.md
[lnk-sdk]: https://github.com/Azure/iot-edge
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer
[lnk-iothub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md
[lnk-create-hub]: ../articles/iot-hub/iot-hub-create-through-portal.md