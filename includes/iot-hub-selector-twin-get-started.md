> [!div class="op_single_selector"]
> * [Node.JS](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [C#/Node.js](../articles/iot-hub/iot-hub-csharp-node-twin-getstarted.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette.

Usare i dispositivi gemelli per:

* Archiviare i metadati dei dispositivi dal back-end della soluzione.
* Segnalare informazioni sullo stato corrente, come funzionalità disponibili e condizioni (ad esempio, il metodo di connettività usato) dall'app per dispositivi.
* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata (come gli aggiornamenti del firmware e della configurazione) tra un'app per dispositivi e un'app di back-end.
* Eseguire query sui metadati, la configurazione o lo stato dei dispositivi.

> [!NOTE]
> I dispositivi gemelli sono progettati per la sincronizzazione e per l'esecuzione di query sulle configurazioni e le condizioni dei dispositivi. Altre informazioni su quando usare i dispositivi gemelli sono reperibili in [Informazioni sui dispositivi gemelli][lnk-twins].

I dispositivi gemelli vengono archiviati in un hub IoT e contengono gli elementi seguenti.

* *Tag*: metadati dei dispositivi accessibili solo dal back-end della soluzione
* *Proprietà desiderate*: oggetti JSON modificabili dal back-end della soluzione e osservabili dall'app per dispositivi
* *Proprietà segnalate*: oggetti JSON modificabili dall'app per dispositivi e leggibili dal back-end della soluzione I tag e le proprietà non possono contenere matrici, ma gli oggetti possono essere annidati.

![][img-twin]

Il back-end della soluzione può anche eseguire query sui dispositivi gemelli in base a tutti i dati sopra indicati.
Vedere [Informazioni sui dispositivi gemelli][lnk-twins] per altre informazioni sui dispositivi gemelli e [Linguaggio di query per hub IoT][lnk-query] per informazioni sull'esecuzione di query.


Questa esercitazione illustra come:

* Creare un'app back-end che aggiunge *tag* a un dispositivo gemello e un'app per dispositivo simulato che segnala il proprio canale di connettività come *proprietà segnalata* nel dispositivo gemello.
* Eseguire query sui dispositivi dall'app back-end con filtri sui tag e sulle proprietà creati in precedenza.

<!-- images -->
[img-twin]: media/iot-hub-selector-twin-get-started/twin.png

<!-- links -->
[lnk-query]: ../articles/iot-hub/iot-hub-devguide-query-language.md
[lnk-twins]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-d2c]: ../articles/iot-hub/iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md