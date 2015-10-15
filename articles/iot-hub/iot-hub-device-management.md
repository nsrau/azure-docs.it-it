<properties
 pageTitle="Gestione dei dispositivi IoT | Microsoft Azure"
 description="Panoramica della gestione dei dispositivi dell'hub IoT di Azure"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="juanpere"/>

# Gestione dei dispositivi IoT tramite Azure IoT Suite e l'hub IoT di Azure

## Introduzione
Azure IoT Suite e l'hub IoT di Azure forniscono le capacità fondamentali per consentire la gestione dei dispositivi per soluzioni IoT, su vasta scala, e per un set di dispositivi e topologie di dispositivi di vario tipo. I riferimenti alla gestione dei dispositivi in questo articolo sono correlati in modo specifico alla gestione dei dispositivi IoT.

I provider di servizi e le aziende o qualsiasi organizzazione che gestisce dispositivi IoT usano le capacità di gestione dei dispositivi per eseguire i processi aziendali seguenti: registrazione e rilevamento di dispositivi IoT, abilitazione della connettività, configurazione remota e aggiornamento del software nei dispositivi. Ad esempio, quando si gestiscono dispositivi in fabbriche o in campi petroliferi saranno disponibili criteri per la configurazione e l'aggiornamento remoto dei dispositivi, con catene di approvazione, requisiti per il controllo delle normative, presenza di protezioni fisiche e altro ancora.

Quando si abilita la gestione dei dispositivi IoT per la soluzione IoT, è necessario prendere in considerazione le capacità seguenti e determinare l'importanza di ogni capacità sulla base degli obiettivi di business:

* Provisioning e rilevamento dei dispositivi: processo tramite il quale un dispositivo viene registrato nel sistema.
* Registro dei dispositivi e modelli dei dispositivi: metodo per specificare il modo in cui i modelli dei dispositivi rappresentano l'uso strutturato dei metadati per le relazioni tra i dispositivi, il ruolo nel sistema e i metodi d convalida.
* Gestione dell'accesso ai dispositivi: metodo per specificare il modo in cui i dispositivi controllano l'accesso alle risorse dei dispositivi dai servizi cloud.
* Controllo remoto: metodo per specificare il modo in cui gli utenti remoti ottengono l'accesso ai dispositivi e richiedono modifiche ai dispositivi.
* Amministrazione remota: processo mediante il quale un amministratore definisce lo stato dei dispositivi disponibili.  
* Configurazione remota: metodo per specificare il modo in cui gli amministratori modificano la configurazione dei dispositivi.
* Aggiornamento remoto di firmware e software: processo mediante il quale gli amministratori possono aggiornare il firmware e il software dei dispositivi.

Le sezioni seguenti forniscono un approfondimento di ogni capacità di gestione dei dispositivi e offrono un modello generale per l'implementazione di queste capacità mediante l'hub IoT di Azure.

## Provisioning e rilevamento dei dispositivi
Il provisioning di un dispositivo con l'hub IoT di Azure viene eseguito tramite l'API del Registro di sistema. Dopo avere registrato il dispositivo e fornito o ricevuto una chiave, sarà possibile abilitare il dispositivo alla connessione all'hub IoT tramite quella chiave. L'hub IoT di Azure comunicherà solo con i dispositivi registrati che presentano credenziali autorizzate. Gli amministratori possono disabilitare l'accesso dei dispositivi all'hub IoT di Azure tramite il portale di amministrazione del dispositivo.

È possibile usare un processo bootstrap, in base al modo in cui i dispositivi IoT vengono prodotti, sottoposti a provisioning e distribuiti. È possibile creare un servizio bootstrap come parte della soluzione per fornire la semplice connettività e posticipare il processo di assegnazione del dispositivo a un hub IoT di Azure specifico. La posizione in cui verrà usato il dispositivo potrebbe essere sconosciuta nel momento in cui il dispositivo viene prodotto. Questo è solo un esempio di un numero elevato di flussi di lavoro potenzialmente complessi che semplificano la registrazione di un dispositivo nell'hub IoT di Azure e l'integrazione con i processi aziendali esistenti.

