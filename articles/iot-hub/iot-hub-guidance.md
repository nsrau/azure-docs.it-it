<properties
 pageTitle="Azure IoT Hub: materiale sussidiario | Microsoft Azure"
 description="Raccolta di argomenti con indicazioni aggiuntive per le soluzioni che usano l'hub IoT di Azure."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Hub IoT di Azure: materiale sussidiario

## Confronto: Hub IoT e Hub eventi
Uno degli usi principali dell'hub IoT di Azure è la raccolta di dati di telemetria dai dispositivi. Per questo motivo l'hub IoT viene spesso confrontato con [Hub eventi], un servizio per l'elaborazione di eventi che fornisce l'ingresso di eventi e dati di telemetria nel cloud su vasta scala, con bassa latenza e affidabilità elevata.

I servizi presentano tuttavia molte differenze, illustrate in dettaglio nelle sezioni seguenti.

| Area | Hub IoT | Hub eventi |
| ---- | ------- | ---------- |
| Modelli di comunicazione | Ingresso di eventi da dispositivo a cloud e messaggistica da cloud a dispositivo. | Solo ingresso di eventi, considerato di solito per scenari da dispositivo a cloud. |
| Sicurezza | Identità per dispositivo e controllo di accesso revocabile. Vedere la [Guida per gli sviluppatori dell'hub IoT - Sicurezza]. | [Criteri di accesso condivisi][Event Hub - security] a livello di Hub eventi, con supporto limitato per la revoca tramite [criteri dell'entità di pubblicazione][Event Hub publisher policies]. Nel contesto delle soluzioni IoT, è spesso necessario implementare una soluzione personalizzata per supportare le credenziali per dispositivo e le misure antispoofing. |
| Scale | L'hub IoT è ottimizzato per supportare milioni di dispositivi connessi simultaneamente. | Hub eventi può supportare un numero più limitato di connessioni simultanee: al massimo 5.000 connessioni AMQP, in base al valore di [Quote del bus di servizio]. D'altra parte, Hub eventi consente agli utenti di specificare la partizione per ogni messaggio inviato. |
| SDK del dispositivo | L'hub IoT fornisce [SDK del dispositivo][Azure IoT Hub SDKs] per un'ampia gamma di piattaforme e linguaggi | Hub eventi è supportato in .NET, C e fornisce interfacce di invio AMQP e HTTP. |

In conclusione, anche se il solo caso di utilizzo è l'ingresso dei dati di telemetria da dispositivo a cloud, l'hub IoT fornisce un servizio progettato espressamente per la connettività dei dispositivi IoT e continuerà a espandere le proposte di valore per questi scenari con funzionalità specifiche per IoT. Hub eventi è progettato per l'ingresso di eventi su vasta scala, sia nel contesto di scenari all'interno e tra data center. Non è insolito usare l'hub IoT e Hub eventi insieme, lasciando che il primo gestisca la comunicazione da dispositivo a cloud e il secondo la fase successiva di ingresso degli eventi nei motori di elaborazione in tempo reale.

