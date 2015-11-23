# Azure e IoT

Benvenuti in Microsoft Azure e Internet delle cose (IoT) Questo articolo illustra un'architettura della soluzione IoT tipica che descrive le caratteristiche comuni di una soluzione IoT che può essere distribuita tramite i servizi di Azure. Una soluzione IoT tipica richiede una comunicazione sicura, bidirezionale tra i dispositivi, possibilmente presenti a milioni, e una soluzione back-end che, ad esempio, utilizzi l'analisi predittiva e automatizzati per rivelare informazioni dal flusso di eventi dal dispositivo al cloud.

## Architettura della soluzione IoT

Il diagramma seguente mostra una tipica architettura di soluzione IoT. Si noti che non include i nomi di alcun servizio di Azure specifico, ma descrive gli elementi chiave di un'architettura di soluzione IoT generica. Le sezioni seguenti forniscono altre informazioni sugli elementi di questa soluzione.

![Architettura della soluzione IoT][img-solution-architecture]

### Connettività dei dispositivi

In un tipico scenario IoT, i dispositivi inviano la telemetria, ad esempio le letture della temperatura, a un endpoint cloud per l'archiviazione e l'elaborazione. I dispositivi possono anche ricevere e rispondere a comandi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Ad esempio, un dispositivo potrebbe recuperare un comando che gli indica di modificare la frequenza di campionamento dei dati.

Una delle maggiori difficoltà con i progetti IoT è quella di connettere in modo affidabile e sicuro i dispositivi al back-end della soluzione. In genere, i dispositivi IoT presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. Dispositivi IoT:

- Sono spesso sistemi incorporati senza operatore umano.
- Li si può trovare in località remote, dove l'accesso fisico è molto costoso.
- Possono essere raggiungibili solo tramite il back-end della soluzione.
- Possono avere risorse di alimentazione e di elaborazione limitate.
- Possono disporre di una connettività di rete intermittente, lenta o costosa.
- Potrebbe essere necessario usare protocolli di applicazioni proprietari, personalizzati o specifici del settore.
- Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai requisiti precedenti, le soluzioni IoT devono offrire scalabilità, sicurezza e affidabilità. L’implementazione del set di requisiti di connettività con tecnologie tradizionali come i contenitori Web e i broker di messaggistica è un'attività lunga e complessa.

Un dispositivo può comunicare direttamente con un endpoint del gateway cloud, o se il dispositivo non può utilizzare uno dei protocolli di comunicazione supportato dal gateway del cloud, è possibile connettersi tramite un gateway intermedio che esegue la conversione del protocollo.

### Elaborazione e analisi dei dati

Nel cloud, un back-end della soluzione IoT:

- Riceve i dati di telemetria a livello di scalabilità dai dispositivi e determina come elaborare e archiviare quei dati. 
- Potrebbe inoltre consentire di inviare comandi dal cloud al dispositivo specifico.
- Fornisce le capacità di registrazione del dispositivo che consente di effettuare il provisioning dei dispositivi e di controllare quali dispositivi possono connettersi all'infrastruttura.
- Consente di tenere traccia dello stato dei dispositivi e di monitorarne le attività.

Le soluzioni IoT possono includere cicli di feedback automatici. Un modulo di Machine Learning nel back-end può, ad esempio, stabilire dai dati di telemetria che la temperatura di un dispositivo specifico ha superato i normali livelli operativi e può quindi inviare un comando al dispositivo consentendogli di eseguire un'azione di correzione

### Presentazione

Molte soluzioni IoT consentono agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o rapporti di Business Intelligence.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Nov15_HO3-->