Quando si usa un servizio bootstrap, viene avviato un dispositivo IoT e ciò può consentire l'accesso a un hub IoT di Azure assegnato. La richiesta deve includere le credenziali bootstrap del dispositivo ed eventuali altri dati necessari. Per i dispositivi autorizzati, il servizio bootstrap deve registrare il dispositivo in un hub IoT di Azure assegnato e deve fornire i dettagli per la connettività al dispositivo che richiede un bootstrap. L'hub IoT deve fornire i dettagli per la connettività al dispositivo che richiede un bootstrap. Per i dispositivi autorizzati, il servizio bootstrap deve registrare il dispositivo in un hub IoT di Azure assegnato e deve fornire i dettagli per la connettività al dispositivo che richiede un bootstrap. L'hub IoT deve fornire i dettagli per la connettività al dispositivo che richiede un bootstrap.

## Registro dei dispositivi e modelli dei dispositivi

Per modello di dispositivo si intende il modello che include le proprietà del dispositivo che possono essere lette/scritte da un servizio cloud e i comandi del dispositivo che possono essere eseguiti in modalità remota da un servizio cloud. In un modello basato sul servizio, descritto in seguito, il dispositivo non conosce il modello, ma i servizi cloud possono comunque registrare e usare i metadati sui dispositivi.

L'archiviazione delle informazioni sui dispositivi e dei metadati associati è essenziale per il sistema IoT e per il ruolo del registro dei dispositivi. In particolare per i dispositivi legacy, che non possono essere modificati, o per i dispositivi che non usano un modello di dispositivo, un modello basato sul servizio può consentire a un servizio IoT di interagire con un insieme di dispositivi. Quando i dispositivi sono in funzione con un modello definito, il registro dei dispositivi può essere usato come una visualizzazione coerente dei dati dei dispositivi, in cui il dispositivo fornisce il master. In questo caso il servizio informa il dispositivo in merito alle modifiche desiderate e le modifiche saranno valide solo dopo la conferma da parte del dispositivo.

### Modello basato sul servizio
Nel caso in cui un dispositivo si connetta all'hub IoT di Azure e non fornisca alcun modello di dispositivo, è importante implementare un registro dei dispositivi che tenga traccia dei dispositivi registrati e dei metadati associati. In questo caso il registro dei dispositivi è l'unica posizione di archiviazione dei metadati dei dispositivi. Gli esempi di metadati di cui tenere traccia includono la topologia logica (ad esempio piano 4, edificio 109), la funzione del dispositivo (ad esempio sensore della porta) o qualsiasi altra informazioni di tag.

### Modello basato sul dispositivo
Per consentire alla soluzione IoT di sfruttare le capacità del dispositivo IoT, è necessario che i dispositivi descrivano se stessi al cloud dopo la connessione all'hub IoT di Azure. I due tipi di modelli seguenti possono essere usati in una soluzione IoT:

#### Modello di dispositivo autodefinito
Un progettista di dispositivi o uno sviluppatore che usa un simulatore di dispositivo usa il modello di dispositivo autodefinito attraverso il processo di iterazione delle capacità del dispositivo durante la creazione del dispositivo. Un progettista di dispositivi può iniziare creando un dispositivo con poche proprietà e pochi comandi e aggiungendone altri in seguito. Analogamente, il progettista di dispositivi può avere molti dispositivi, ognuno dei quali fornisce capacità uniche, e il modello autodefinito consente al progettista di evitare di registrare la struttura del modello di dispositivo. La variabilità significativa dei modelli autodefiniti può costituire un problema in caso di ridimensionamento a un numero elevato di dispositivi.

