# Azure e Internet delle cose

Microsoft Azure e Internet delle cose (IoT) Questo articolo illustra un'architettura della soluzione IoT che descrive le caratteristiche comuni di una soluzione IoT che può essere distribuita tramite i servizi Azure. Le soluzioni IoT richiedono una comunicazione sicura, bidirezionale tra i numerosi dispositivi e una soluzione back-end che, ad esempio, usi l'analisi predittiva e automatizzata per rivelare informazioni dal flusso di eventi dal dispositivo al cloud.

Hub IoT di Azure è un componente essenziale quando si implementa questa architettura della soluzione IoT usando i servizi Azure e IoT Suite fornisce implementazioni complete ed end-to-end di questa architettura per scenari IoT specifici, quali *monitoraggio remoto* e *manutenzione predittiva*.

## Architettura della soluzione IoT

Il diagramma seguente mostra una tipica architettura di soluzione IoT. Si noti che non include i nomi di alcun servizio di Azure specifico, ma descrive gli elementi chiave di un'architettura di soluzione IoT generica. In questa architettura, i dispositivi IoT raccolgono i dati che inviano a un gateway nel cloud. Il gateway nel cloud rende disponibili i dati per l'elaborazione da altri servizi back-end da cui i dati vengono recapitati ad altre applicazioni line-of-business o agli operatori umani tramite un dashboard o un altro dispositivo di presentazione.

![Architettura della soluzione IoT][img-solution-architecture]

### Connettività dei dispositivi

In questa architettura della soluzione IoT, i dispositivi inviano la telemetria, ad esempio le letture della temperatura, a un endpoint cloud per l'archiviazione e l'elaborazione. I dispositivi possono anche ricevere e rispondere a comandi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Ad esempio, un dispositivo potrebbe recuperare un comando che gli indica di modificare la frequenza di campionamento dei dati.

Una delle maggiori difficoltà con i progetti IoT è quella di connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione. I dispositivi IoT presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. Dispositivi IoT:

- Sono spesso sistemi incorporati senza operatore umano.
- È possibile trovarli in località remote, dove l'accesso fisico è molto costoso.
- Possono essere raggiungibili solo tramite il back-end della soluzione. Non esiste un altro modo per interagire con il dispositivo.
- Possono avere risorse di alimentazione e di elaborazione limitate.
- Possono disporre di una connettività di rete intermittente, lenta o costosa.
- Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
- Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L’implementazione del set di requisiti di connettività con tecnologie tradizionali come i contenitori Web e i broker di messaggistica è un'attività lunga e complessa. Hub IoT di Azure e gli SDK per dispositivi IoT rendono più semplice l'implementazione di soluzioni che soddisfano tali requisiti.

Un dispositivo può comunicare direttamente con un endpoint del gateway cloud o, se il dispositivo non può usare i protocolli di comunicazione supportati dal gateway del cloud, è possibile connettersi tramite un gateway intermedio, ad esempio il [gateway del protocollo hub IoT][lnk-protocol-gateway] che esegue la conversione del protocollo. Elaborazione

### e analisi dei dati

È nel cloud che avviene la maggior parte delle elaborazioni dei dati di una soluzione IoT back-end, in particolare il filtro e l'aggregazione dei dati di telemetria e del relativo routing ad altri servizi. Il back-end della soluzione IoT:

- Riceve i dati di telemetria a livello di scalabilità dai dispositivi e determina come elaborare e archiviare quei dati. 
- Può consentire di inviare comandi dal cloud al dispositivo specifico.
- Fornisce funzionalità di registrazione del dispositivo che consentono di effettuare il provisioning dei dispositivi e controllare quali dispositivi possono connettersi all'infrastruttura.
- Consente di tenere traccia dello stato dei dispositivi e di monitorarne le attività.

Le soluzioni IoT possono includere cicli di feedback automatici. Un modulo di analisi nel back-end può, ad esempio, stabilire dai dati di telemetria che la temperatura di un dispositivo specifico ha superato i normali livelli operativi e può quindi inviare un comando al dispositivo consentendogli di eseguire un'azione di correzione.

### Connettività aziendale e di presentazione

Il livello di connettività aziendale e di presentazione consente agli utenti finali di interagire con la soluzione IoT e i dispositivi. Consente anche agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o rapporti di Business Intelligence. Ad esempio, un operatore può controllare lo stato di determinati mezzi di trasporto addetti alla spedizione e visualizzare eventuali avvisi generati dal sistema. Questo livello consente inoltre l'integrazione della soluzione back-end IoT con le applicazioni line-of-business esistenti che possono legarsi ai processi o ai flussi di lavoro aziendali.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]: iot-hub-protocol-gateway.md

<!---HONumber=AcomDC_0309_2016-->