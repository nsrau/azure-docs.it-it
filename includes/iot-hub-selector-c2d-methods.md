> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-direct-methods.md)

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Le esercitazioni precedenti, ad esempio [Introduzione all'hub IoT] e [Inviare messaggi da cloud a dispositivo con l'hub IoT], illustrano le funzionalità di messaggistica di base da dispositivo a cloud e da cloud a dispositivo dell'hub IoT. L'hub IoT offre inoltre la possibilità di richiamare metodi non durevoli dal cloud ai dispositivi. I metodi diretti rappresentano un'interazione di tipo richiesta-risposta con un dispositivo simile a una chiamata HTTP, perché dopo il timeout specificato dall'utente l'esito positivo o negativo viene comunicato immediatamente per far conoscere all'utente lo stato della chiamata. [Richiamare un metodo diretto in un dispositivo ][lnk-devguide-methods] descrive in maniera dettagliata i metodi diretti e illustra quando usarli in alternativa ai messaggi da cloud a dispositivo o alle proprietà desiderate.

Questa esercitazione illustra come:

* Creare un hub IoT nel portale di Azure e un'identità del dispositivo nell'hub IoT.
* Creare un'app per dispositivo simulato che ha un metodo diretto che può essere chiamato dal cloud.
* Creare un'app console che chiama un metodo diretto nell'app per dispositivo simulato tramite l'hub IoT.

> [!NOTE]
> Al momento i metodi diretti sono supportati solo sui dispositivi che si connettono all'hub IoT usando il protocollo MQTT. Per istruzioni su come convertire l'app per dispositivo esistente in modo che usi MQTT, vedere l'articolo [Supporto di MQTT][lnk-devguide-mqtt].


[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[Inviare messaggi da cloud a dispositivo con l'hub IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[Introduzione all'hub IoT]: ../articles/iot-hub/iot-hub-node-node-getstarted.md