
# <a name="azure-and-internet-of-things"></a>Azure e Internet delle cose

Microsoft Azure e Internet delle cose (IoT) Questo articolo illustra le caratteristiche comuni di una soluzione IoT che può essere distribuita tramite i servizi di Azure. Le soluzioni IoT richiedono una comunicazione bidirezionale sicura tra numeri elevati di dispositivi e un back-end della soluzione. Il back-end della soluzione può ad esempio usare l'analisi predittiva e automatizzata per individuare informazioni dettagliate dal flusso di eventi da dispositivo a cloud.

L'[hub IoT Azure][lnk-iot-hub] è un elemento fondamentale di qualsiasi soluzione IoT che usi i servizi di Azure. L'hub IoT è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end della soluzione. 

[Azure IoT Suite][lnk-iot-suite] mette a disposizione implementazioni complete ed end-to-end di questa architettura per scenari IoT specifici. ad esempio:

* La soluzione di *monitoraggio remoto* consente di monitorare lo stato dei dispositivi, ad esempio i distributori automatici.
* La soluzione di *manutenzione predittiva* consente di prevedere le esigenze di manutenzione di dispositivi come le pompe nelle stazioni di pompaggio remote e di evitare tempi di inattività non pianificati.
* La soluzione di *connected factory* consente di connettersi ai dispositivi industriali e di monitorarli.

## <a name="iot-solution-architecture"></a>Architettura della soluzione IoT

Il diagramma seguente mostra una tipica architettura di soluzione IoT. Il diagramma non include i nomi di alcun servizio di Azure specifico, ma descrive gli elementi chiave di un'architettura di soluzione IoT generica. In questa architettura, i dispositivi IoT raccolgono i dati che inviano a un gateway nel cloud. Il gateway del cloud rende i dati disponibili per l'elaborazione da parte di altri servizi back-end. Il back-end della soluzione fornisce dati alle applicazioni line-of-business o agli operatori umani tramite un dashboard o un report.

![Architettura della soluzione IoT][img-solution-architecture]

> [!NOTE]
> Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Architettura di riferimento di Microsoft Azure IoT).

### <a name="device-connectivity"></a>Connettività dei dispositivi

In questa soluzione IoT i dispositivi inviano i dati telemetrici, come le letture dei sensori di una stazione di pompaggio, a un endpoint cloud per l'archiviazione e l'elaborazione. In uno scenario di manutenzione predittiva, il back-end della soluzione potrebbe usare il flusso di dati dei sensori per determinare quando una determinata pompa richiede manutenzione. I dispositivi possono anche ricevere e rispondere a messaggi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Nello scenario della manutenzione predittiva il back-end della soluzione potrebbe ad esempio inviare messaggi ad altre pompe della stazione di pompaggio per avviare il reinstradamento dei flussi appena prima che inizi l'intervento di manutenzione, in modo che il tecnico della manutenzione possa iniziare immediatamente a risolvere il problema.

Una delle maggiori difficoltà con i progetti IoT è quella di connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione. I dispositivi IoT presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. Dispositivi IoT:

* Sono spesso sistemi incorporati senza operatore umano.
* Possono essere distribuiti in località remote, dove l'accesso fisico è costoso.
* Possono essere raggiungibili solo tramite il back-end della soluzione. Non esiste un altro modo per interagire con il dispositivo.
* Possono avere risorse di alimentazione e di elaborazione limitate.
* Possono disporre di una connettività di rete intermittente, lenta o costosa.
* Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
* Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L’implementazione del set di requisiti di connettività con tecnologie tradizionali come i contenitori Web e i broker di messaggistica è un'attività lunga e complessa. Hub IoT di Azure e Azure IoT SDK per dispositivi rendono più semplice l'implementazione di soluzioni che soddisfano tali requisiti.

Un dispositivo può comunicare direttamente con un endpoint del gateway nel cloud. Se il dispositivo non può usare nessuno dei protocolli di comunicazione supportati dal gateway nel cloud, può connettersi tramite un gateway intermedio. Ad esempio, il [gateway del protocollo di IoT di Azure][lnk-protocol-gateway] può convertire i protocolli se i dispositivi non sono in grado di usare nessuno dei protocolli supportati dall'hub IoT.

### <a name="data-processing-and-analytics"></a>e analisi dei dati

Nel cloud è soprattutto nel back-end della soluzione IoT che viene eseguita l'elaborazione dei dati. Il back-end della soluzione IoT:

* Riceve i dati di telemetria a livello di scalabilità dai dispositivi e determina come elaborare e archiviare quei dati. 
* Può consentire di inviare comandi dal cloud ai dispositivi specifici.
* Fornisce funzionalità di registrazione del dispositivo che consentono di effettuare il provisioning dei dispositivi e controllare quali dispositivi possono connettersi all'infrastruttura.
* Consente di tenere traccia dello stato dei dispositivi e di monitorarne le attività.

Nello scenario di manutenzione predittiva, il back-end della soluzione archivia i dati di telemetria cronologici. Il back-end della soluzione può usare questi dati per identificare modelli che indicano che è necessario eseguire la manutenzione su una pompa specifica.

Le soluzioni IoT possono includere cicli di feedback automatici. Un modulo di analisi nel back-end della soluzione può ad esempio stabilire dai dati di telemetria che la temperatura di un dispositivo specifico ha superato i normali livelli operativi. La soluzione può quindi inviare un comando al dispositivo per l'esecuzione di un'azione correttiva.

### <a name="presentation-and-business-connectivity"></a>Connettività aziendale e di presentazione

Il livello di connettività aziendale e di presentazione consente agli utenti finali di interagire con la soluzione IoT e i dispositivi. Consente anche agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o report, che possono mostrare sia i dati cronologici sia i dati quasi in tempo reale. Ad esempio, un operatore può controllare lo stato di determinate stazioni di pompaggio e vedere gli avvisi generati dal sistema. Questo livello consente inoltre l'integrazione della soluzione back-end IoT con le applicazioni line-of-business esistenti che possono legarsi ai processi o ai flussi di lavoro aziendali. La soluzione di manutenzione predittiva, ad esempio, può essere integrata con un sistema di pianificazione che prenota un tecnico per visitare una stazione di pompaggio quando la soluzione individua una pompa che richiede manutenzione.

![Dashboard della soluzione IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