#### Modello di dispositivo predefinito
Una distribuzione IoT di produzione che opera con vincoli di alimentazione/elaborazione ottiene molti vantaggi dall'uso di un modello di dispositivo predefinito, che prevede l'uso minimo delle risorse di elaborazione e dell'alimentazione del dispositivo. Analogamente, il traffico di rete minimo consente ai dispositivi di trasmettere tramite reti eterogenee (WiFi, 2G/3G/4G, BLE, satellite e così via), in particolare quando si usa un'infrastruttura con limitazioni e costosa, ad esempio il satellite. Quando si implementa un modello di dispositivo predefinito, è possibile che un progettista di dispositivi invii le informazioni sul dispositivo codificate in uno o due byte, da usare come chiave per il modello di dispositivo predefinito. La brevità di questo approccio consente di ottenere efficienze di uno o due ordini di grandezza rispetto al modello di dispositivo autodefinito.

### Soluzione preconfigurata per il monitoraggio remoto e modello di dispositivo corrispondente
La soluzione preconfigurata per il monitoraggio remoto di Azure IoT Suite implementa un modello di dispositivo autodefinito. L'uso di questo modello consente un'iterazione rapida durante la definizione e l'evoluzione delle capacità del dispositivo.

Il codice in cui la soluzione preconfigurata per il monitoraggio remoto crea un oggetto dispositivo e lo invia al servizio è disponibile in Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs. Esaminando i metodi SendDeviceInfo e GetDeviceInfo è possibile notare il modo in cui viene creato il modello di dispositivo autodefinito e il modo in cui viene inviato all'hub IoT di Azure.

Il codice in cui il servizio cloud elabora gli eventi correlati al modello di dispositivo è disponibile in EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs. Il metodo denominato ProcessJToken è alla base della gestione dei messaggi correlati al modello di dispositivo inviati al lato servizio della soluzione preconfigurata.

Dopo la ricezione di un messaggio correlato al modello del dispositivo, i metodi UpdateDeviceFromSimulatedDeviceInfoPacketAsync e UpdateDeviceFromRealDeviceInfoPacketAsync nel file DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs saranno responsabili dell'aggiornamento del registro dei dispositivi. L'API del registro dei dispositivi nella soluzione preconfigurata per il monitoraggio registro di sistema è disponibile in DeviceManagement/Web/WebApiControllers/DeviceApiController.cs.

### Modelli di dispositivo per il gateway campo
I gateway campo vengono spesso usati per abilitare la connettività e la conversione dei protocolli per i dispositivi che non possono o non devono connettersi direttamente a Internet. Se il dispositivo da creare è un gateway campo, può rappresentare i dispositivi connessi tramite il gateway campo in tutte le interazioni con l'hub IoT di Azure. In quanto produttore del gateway campo, è responsabilità dello sviluppatore implementare la conversione tra i protocolli del dispositivo e i protocolli supportati dal servizio IoT. Se si vuole abilitare il gateway campo alla connessione a dispositivi BLE (Bluetooth Low Energy), sarà necessario implementare l'interfaccia BLE nei dispositivi e l'interfaccia per l'hub IoT di Azure.

## Gestione dell'accesso ai dispositivi
La soluzione preconfigurata di Azure IoT Suite controlla l'accesso a diversi aspetti del dispositivo, inclusi i diritti di accesso in lettura e scrittura per le proprietà del dispositivo e i diritti di esecuzione per i comandi del dispositivo. Nelle soluzioni preconfigurate questo accesso viene controllato tramite Azure Active Directory (AAD) nel portale di amministrazione del dispositivo. È possibile abilitare l'accesso basato sui ruoli estendendo l'uso di AAD nella soluzione preconfigurata.

## Controllo remoto
Il controllo remoto non rientra nell'ambito degli scenari per le soluzioni preconfigurate di Azure IoT Suite. L'analisi seguente, tuttavia, illustra in modo generale le opzioni disponibili per l'abilitazione del controllo remoto nella soluzione IoT.

