
# <a name="azure-and-the-internet-of-things"></a>Azure e Internet delle cose

Microsoft Azure e Internet delle cose (IoT) Questo articolo descrive le caratteristiche comuni di una soluzione IoT nel cloud. Le soluzioni IoT richiedono una comunicazione bidirezionale sicura tra dispositivi, possibilmente nell'ordine dei milioni, e un back-end della soluzione. Una soluzione può ad esempio usare l'analisi predittiva e automatizzata per trovare informazioni dettagliate dal flusso di eventi da dispositivo a cloud.

## <a name="iot-solution-architecture"></a>Architettura della soluzione IoT

Il diagramma seguente mostra gli elementi chiave di una tipica architettura della soluzione IoT. Il diagramma è indipendente dai dettagli dell'implementazione specifica, ad esempio i servizi di Azure usati e i sistemi operativi dei dispositivi. In questa architettura, i dispositivi IoT raccolgono i dati che inviano a un gateway nel cloud. Il gateway del cloud rende i dati disponibili per l'elaborazione da parte di altri servizi back-end. Questi servizi back-end possono inviare i dati a:

* Altre applicazioni line-of-business.
* Operatori umani tramite un dashboard o un altro dispositivo di presentazione.

![Architettura della soluzione IoT][img-solution-architecture]

> [!NOTE]
> Per un'analisi approfondita dell'architettura IoT, vedere [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Architettura di riferimento di Microsoft Azure IoT).

### <a name="device-connectivity"></a>Connettività dei dispositivi

In un'architettura della soluzione IoT i dispositivi in genere inviano i dati di telemetria al cloud per l'archiviazione e l'elaborazione. In uno scenario di manutenzione predittiva, ad esempio, il back-end della soluzione potrebbe usare il flusso di dati dei sensori per determinare quando una determinata pompa richiede manutenzione. I dispositivi possono anche ricevere e rispondere a messaggi da cloud a dispositivo leggendo i messaggi da un endpoint cloud. Nello stesso esempio il back-end della soluzione potrebbe inviare messaggi ad altre pompe della stazione di pompaggio per avviare il reinstradamento dei flussi appena prima che inizi l'intervento di manutenzione, in modo che il tecnico della manutenzione possa iniziare subito il lavoro al suo arrivo.

La connessione sicura e affidabile dei dispositivi è spesso la sfida principale nelle soluzioni IoT. I dispositivi IoT infatti presentano caratteristiche diverse da quelle di altri client, ad esempio browser e app per dispositivi mobili. I dispositivi IoT in particolare:

* Sono spesso sistemi incorporati senza operatore umano (diversamente da un telefono).
* Possono essere distribuiti in località remote, dove l'accesso fisico è costoso.
* Possono essere raggiungibili solo tramite il back-end della soluzione. Non esiste un altro modo per interagire con il dispositivo.
* Possono avere risorse di alimentazione e di elaborazione limitate.
* Possono disporre di una connettività di rete intermittente, lenta o costosa.
* Possono richiedere protocolli di applicazioni proprietari, personalizzati o specifici del settore.
* Possono essere create utilizzando un'ampia gamma di piattaforme hardware e software molto comuni.

Oltre ai vincoli precedenti, le soluzioni IoT devono essere anche scalabili, sicure e affidabili.

A seconda del protocollo di comunicazione e della disponibilità di rete, un dispositivo può comunicare con il cloud direttamente o tramite un gateway intermedio. Le architetture IoT presentano spesso una combinazione di questi due modelli di comunicazione.

### <a name="data-processing-and-analytics"></a>e analisi dei dati

Nelle moderne soluzioni IoT l'elaborazione dei dati può essere eseguita nel cloud o sul lato dispositivo. L'elaborazione lato dispositivo viene detta *elaborazione perimetrale*. La scelta di dove elaborare i dati dipende da alcuni fattori, ad esempio:

* Vincoli di rete. Se la larghezza di banda tra i dispositivi e il cloud è limitata, si è incentivati a preferire l'elaborazione perimetrale.
* Tempo di risposta. Se è necessario intervenire su un dispositivo in tempo quasi reale, può essere meglio elaborare la risposta nel dispositivo stesso, ad esempio un braccio robotico che deve essere arrestato in caso di emergenza.
* Ambiente normativo. Alcuni dati non possono essere inviati al cloud.

In generale l'elaborazione dei dati sia nel dispositivo perimetrale che nel cloud è una combinazione delle funzionalità seguenti:

* Ricezione dei dati di telemetria su larga scala dai dispositivi e scelta di come elaborare e archiviare tali dati.
* Analisi dei dati di telemetria per fornire informazioni dettagliate, sia in tempo reale che dopo un evento.
* Invio di comandi dal cloud o da un dispositivo gateway a un dispositivo specifico.

Un back-end cloud IoT deve anche offrire:

* Funzionalità di registrazione del dispositivo che consentono di:
    * Effettuare il provisioning dei dispositivi.
    * Controllare a quali dispositivi è consentito connettersi all'infrastruttura.
* Gestione dei dispositivi per controllarne lo stato e monitorarne le attività.

In uno scenario di manutenzione predittiva, ad esempio, il back-end cloud archivia i dati di telemetria cronologici. La soluzione usa questi dati per identificare un potenziale comportamento anomalo in pompe specifiche prima che causino un vero problema. Usando l'analisi dei dati, può stabilire che la soluzione preventiva è l'invio di un comando al dispositivo per eseguire un'azione correttiva. Questo processo genera un ciclo di feedback automatizzato tra il dispositivo e il cloud che accresce notevolmente l'efficienza della soluzione.

### <a name="presentation-and-business-connectivity"></a>Connettività aziendale e di presentazione

Il livello di connettività aziendale e di presentazione consente agli utenti finali di interagire con la soluzione IoT e i dispositivi. Consente anche agli utenti di visualizzare e analizzare i dati raccolti dai dispositivi. Queste visualizzazioni possono assumere la forma di dashboard o report di BI che possono mostrare sia i dati cronologici sia i dati quasi in tempo reale. Ad esempio, un operatore può controllare lo stato di determinate stazioni di pompaggio e vedere gli avvisi generati dal sistema. Questo livello consente anche l'integrazione della soluzione back-end IoT con le applicazioni line-of-business esistenti che possono legarsi ai processi o ai flussi di lavoro aziendali. Una soluzione di manutenzione predittiva, ad esempio, può essere integrata con un sistema di pianificazione per prenotare un tecnico per visitare una stazione di pompaggio quando identifica una pompa che richiede manutenzione.

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
