> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Questo articolo descrive la procedura dettagliata del [codice di esempio Hello World][lnk-helloworld-sample] per illustrare i componenti fondamentali dell'architettura di [Azure IoT Edge][lnk-iot-edge]. L'esempio usa Azure IoT Edge per creare un gateway semplice che registri un messaggio "hello world" in un file ogni cinque secondi.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

* **Architettura di esempio Hello World**: descrive in che modo si applicano i [concetti dell'architettura di Azure IoT Edge][lnk-edge-concepts] all'esempio Hello World e come vengono assemblati i componenti.
* **Come compilare l'esempio**: i passaggi richiesti per compilare l'esempio.
* **Come eseguire l'esempio**: i passaggi richiesti per eseguire l'esempio. 
* **Output tipico**: un esempio del possibile output risultante quando si esegue l'esempio.
* **Frammenti di codice**: raccolta di frammenti di codice per mostrare come l'esempio Hello World implementa i componenti principali del gateway IoT Edge.


## <a name="hello-world-sample-architecture"></a>Architettura di esempio Hello World
L'esempio Hello World illustra i concetti descritti nella sezione precedente. L'esempio Hello World implementa un gateway IoT Edge la cui pipeline è costituita da due moduli di IoT Edge:

* Il modulo *hello world* crea un messaggio ogni cinque secondi e lo passa al modulo logger.
* Il modulo *logger* scrive i messaggi che riceve in un file.

![Architettura dell'esempio Hello World compilato con Azure IoT Edge][4]

Come descritto nella sezione precedente, il modulo Hello World non passa i messaggi direttamente al modulo logger ogni cinque secondi, ma pubblica un messaggio nel broker ogni cinque secondi.

Il modulo logger riceve il messaggio dal broker e interviene, scrivendo il contenuto del messaggio in un file.

Il modulo logger utilizza solo i messaggi provenienti dal broker, senza mai pubblicare nuovi messaggi nel broker.

![Modo in cui il broker indirizza i messaggi tra i moduli in Azure IoT Edge][5]

La figura precedente illustra l'architettura dell'esempio Hello World e i percorsi relativi ai file di origine che implementano parti diverse dell'esempio nel [repository][lnk-iot-edge]. Esplorare il codice per conto proprio o usare i frammenti di codice di questo articolo come guida.

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md