Negli scenari IT il controllo remoto viene usato spesso per assistere gli utenti remoti o per configurare in modalità remota i server remoti. Negli scenari IoT la maggior parte dei dispositivi non ha utenti attivi, quindi il controllo remoto viene usato in scenari di configurazione remota e di diagnostica. Il controllo remoto può essere implementato usando due modelli diversi:

* Connessione diretta: per abilitare il controllo remoto tramite una connessione diretta a un dispositivo, ad esempio SSH su Linux, Desktop remoto su Windows o tramite gli strumenti di debug remoto, è necessario essere in grado di creare una connessione al dispositivo. Considerato il rischio per la sicurezza derivante dall'esposizione di un dispositivo a Internet aperto, è consigliabile usare un servizio di inoltro, ad esempio il servizio di inoltro del bus di servizio di Azure, per abilitare la connessione e il traffico per/dal dispositivo. Poiché una connessione di inoltro è una connessione in uscita dal dispositivo, consente di limitare la superficie di attacco delle porte TCP aperte sul dispositivo.

* Il controllo remoto basato sui comandi del dispositivo tramite un comando del dispositivo sfrutta la connessione e il canale di comunicazione esistenti stabiliti tra il dispositivo e l'hub IoT di Azure. Per abilitare il controllo remoto basato sui comandi del dispositivo, è necessario implementare i requisiti seguenti:
  * Il software eseguito sul dispositivo deve segnalare al servizio IoT che i comandi del dispositivo sono disponibili sul dispositivo. Questo elemento viene in genere definito come parte del modello del dispositivo.
  * Il software eseguito sul dispositivo deve implementare i comandi del controllo remoto. Questi comandi del dispositivo devono seguire un modello di tipo richiesta (dal servizio IoT al dispositivo) e risposta (dal dispositivo al servizio IoT). L'esecuzione dei comandi del dispositivo potrebbe influire sullo stato del dispositivo e il nuovo stato dovrà essere aggiornato nel registro dei dispositivi.

Quando il dispositivo è l'archivio master per la configurazione e lo stato dei dispositivi, il registro usa un modello coerente e le modifiche allo stato del dispositivo devono essere inserite nel registro dei dispositivi. L'aggiornamento dello stato del registro dei dispositivi può essere imposta da una richiesta dal servizio IoT al dispositivo oppure il dispositivo può aggiornare automaticamente il servizio non appena viene riconosciuta una modifica nello stato del sistema. L'aggiornamento automatico del servizio dal dispositivo potrebbe generare un traffico di rete elevato e potrebbe aumentare l'utilizzo del processore e dell'alimentazione disponibile del dispositivo.

## Amministrazione e monitoraggio remoti
Poiché la maggior parte dei dispositivi IoT differenzia in modo specifico i ruoli utente operativi dai ruoli utente gestionali, l'esperienza di amministrazione remota prevede che gli amministratori monitorino lo stato dei propri dispositivi, configurino il flusso dei dati dai dispositivi nei processi e nelle applicazioni aziendali (ad esempio, CRM, ERP, sistemi di manutenzione e così via) e aggiornino in modalità remota lo stato della configurazione dei dispositivi tramite l'uso dei comandi del dispositivo.

Le soluzioni preconfigurate di Azure IoT Suite forniscono un sito Web di Azure che offre un punto di partenza per l'esperienza di amministrazione dei dispositivi che può essere estesa a scenari nella soluzione IoT verticale.

Gli amministratori definiscono i limiti dell'integrità dei dispositivi come una funzione dei dati operativi del dispositivo, in genere molto veloci, e dei metadati del dispositivo, in genere molto lenti. Un'opzione per abilitare il sistema di avvisi relativi all'integrità dei dispositivi è costituita dalle regole implementate tramite un motore di elaborazione dei flussi, ad esempio Analisi di flusso di Azure, invece che tramite una strategia di polling.

## Configurazione remota
La modifica remota della configurazione di un dispositivo può essere importante per diverse fasi del ciclo di vita di un dispositivo, dal provisioning alla diagnostica o all'integrazione con i processi aziendali. Le modifiche remote della configurazione vengono abilitate tramite una combinazione del modello del dispositivo e la capacità del servizio IoT di aggiornare in modalità remota lo stato dell'istanza di un modello del dispositivo.

