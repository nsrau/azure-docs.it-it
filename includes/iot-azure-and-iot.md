# Azure e Internet delle cose

Microsoft Azure e Internet delle cose (IoT) Questo articolo illustra un'architettura della soluzione IoT che descrive le caratteristiche comuni di una soluzione IoT che può essere distribuita tramite i servizi Azure. Le soluzioni IoT richiedono una comunicazione sicura, bidirezionale tra i numerosi dispositivi e una soluzione back-end che, ad esempio, usi l'analisi predittiva e automatizzata per rivelare informazioni dal flusso di eventi dal dispositivo al cloud.

Hub IoT di Azure è un componente essenziale quando si implementa questa architettura della soluzione IoT usando i servizi Azure, inoltre IoT Suite fornisce implementazioni complete ed end-to-end di questa architettura per scenari IoT specifici. Ad esempio la soluzione di *monitoraggio remoto* consente di monitorare lo stato di dispositivi come i distributori automatici e la *manutenzione predittiva* aiuta ad anticipare le esigenze di manutenzione di dispositivi come le pompe nelle stazioni di pompaggio remote e a evitare tempi di inattività non pianificati.

## Architettura della soluzione IoT

Il diagramma seguente mostra una tipica architettura di soluzione IoT. Si noti che non include i nomi di alcun servizio di Azure specifico, ma descrive gli elementi chiave di un'architettura di soluzione IoT generica. In questa architettura, i dispositivi IoT raccolgono i dati che inviano a un gateway nel cloud. Il gateway nel cloud rende disponibili i dati per l'elaborazione da altri servizi back-end da cui i dati vengono recapitati ad altre applicazioni line-of-business o agli operatori umani tramite un dashboard o un altro dispositivo di presentazione.

![Architettura della soluzione IoT][img-solution-architecture]

> [AZURE.NOTE] Per un'analisi approfondita dell'architettura IoT, vedere l'[architettura di riferimento di Microsoft Azure IoT][lnk-refarch].

### Connettività dei dispositivi

In questa architettura della soluzione IoT i dispositivi inviano i dati telemetrici, come le letture dei sensori di una stazione di pompaggio, a un endpoint cloud per l'archiviazione e l'elaborazione. In uno scenario di manutenzione predittiva, il back-end potrebbe usare il flusso di dati dei sensori per determinare quando una determinata pompa richiede manutenzione. I dispositivi possono anche ricevere e rispondere a comandi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Ad esempio, nello scenario della manutenzione predittiva il back-end della soluzione potrebbe inviare comandi ad altre pompe della stazione di pompaggio per avviare il reinstradamento dei flussi appena prima che inizi l'intervento di manutenzione, in modo che il tecnico della manutenzione possa iniziare subito il lavoro al suo arrivo.

Una delle maggiori difficoltà con i progetti IoT è quella di connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione per consentire al dispositivo di inviare i dati telemetrici e recuperare i comandi. I dispositivi IoT presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. Dispositivi IoT:

- Sono spesso sistemi incorporati senza operatore umano.
- Possono essere distribuiti in località remote, dove l'accesso fisico è molto costoso.
- Possono essere raggiungibili solo tramite il back-end della soluzione. Non esiste un altro modo per interagire con il dispositivo.
- Possono avere risorse di alimentazione e di elaborazione limitate.
- Possono disporre di una connettività di rete intermittente, lenta o costosa.
- Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
- Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L’implementazione del set di requisiti di connettività con tecnologie tradizionali come i contenitori Web e i broker di messaggistica è un'attività lunga e complessa. Hub IoT di Azure e gli SDK per dispositivi IoT rendono più semplice l'implementazione di soluzioni che soddisfano tali requisiti.

Un dispositivo può comunicare direttamente con un endpoint del gateway cloud oppure, se il dispositivo non può usare i protocolli di comunicazione supportati dal gateway cloud, è possibile connettersi tramite un gateway intermedio, come il [gateway del protocollo hub IoT][lnk-protocol-gateway] che esegue la conversione del protocollo. Ad esempio da Common Industrial Protocol (CIP) ad AMQPS.

### e analisi dei dati

È nel cloud che avviene la maggior parte delle elaborazioni dei dati di una soluzione IoT back-end, in particolare il filtro e l'aggregazione dei dati di telemetria e del relativo routing ad altri servizi. Il back-end della soluzione IoT:

- Riceve i dati di telemetria a livello di scalabilità dai dispositivi e determina come elaborare e archiviare quei dati. 
- Può consentire di inviare comandi dal cloud al dispositivo specifico.
- Fornisce funzionalità di registrazione del dispositivo che consentono di effettuare il provisioning dei dispositivi e controllare quali dispositivi possono connettersi all'infrastruttura.
- Consente di tenere traccia dello stato dei dispositivi e di monitorarne le attività.

Nello scenario della manutenzione predittiva il back-end della soluzione archivia i dati telemetrici cronologici, al fine di usarli per identificare modelli, inoltre analizza i dati telemetrici non appena pervengono, per individuare modelli indicanti la necessità di un intervento di manutenzione su una determinata pompa.

Le soluzioni IoT possono includere cicli di feedback automatici. Un modulo di analisi nel back-end può, ad esempio, stabilire dai dati di telemetria che la temperatura di un dispositivo specifico ha superato i normali livelli operativi e può quindi inviare un comando al dispositivo contenente l'istruzione di eseguire un'azione correttiva.

### Connettività aziendale e di presentazione

Il livello di connettività aziendale e di presentazione consente agli utenti finali di interagire con la soluzione IoT e i dispositivi. Consente anche agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o report di BI che possono mostrare sia i dati cronologici sia i dati quasi in tempo reale. Ad esempio, un operatore può controllare lo stato di determinate stazioni di pompaggio e vedere gli avvisi generati dal sistema. Questo livello consente inoltre l'integrazione della soluzione back-end IoT con le applicazioni line-of-business esistenti che possono legarsi ai processi o ai flussi di lavoro aziendali. La soluzione di manutenzione predittiva, ad esempio, può essere integrata con un sistema di pianificazione che prenota un tecnico per visitare una stazione di pompaggio quando la soluzione identifica una pompa che richiede manutenzione.

![Dashboard della soluzione IoT][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf

<!---HONumber=AcomDC_0601_2016-->