## Provisioning dei dispositivi <a id="provisioning"></a>
Le soluzioni IoT mantengono le informazioni sui dispositivi in diversi sistemi e archivi. Il [registro identità dell'hub IoT][IoT Hub Developer Guide - identity registry] è uno di questi, tra gli altri archivi che mantengono le informazioni sul dispositivo specifiche dell'applicazione. Si definisce *provisioning* il processo di creazione delle informazioni sul dispositivo richieste in tutti questi sistemi.

I requisiti e le strategie per il provisioning dei dispositivi sono molte. Per altre informazioni, vedere la [Guida alla gestione dei dispositivi dell'hub IoT]. Il [registro delle identità dell'hub IoT][IoT Hub Developer Guide - identity registry] fornisce le API necessarie per l'integrazione dell'hub IoT nel processo di provisioning.

## Gateway sul campo <a id="fieldgateways"></a>
Un gateway sul campo è un dispositivo specializzato o un software di utilizzo generico che agisce come componente che abilita la comunicazione e potenzialmente come sistema di controllo locale dei dispositivi e hub di elaborazione dei dati dei dispositivi. Un gateway sul campo può eseguire funzioni di controllo ed elaborazione locali verso i dispositivi. Sull'altro lato può filtrare o aggregare la telemetria del dispositivo e quindi ridurre la quantità di dati trasferiti al back-end.

L'ambito di un gateway sul campo include il gateway stesso e tutti i dispositivi collegati. Come implica il nome, i gateway sul campo operano all'esterno della struttura di elaborazione dei dati e sono solitamente associati alla località. Un gateway sul campo è diverso da un semplice router del traffico, in quanto ha un ruolo attivo nella gestione dell'accesso e del flusso di informazioni. Per questo motivo è un'entità indirizzata all'applicazione e un terminale di sessione o connessione di rete, ad esempio i gateway in questo contesto possono essere utili per il provisioning dei dispositivi, la trasformazione dei dati, la conversione dei protocolli e l'elaborazione delle regole eventi. I dispositivi o i firewall NAT, al contrario, non sono idonei come gateway sul campo perché non sono terminali di sessione o di connessione espliciti, ma piuttosto inoltrano (o negano) le connessioni o le sessioni che li attraversano.

### Gateway sul campo opachi o trasparenti
Rispetto alle identità dei dispositivi, i gateway sul campo sono detti *trasparenti* se altri dispositivi nell'ambito hanno voci di identità del dispositivo nel registro delle identità dell'hub IoT. Sono detti *opachi* nel caso in cui la sola identità nel registro delle identità dell'hub IoT sia l'identità del gateway sul campo.

È importante notare che l'uso di tageway *opachi* impedisce all'hub IoT di fornire [antispoofing delle identità del dispositivo][IoT Hub Developer Guide - Anti-spoofing]. Inoltre, poiché tutti i dispositivi nell'ambito del gateway sul campo sono rappresentati come un singolo dispositivo nell'hub IoT, sono soggetti a limitazioni e quote come dispositivo singolo.

### Altre considerazioni

Quando si implementa un gateway sul campo, è possibile usare Azure IoT device SDK. Alcuni SDK offrono funzionalità specifiche del gateway sul campo, ad esempio funzionalità per il multiplexing delle comunicazioni di più dispositivi sulla stessa connessione all'hub IoT. Come descritto nella [Guida per gli sviluppatori dell'hub Io - Scelta del protocollo di comunicazione], è consigliabile evitare di usare HTTP/1 come protocollo di trasporto per i gateway sul campo.

## Uso di schemi/servizi di autenticazione dei dispositivi personalizzati <a id="customauth"></a>
L'hub IoT di Azure consente di configurare il controllo di accesso e le credenziali di sicurezza per dispositivo usando il [registro delle identità del dispositivo][IoT Hub Developer Guide - identity registry]. Anche una soluzione IoT, che già ha un investimento significativo in termini di schema di autenticazione e/o registro delle identità dei dispositivi personalizzati, può comunque sfruttare i vantaggi offerti dalle altre funzionalità dell'hub IoT creando un *servizio token* per l'hub IoT.

Il servizio token è un servizio cloud distribuito automaticamente che usa criteri di accesso condiviso dell'hub IoT con l'autorizzazione **DeviceConnect** per creare token *con ambito dispositivo*.

  ![][img-tokenservice]

Ecco i passaggi principali del modello di servizio token:

