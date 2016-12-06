> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> 
> 

## <a name="introduction"></a>Introduzione
I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che viene connesso all'hub IoT.

Usare i dispositivi gemelli per:

* Archiviare i metadati dei dispositivi dal back-end.
* Segnalare informazioni sullo stato corrente, come funzionalità disponibili e condizioni (ad esempio, il metodo di connettività usato) dall'app per dispositivi.
* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata (come gli aggiornamenti del firmware e della configurazione) tra l'app per dispositivi e il back-end.
* Eseguire query sui metadati, la configurazione o lo stato dei dispositivi.

> [!NOTE]
> I dispositivi gemelli sono progettati per la sincronizzazione e per l'esecuzione di query sulle configurazioni e le condizioni dei dispositivi. Usare [messaggi da dispositivo a cloud][lnk-d2c] per le sequenze di eventi con timestamp (come i flussi di telemetria dei dati dei sensori su base temporale) e [metodi diretti][lnk-methods] per il controllo interattivo dei dispositivi, ad esempio per l'attivazione di una ventola da un'app controllata dall'utente.
> 
> 

I dispositivi gemelli vengono archiviati in un hub IoT e contengono gli elementi seguenti.

* *Tag*: metadati dei dispositivi accessibili solo dal back-end
* *Proprietà desiderate*: oggetti JSON modificabili dal back-end e osservabili dall'app per dispositivi
* *Proprietà segnalate*: oggetti JSON modificabili dall'app per dispositivi e leggibili dal back-end I tag e le proprietà non possono contenere matrici, ma gli oggetti possono essere annidati.

![][img-twin]

Il back-end dell'app può anche eseguire query sui dispositivi gemelli in base a tutti i dati sopra indicati.
Per altre informazioni sui dispositivi gemelli e sulle query, vedere rispettivamente [Informazioni sui dispositivi gemelli][lnk-twins] e le informazioni di riferimento sul [linguaggio di query dell'hub IoT][lnk-query].

> [!NOTE]
> Al momento i dispositivi gemelli sono accessibili solo dai dispositivi che si connettono all'hub IoT usando il protocollo MQTT. Per istruzioni su come convertire l'app per dispositivo esistente in modo che usi MQTT, leggere l'articolo [Supporto di MQTT][lnk-devguide-mqtt].
> 
> 

Questa esercitazione illustra come:

* Creare un'app back-end che aggiunge *tag* a un dispositivo gemello e un dispositivo simulato che segnala il proprio canale di connettività in una *proprietà segnalata* nel dispositivo gemello.
* Eseguire query sui dispositivi dall'app back-end con filtri sui tag e sulle proprietà creati in precedenza.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

<!--HONumber=Nov16_HO3-->


