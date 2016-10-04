> [AZURE.SELECTOR]
- [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md)
- [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-get-started.md)

Questo articolo descrive la procedura dettagliata del [codice di esempio Hello World][lnk-helloworld-sample] per illustrare i componenti fondamentali dell'architettura di [Azure IoT Gateway SDK][lnk-gateway-sdk]. L'esempio usa il Gateway SDK per creare un gateway semplice che registri un messaggio "hello world" in un file ogni cinque secondi.

In questa procedura dettagliata verranno trattati i seguenti argomenti:

- **Concetti**: una panoramica concettuale dei componenti che costituiscono un gateway creato con il Gateway SDK.
- **Architettura di esempio Hello World**: descrive in che modo si applicano i concetti all'esempio Hello World e come vengono assemblati i componenti.
- **Come compilare l'esempio**: i passaggi richiesti per compilare l'esempio.
- **Come eseguire l'esempio**: i passaggi richiesti per eseguire l'esempio.
- **Output tipico**: un esempio del possibile output risultante quando si esegue l'esempio.
- **Frammenti di codice**: una raccolta di frammenti di codice per mostrare in che modo l'esempio Hello World implementa i componenti principali del gateway.

## Concetti di Gateway SDK

Prima di esaminare il codice di esempio o di creare il proprio gateway sul campo usando Gateway SDK, è necessario comprendere i concetti principali che supportano l'architettura dell'SDK.

### Moduli

Per compilare un gateway con Azure IoT Gateway SDK è necessario creare e assemblare dei *moduli*. I moduli usano *messaggi* per scambiarsi dati tra loro. Un modulo riceve un messaggio, esegue un'azione su di esso, lo trasforma facoltativamente in un nuovo messaggio e quindi lo pubblica per l'elaborazione da parte di altri moduli. Alcuni moduli potrebbero produrre solo nuovi messaggi e non elaborare mai i messaggi in arrivo. Una catena di moduli crea una pipeline di elaborazione dei dati in cui ogni modulo esegue una trasformazione sui dati in un punto nella pipeline.

![][1]
 
L'SDK contiene quanto segue:

- Moduli già scritti che eseguono funzioni di gateway comuni.
- Le interfacce che uno sviluppatore può usare per scrivere i moduli personalizzati.
- L'infrastruttura necessaria per distribuire ed eseguire un set di moduli.

L'SDK fornisce un livello di astrazione che consente di creare i gateway da eseguire in una vasta gamma di sistemi operativi e piattaforme.

![][2]

### Messaggi

Nonostante sia più semplice concettualizzare il funzionamento di un gateway come una trasmissione di messaggi tra moduli, ciò non lo riflette con precisione. I moduli usano un broker per comunicare tra loro. I messaggi vengono pubblicati nel broker con bus, pub/sub o qualsiasi altro modello di messaggistica e quindi il broker indirizza i messaggi ai moduli connessi.

Un modulo usa la funzione **Broker\_Publish** per pubblicare un messaggio nel broker. Il broker recapita i messaggi a un modulo richiamando una funzione di callback. Un messaggio è costituito da un set di proprietà chiave/valore e contenuto passato come blocco di memoria.

![][3]

### Routing e filtro dei messaggi

Per indirizzare i messaggi ai moduli corretti è possibile procedere in due modi. È possibile passare un set di collegamenti al broker in modo che conosca l'origine e il sink di ogni modulo oppure il modulo può filtrare le proprietà del messaggio. Un modulo deve agire unicamente sui messaggi a esso destinati. I collegamenti e il filtro dei messaggi creano di fatto una pipeline dei messaggi.

## Architettura di esempio Hello World

L'esempio Hello World illustra i concetti descritti nella sezione precedente. L'esempio Hello World implementa un gateway la cui pipeline è costituita da due moduli:

-	Il modulo *hello world* crea un messaggio ogni cinque secondi e lo passa al modulo logger.
-	Il modulo *logger* scrive i messaggi che riceve in un file.

![][4]

Come descritto nella sezione precedente, il modulo Hello World non passa i messaggi direttamente al modulo logger ogni cinque secondi, ma pubblica un messaggio nel broker ogni cinque secondi.

Il modulo logger riceve il messaggio dal broker e interviene, scrivendo il contenuto del messaggio in un file.

Il modulo logger utilizza solo i messaggi provenienti dal broker, senza mai pubblicare nuovi messaggi nel broker.

![][5]

La figura precedente illustra l'architettura dell'esempio Hello World e i percorsi relativi ai file di origine che implementano parti diverse dell'esempio nel [repository][lnk-gateway-sdk]. Esplorare il codice per conto proprio o usare i frammenti di codice seguente come guida.

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-getstarted-selector/modules.png
[2]: media/iot-hub-gateway-sdk-getstarted-selector/modules_2.png
[3]: media/iot-hub-gateway-sdk-getstarted-selector/messages_1.png
[4]: media/iot-hub-gateway-sdk-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-gateway-sdk-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/hello_world
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

<!---HONumber=AcomDC_0928_2016-->