1. Creare [criteri di accesso condiviso dell'hub IoT][IoT Hub Developer Guide - Security] con autorizzazioni **DeviceConnect** per l'hub IoT. I criteri saranno usati dal servizio token per firmare i token.
2. Quando un dispositivo deve accedere all'hub IoT, richiede un token firmato al servizio token. Il dispositivo può usare lo schema di autenticazione o il registro delle identità dei dispositivi personalizzato.
3. Il servizio token restituisce un token, creato in base alla [Guida per gli sviluppatori dell'hub IoT - Sicurezza], usando `/devices/{deviceId}` come `resourceURI`, con `deviceId` come dispositivo da autenticare.
4. Il dispositivo usa il token direttamente con l'hub IoT.

Il servizio token può impostare la scadenza del token, secondo le esigenze. Alla scadenza, l'hub IoT interrompe la connessione e il dispositivo dovrà richiedere un nuovo token al servizio token. Chiaramente, un intervallo di scadenza breve aumenterà il carico sia nel dispositivo che nel servizio token.

È utile specificare che l'identità del dispositivo deve essere creata di nuovo nell'hub IoT per consentire al dispositivo di connettersi. Ciò significa che il controllo di accesso per dispositivo, disabilitando le identità del dispositivo in base alla [Guida per gli sviluppatori dell'hub IoT - Registro delle identità], è ancora operativo, anche se il dispositivo si autentica con un token. Questo approccio contribuisce ad attenuare gli effetti dell'esistenza di token di lunga durata.

### Confronto con un gateway personalizzato
Il modello di servizio token è il metodo consigliato per implementare uno schema di autenticazione/registro di identità personalizzato con l'hub IoT, perché consente all'hub IoT di gestire la maggior parte del traffico delle soluzioni. In alcuni casi lo schema di autenticazione personalizzato è talmente intrecciato con il protocollo, ad esempio [TLS-PSK], da richiedere un servizio che elabora tutto il traffico (*gateway personalizzato*). Per altre informazioni, vedere l'articolo [Gateway del protocollo].

## Ridimensionamento dell'hub IoT
L'hub IoT può supportare fino a un milione di dispositivi connessi simultaneamente incrementando il numero di unità S1 o S2 dell'hub IoT fino a 2.000. Per altre informazioni, vedere [Prezzi dell'hub IoT][lnk-pricing].

Ogni unità dell'hub IoT consente un certo numero di identità del dispositivo nel relativo registro, che possono essere tutte connesse simultaneamente, e un certo numero di messaggi giornalieri.

Per il ridimensionamento corretto della soluzione, è necessario considerare l'uso specifico che viene fatto dell'hub IoT. In particolare, considerare la velocità effettiva di picco richiesta per le categorie di operazioni seguenti:

* Messaggi da dispositivo a cloud
* Messaggi da cloud a dispositivo
* Operazioni del registro delle identità

Oltre alle informazioni sulla velocità effettiva, vedere le [quote e limitazioni dell'hub IoT] e progettare la propria soluzione di conseguenza.

### Velocità effettiva dei messaggi da dispositivo a cloud e da cloud a dispositivo.
Il modo migliore per definire le dimensioni di una soluzione hub IoT consiste nel valutare il traffico per dispositivo.

I messaggi da dispositivo a cloud seguono queste linee guida in caso di velocità effettiva sostenuta

| Livello | Velocità effettiva sostenuta | Frequenza di invio sostenuta |
| ---- | -------------------- | ------------------- |
| S1 | Fino a 8 KB/ora per dispositivo | Media di 4 messaggi/ora per dispositivo |
| S2 | Fino a 4 KB/ora per dispositivo | Media di 2 messaggi/minuto per dispositivo |

Quando si ricevono messaggi da dispositivo a cloud, il back-end dell'applicazione può prevedere la velocità effettiva massima seguente (su tutti i lettori).

| Livello | Velocità effettiva sostenuta |
| ---- | -------------------- |
| S1 | Fino a 120 KB/minuto per untià, con un di minimo 2 MB/secondo |
| S2 | Fino a 4 MB/secondo per unità, con un massimo di 2 MB/secondo |

Le prestazioni dei messaggi da cloud a dispositivo vengono ridimensionate per dispositivo, con ogni dispositivo che riceve al massimo 5 messaggi al minuto.

### Velocità effettiva delle operazioni del registro delle identità
Le operazioni del registro delle identità dell'hub IoT non sono considerate operazioni di runtime, in quanto sono per lo più correlate al provisioning dei dispositivi. Per i dati specifici sulle prestazioni in modalità burst, vedere [Quote e limitazioni dell'hub IoT].

### Partizionamento orizzontale
Mentre un hub IoT può essere ridimensionato fino a milioni di dispositivi, a volte la soluzione richiede caratteristiche di prestazioni specifiche che un singolo hub IoT non può garantire. In questo caso, è consigliabile partizionare i dispositivi in più hub IoT, per supportare le condizioni di burst del traffico e ottenere la velocità effettiva richiesta o le velocità operative richieste.

## Disponibilità elevata e ripristino di emergenza
Come servizio di Azure, l'hub IoT offre velocità elevata usando le ridondanze a livello di area di Azure, senza richiedere attività aggiuntive alla soluzione. Azure offre anche numerose funzionalità che facilitano la compilazione di soluzioni con funzionalità di ripristino di emergenza o disponibilità tra aree, se necessario. Le soluzioni devono essere progettate e preparate per sfruttare i vantaggi di tali funzionalità per fornire la disponibilità elevata globale e tra aree a dispositivi o utenti. L'articolo [Informazioni tecniche sulla continuità aziendale di Azure] descrive le funzionalità integrate di Azure per la continuità aziendale e il ripristino di emergenza. Il documento [Informazioni tecniche sulla continuità aziendale di Azure] fornisce informazioni sull'architettura nelle strategie per consentire alle applicazioni di Azure di ottenere disponibilità elevata e ripristino di emergenza (HADR).

## Failover di area con l'hub IoT
Un approfondimento completo sulle tecnologie di distribuzione nelle soluzioni IoT non rientra nell'ambito di questa sezione, ma ai fini della disponibilità elevata e del ripristino di emergenza viene considerato il modello di distribuzione per il *failover di area*.

In un modello di failover di area, il back-end della soluzione viene eseguito principalmente in una posizione del data center, ma verrano distribuiti un hub IoT e un back-end aggiuntivi in un'area del data center a fini del failover, qualora l'hub IoT nel data center primario dovesse subire un'interruzione dell'alimentazione o nel caso la connettività di rete dal dispositivo al data center primario dovesse interrompersi. I dispositivi usano un endpoint di servizio secondario quando il gateway promario non è raggiungibile. Con una funzionalità di failover tra aree, la disponibilità della soluzione può essere migliorata al di là della disponibilità elevata di una singola area.

Ad alto livello, per implementare un modello di failover di area con l'hub IoT, sono necessari gli elementi seguenti.

* **Hub IoT secondario e logica di routing del dispositivo**: in caso di interruzione del servizio nell'area primaria, i dispositivi devono avviare la connessione all'area secondaria. Data la condizione con stato della maggior parte dei servizi coinvolti, gli amministratori delle soluzioni attivano comunemente il processo di failover tra aree. Il modo migliore per comunicare il nuovo endpoint ai dispositivi, mantenendo il controllo del processo, consiste nel fare in modo che controllino regolarmente un servizio *concierge* per verificare la disponibilità dell'endpoint attualmente attivo. Il servizio concierge può essere una semplice applicazione Web replicata e mantenuta raggiungibile con tecniche di reindirizzamento DNS, ad esempio con [Gestione traffico di Azure]).
* **Replica del registro delle identità**: per essere utilizzabile, l'hub IoT secondario deve contenere tutte le identità dei dispositivi che devono essere in grado di connettersi alla soluzione. La soluzione deve mantenere backup con replica geografica delle identità dei dispositivi e caricarli nell'hub IoT secondario prima del passaggio all'endpoint attivo per i dispositivi. La funzionalità di esportazione delle identità dei dispositivi dell'hub IoT è molto utile in questo contesto [Guida per gli sviluppatori dell'hub IoT - Registro delle identità].
* **Logica di unione**: quando un'area primaria diventa di nuovo disponibile, deve essere eseguita la migrazione di tutti i dati e dello stato creati nel sito secondario all'area primaria. Queste informazioni sono per lo più correlate alle identità dei dispositivi e ai metadati dell'applicazione, che dovranno essere uniti all'hub IoT primario e probabilmente ad altri archivi specifici dell'applicazione nell'area primaria. Per semplificare questo passaggio, è di solito consigliabile usare operazioni idempotenti. In questo modo si minimizzano gli effetti collaterali non solo dell'eventuale distribuzione coerente degli eventi, ma anche dei duplicati o del recapito non ordinato degli eventi. La logica dell'applicazione dovrebbe anche essere progettata per tollerare le potenziali incoerenze o lo stato "leggermente" non ordinato, considerato il tempo aggiuntivo che il sistema impiega per ripristinare l'integrità o in base a obiettivi del punto di ripristino (RPO). L'articolo seguente fornisce informazioni più dettagliate su questo argomento: [Operatore alternativo: informazioni aggiuntive per architetture cloud resilienti].




[img-tokenservice]: media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guida per gli sviluppatori dell'hub IoT - Registro delle identità]: iot-hub-devguide.md#identityregistry
[Guida per gli sviluppatori dell'hub Io - Scelta del protocollo di comunicazione]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guida per gli sviluppatori dell'hub IoT - Sicurezza]: iot-hub-devguide.md#security
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Gateway del protocollo]: iot-hub-protocol-gateway.md
[quote e limitazioni dell'hub IoT]: iot-hub-devguide.md#throttling

[Guida alla gestione dei dispositivi dell'hub IoT]: iot-hub-device-management.md
[Hub eventi]: ../event-hubs/event-hubs-what-is-event-hubs/
[Gestione traffico di Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview/
[Event Hub publisher policies]: ../event-hubs-overview/#common-publisher-tasks
[Quote del bus di servizio]: ../service-bus/service-bus-quotas/
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md

[TLS-PSK]: https://tools.ietf.org/html/rfc4279

[Informazioni tecniche sulla continuità aziendale di Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Informazioni tecniche sulla continuità aziendale di Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[Operatore alternativo: informazioni aggiuntive per architetture cloud resilienti]: https://msdn.microsoft.com/library/azure/jj853352.aspx

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub

<!---HONumber=Oct15_HO1-->