Nella soluzione preconfigurata del monitoraggio remoto il dispositivo indica che supporta i comandi del dispositivo seguenti per la configurazione remota:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Questi comandi del dispositivo non vengono visualizzati come comandi del dispositivo disponibili nel portale di amministrazione dei dispositivi della soluzione preconfigurata, poiché si tratta di comandi del dispositivo, che vengono eseguiti in modalità remota da parti specifiche del portale. Quando un comando del dispositivo viene ricevuto dal dispositivo, una risposta di acknowledgement viene inviata dal dispositivo al servizio. Dopo l'elaborazione del comando del dispositivo, il dispositivo invia una risposta risultante che segnala al servizio che l'esecuzione del comando del dispositivo è riuscita o non è riuscita con un codice errore. A questo punto lo stato desiderato per il dispositivo viene confermato sul dispositivo e il registro dei dispositivi viene aggiornato.

## Aggiornamento remoto di firmware e software
L'aggiornamento remoto di firmware e software è una capacità importante di un sistema IoT, poiché consente ai dispositivi di eseguire il software più recente e sicuro. L'aggiornamento remoto di firmware e software su un dispositivo è un esempio di processo con esecuzione prolungata distribuito che coinvolge in genere processi aziendali. Ad esempio, l'aggiornamento del firmware su un dispositivo che controlla una pompa di benzina a efficienza elevata potrebbe richiedere l'esecuzione di passaggi nei sistemi adiacenti per il reindirizzamento del carburante durante l'esecuzione e la verifica dell'aggiornamento.

### Analisi dell'aggiornamento del firmware
Ecco un esempio di implementazione dell'aggiornamento del firmware che potrebbe essere adatto alle esigenze aziendali specifiche dell'utente. L'obiettivo di questo esempio consiste nell'illustrare le considerazioni sul processo, non nell'imporre un'implementazione specifica. Per la progettazione dell'aggiornamento è necessario prendere in considerazione molti fattori aziendali e tecnici, che non rientrano nell'ambito di questo articolo.

Gli aggiornamenti ai dispositivi vengono avviati a livello di servizio IoT e i dispositivi vengono informati in un momento specifico tramite un comando del dispositivo. Quando un dispositivo supporta esplicitamente l'aggiornamento remoto del firmware o del software, il servizio IoT deve recapitare i comandi di aggiornamento in base al processo e ai criteri aziendali definiti. Immediatamente dopo la ricezione del comando del dispositivo per l'aggiornamento, il dispositivo deve scaricare il pacchetto di aggiornamento, effettuare il riavvio nella versione appena distribuita (in caso di aggiornamento del firmware) oppure avviare il nuovo pacchetto software e verificare che il nuovo firmware o software venga eseguito come previsto. Durante questo processo in più fasi, il dispositivo deve segnalare al servizio IoT lo stato del dispositivo durante l'esecuzione dei diversi passaggi.

Il recapito del pacchetto di aggiornamento può essere eseguito tramite un servizio di archiviazione come Archiviazione di Azure oppure tramite una rete per la distribuzione di contenuti (CDN, Content Delivery Network). La verifica dell'integrità del pacchetto scaricato prima dell'applicazione dell'aggiornamento firmware o software è importante per assicurare che il pacchetto provenga dall'origine prevista.

Dopo il completamento di un aggiornamento firmware, il dispositivo deve essere in grado di verificare e identificare uno stato valido. Se il dispositivo non riesce a impostare uno stato valido, il software sul dispositivo deve avviare un rollback a uno stato valido noto. È possibile che lo stato valido noto sia l'ultimo stato valido noto o un'immagine del firmware del dispositivo nota come "stato ottimale" archiviato in una partizione di archiviazione.

<!----HONumber=Oct15_HO1-->