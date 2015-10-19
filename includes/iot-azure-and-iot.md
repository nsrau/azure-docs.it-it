# Microsoft Azure e Internet delle cose (IoT)

Una tipica soluzione IoT richiede una comunicazione bidirezionale sicura tra dispositivi, possibilmente nell'ordine dei milioni, e un back-end applicazioni. Esempi di funzionalità del back-end applicazioni possono includere l'elaborazione di eventi da dispositivo a cloud per individuare informazioni utili con l'analisi predittiva e automatizzata.

Microsoft fornisce un set di librerie (che supportano più linguaggi e piattaforme hardware) che è possibile usare per sviluppare applicazioni client da eseguire su un dispositivo IoT. Per implementare l'applicazione back-end IoT, è possibile combinare più servizi di Azure o usare una delle soluzioni preconfigurate disponibili in [IoT Suite di Azure][]. Per capire meglio come Azure abiliti questa infrastruttura IoT, è utile considerare una tipica architettura di soluzione IoT.

## Architettura della soluzione IoT

Il diagramma seguente mostra una tipica architettura di soluzione IoT. Si noti che non include i nomi di alcun servizio di Azure specifico, ma descrive gli elementi chiave di un'architettura di soluzione IoT generica. Le sezioni seguenti forniscono altre informazioni sugli elementi di questa soluzione.

![Architettura della soluzione IoT][img-solution-architecture]

### Connettività dei dispositivi

In un tipico scenario IoT, i dispositivi inviano la telemetria da dispositivo a cloud, ad esempio le letture della temperatura, a un endpoint cloud per l'archiviazione e l'elaborazione. I dispositivi possono anche ricevere e rispondere a comandi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Ad esempio, un dispositivo potrebbe recuperare un comando che gli indica di modificare la frequenza di campionamento dei dati.

Un dispositivo o un'origine dati in una soluzione IoT può essere un semplice sensore connesso alla rete o addirittura un potente dispositivo di elaborazione autonomo. Un dispositivo può avere funzionalità di elaborazione, memoria, larghezza di banda delle comunicazioni e supporto del protocollo di comunicazione limitati.

Un dispositivo può comunicare direttamente con un endpoint di un gateway cloud con un protocollo di comunicazione, come AMQP o HTTP, oppure con un intermediario, come un gateway campo, che fornisce un servizio come la conversione del protocollo.

### Elaborazione e analisi dei dati

Nel cloud un processore di eventi di flusso riceve i messaggi da dispositivo a cloud su larga scala dai dispositivi e determina come elaborare e archiviare tali messaggi. Una soluzione per i dispositivi connessi consente di inviare a dispositivi specifici i dati da cloud a dispositivo sotto forma di comandi. La registrazione dei dispositivi con la soluzione IoT consente di effettuare il provisioning dei dispositivi e di controllare quali dispositivi possono connettersi all'infrastruttura. Il back-end consente di tenere traccia dello stato dei dispositivi e di monitorarne le attività.

Le soluzioni IoT possono includere cicli di feedback automatici. Un modulo di Machine Learning, ad esempio, può stabilire dai dati di telemetria da dispositivo a cloud che la temperatura di un dispositivo specifico ha superato i normali livelli operativi e può quindi inviare un comando al dispositivo consentendogli di eseguire un'azione di correzione

### Presentazione

Molte soluzioni IoT consentono agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o rapporti di Business Intelligence.

[img-solution-architecture]: media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[IoT Suite di Azure]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Oct15_HO2-->