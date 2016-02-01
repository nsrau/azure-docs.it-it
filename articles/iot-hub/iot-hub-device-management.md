<properties
 pageTitle="Gestione dei dispositivi IoT | Microsoft Azure"
 description="Una panoramica dell'utilizzo di IoT Hub e IoT Suite per gestire i dispositivi IoT"
 services="iot-hub,iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="01/21/2016"
 ms.author="dobett"/>

# Gestione dei dispositivi IoT tramite Azure IoT Suite e l'hub IoT di Azure

Azure IoT Suite e l'hub IoT di Azure forniscono le funzionalità fondamentali per consentire la gestione dei dispositivi per soluzioni IoT, su vasta scala, e per un set di dispositivi e topologie di dispositivi di vario tipo. I riferimenti alla gestione dei dispositivi in questo articolo sono correlati in modo specifico alla gestione dei dispositivi IoT.

## Introduzione

I provider di servizi e le aziende, o qualsiasi organizzazione che gestisca un insieme di dispositivi IoT, usano funzionalità di gestione dei dispositivi per eseguire i processi aziendali seguenti:

* Registrare e rilevare dispositivi IoT.
* Abilitare la connettività.
* Configurare e aggiornare in remoto il software nei dispositivi. Ad esempio, quando si gestiscono dispositivi in fabbriche o in campi petroliferi saranno disponibili criteri per la configurazione e l'aggiornamento remoto dei dispositivi, con catene di approvazione, requisiti per il controllo delle normative, presenza di protezioni fisiche e altro ancora.

Quando si abilita la gestione dei dispositivi IoT per la soluzione IoT, è necessario prendere in considerazione le capacità seguenti e determinare l'importanza di ogni capacità sulla base degli obiettivi di business:

* **[Provisioning e rilevamento dei dispositivi](#device-provisioning-and-discovery)**: processo con cui un dispositivo viene registrato nel sistema.
* **[Registro dei dispositivi e modelli dei dispositivi](#device-registry-and-device-models)**: metodo per specificare il modo in cui i modelli dei dispositivi rappresentano l'uso strutturato dei metadati per le relazioni tra i dispositivi, i ruoli nel sistema e i metodi di convalida.
* **[Gestione dell'accesso ai dispositivi](#device-access-management)**: metodo per specificare il modo in cui i dispositivi controllano l'accesso alle risorse dei dispositivi dai servizi cloud.
* **[Controllo remoto](#remote-control)**: metodo per specificare il modo in cui gli utenti remoti ottengono l'accesso ai dispositivi e richiedono modifiche ai dispositivi.
* **[Amministrazione remota](#remote-administration-and-monitoring)**: processo con cui un amministratore definisce lo stato dei dispositivi disponibili.  
* **[Configurazione remota](#remote-configuration)**: metodo con cui gli amministratori modificano la configurazione dei dispositivi.
* **[Aggiornamento remoto di firmware e software](#remote-firmware-and-software-update)**: processo con cui gli amministratori possono aggiornare il firmware e il software dei dispositivi.

Le sezioni seguenti forniscono un approfondimento di ogni capacità di gestione dei dispositivi e descrivono un modello generale per l'implementazione di queste capacità con l'hub IoT di Azure.

## Provisioning e rilevamento dei dispositivi

Il provisioning di un dispositivo con l'hub IoT di Azure si esegue tramite l'API del Registro di sistema. Dopo avere registrato il dispositivo e fornito o ricevuto una chiave, sarà possibile abilitare il dispositivo alla connessione all'hub IoT tramite quella chiave. L'hub IoT di Azure comunica solo con i dispositivi registrati che presentano credenziali autorizzate. Gli amministratori possono disabilitare l'accesso dei dispositivi all'hub IoT di Azure tramite il portale di amministrazione del dispositivo.

È possibile usare un processo bootstrap, in base al modo in cui i dispositivi IoT vengono prodotti, sottoposti a provisioning e distribuiti. È possibile creare un servizio bootstrap come parte della soluzione per fornire la semplice connettività e posticipare il processo di assegnazione del dispositivo a un hub IoT specifico. La posizione in cui verrà usato il dispositivo potrebbe essere sconosciuta quando il dispositivo viene prodotto. Questo è solo un esempio dei tanti flussi di lavoro potenzialmente complessi che consentono la registrazione di un dispositivo nell'hub IoT di Azure e l'integrazione con i processi aziendali esistenti.

Quando si usa un servizio bootstrap, viene avviato un dispositivo IoT che genera una richiesta e ciò può consentire l'accesso a un hub IoT di Azure assegnato. La richiesta deve includere le credenziali bootstrap del dispositivo ed eventuali altri dati necessari. Per i dispositivi autorizzati, il servizio bootstrap deve registrare il dispositivo in un hub IoT di Azure assegnato e deve fornire i dettagli per la connettività al dispositivo che richiede un bootstrap. L'hub IoT fornisce i dettagli per la connettività al dispositivo che richiede un bootstrap.

## Registro dei dispositivi e modelli dei dispositivi

Per *modello di dispositivo* si intende il modello che include le proprietà del dispositivo, che possono essere lette o scritte da un servizio cloud. Include anche i comandi del dispositivo che possono essere eseguiti in modalità remota da un servizio cloud. In un modello basato sul servizio, descritto nella sezione seguente, il dispositivo non conosce il modello, ma i servizi cloud possono comunque registrare e usare i metadati per i dispositivi.

L'archiviazione delle informazioni sui dispositivi e dei metadati associati è essenziale per il sistema IoT e per il ruolo del registro dei dispositivi. Questo vale in particolare per i dispositivi legacy, che non possono essere modificati, o per i dispositivi che non usano un modello di dispositivo. Un modello basato sul servizio può consentire a un servizio IoT di interagire con un insieme di dispositivi. Quando i dispositivi sono in funzione con un modello definito, il registro dei dispositivi può essere usato come una visualizzazione coerente dei dati dei dispositivi, in cui il dispositivo funge da master. In questo caso il servizio informa il dispositivo in merito alle modifiche da apportare e le modifiche saranno valide solo dopo la conferma da parte del dispositivo.

### Modello basato sul servizio

Se un dispositivo si connette all'hub IoT di Azure e non fornisce alcun modello di dispositivo, è importante implementare un registro dei dispositivi che tenga traccia dei dispositivi registrati e dei metadati associati. In questo caso il registro dei dispositivi è l'unica posizione di archiviazione dei metadati per i dispositivi. Gli esempi di metadati di cui tenere traccia includono la topologia logica (ad esempio piano 4, edificio 109), la funzione del dispositivo (ad esempio sensore della porta) o qualsiasi altra informazioni di tag.

### Modello basato sul dispositivo

Per consentire alla soluzione IoT di sfruttare le capacità del dispositivo IoT, è necessario che i dispositivi descrivano se stessi al cloud dopo la connessione all'hub IoT. Di seguito sono riportati due tipi di modelli di dispositivo che si possono usare in una soluzione IoT:

#### Modello di dispositivo autodefinito

Un progettista di dispositivi o uno sviluppatore che usa un simulatore di dispositivo usa il modello di dispositivo autodefinito mediante l'iterazione delle funzionalità del dispositivo durante la creazione. Un progettista di dispositivi può iniziare creando un dispositivo con poche proprietà e pochi comandi e aggiungendone altri in seguito. Analogamente, il progettista di dispositivi può avere molti dispositivi, ognuno dei quali fornisce funzionalità uniche e usa il modello autodefinito. In questo caso, il progettista può evitare di registrare la struttura del modello di dispositivo. La variabilità significativa dei modelli autodefiniti può costituire un problema in caso di ridimensionamento a molti dispositivi.

#### Modello di dispositivo predefinito

Una distribuzione IoT di produzione che opera con vincoli di alimentazione/elaborazione ottiene molti vantaggi dall'uso di un modello di dispositivo predefinito, che contribuisce a ridurre l'uso delle risorse di elaborazione e il consumo energetico del dispositivo. Analogamente, il traffico di rete minimo consente ai dispositivi di trasmettere tramite reti eterogenee (WiFi, 2G/3G/4G, BLE, satellite e così via), in particolare quando si usa un'infrastruttura con limitazioni e costosa, ad esempio il satellite. Quando si implementa un modello di dispositivo predefinito, è possibile che un progettista di dispositivi invii le informazioni sul dispositivo codificate in uno o due byte, da usare come chiave per il modello di dispositivo predefinito. La brevità di questo approccio consente di ottenere efficienze di uno o due ordini di grandezza maggiori rispetto al modello di dispositivo autodefinito.

### Soluzione preconfigurata per il monitoraggio remoto e modello di dispositivo corrispondente

La soluzione preconfigurata per il monitoraggio remoto di Azure IoT Suite implementa un modello di dispositivo autodefinito. L'uso di questo modello consente un'iterazione rapida durante la definizione e l'evoluzione delle capacità del dispositivo.

Il codice sorgente per questa soluzione preconfigurata è disponibile nel repository di GitHub [azure-iot-solution][lnk-azure-iot-solution].

Il codice in cui la soluzione preconfigurata per il monitoraggio remoto crea un oggetto dispositivo e lo invia al servizio è disponibile nel file **Simulator/Simulator.WorkerRole/SimulatorCore/Devices/DeviceBase.cs**. Nei metodi **SendDeviceInfo** e **GetDeviceInfo** è possibile notare il modo in cui il modello di dispositivo autodefinito viene creato e inviato all'hub IoT di Azure.

Il codice in cui il servizio cloud elabora gli eventi correlati al modello di dispositivo è disponibile nel file **EventProcessor/EventProcessor.WorkerRole/Processors/DeviceManagementProcessor.cs**. La maggior parte delle operazioni richieste per gestire i messaggi correlati al modello di dispositivo inviati dal dispositivo al lato servizio della soluzione preconfigurata viene eseguita nel metodo **ProcessJToken**.

Dopo la ricezione di un messaggio correlato al modello del dispositivo, i metodi **UpdateDeviceFromSimulatedDeviceInfoPacketAsync** e **UpdateDeviceFromRealDeviceInfoPacketAsync** nel file **DeviceManagement/Infrastructure/BusinessLogic/DeviceLogic.cs** sono responsabili dell'aggiornamento del registro dei dispositivi. Le API del registro dei dispositivi nella soluzione preconfigurata per il monitoraggio remoto sono disponibili nel file **DeviceManagement/Web/WebApiControllers/DeviceApiController.cs**.

### Modelli di dispositivo per il gateway campo

I gateway campo vengono spesso usati per abilitare la connettività e la conversione dei protocolli per i dispositivi che non possono o non devono connettersi direttamente a Internet. Se il dispositivo da creare è un gateway campo, può rappresentare i dispositivi che si connettono tramite il gateway campo in tutte le interazioni con l'hub IoT di Azure. Il produttore del gateway campo ha anche la responsabilità di implementare la conversione tra i protocolli del dispositivo e i protocolli supportati dal servizio IoT. Se si vuole abilitare il gateway campo alla connessione a dispositivi BLE (Bluetooth Low Energy), è necessario implementare l'interfaccia BLE nei dispositivi e l'interfaccia per l'hub IoT di Azure.

## Gestione dell'accesso ai dispositivi

La soluzione preconfigurata IoT Suite controlla l'accesso a diversi aspetti del dispositivo, inclusi i diritti di accesso in lettura e scrittura per le proprietà del dispositivo e i diritti di esecuzione per i comandi del dispositivo. Nelle soluzioni preconfigurate questo accesso viene controllato tramite Azure Active Directory (AAD) nel portale di amministrazione del dispositivo. È possibile abilitare l'accesso basato sui ruoli estendendo l'uso di AAD nella soluzione preconfigurata.

## Controllo remoto

Il controllo remoto non rientra nell'ambito degli scenari per le soluzioni preconfigurate di Azure IoT Suite. L'analisi seguente, tuttavia, illustra in modo generale le opzioni per l'abilitazione del controllo remoto nella soluzione IoT.

Negli scenari IT il controllo remoto viene usato spesso per assistere gli utenti remoti o per configurare in modalità remota i server remoti. Negli scenari IoT la maggior parte dei dispositivi non ha utenti attivi, quindi il controllo remoto viene usato in scenari di configurazione remota e di diagnostica. È possibile implementare il controllo remoto usando due modelli diversi:

* **Connessione diretta**: per abilitare il controllo remoto tramite una connessione diretta a un dispositivo, ad esempio SSH in Linux, Desktop remoto in Windows o tramite gli strumenti di debug remoto, è necessario poter creare una connessione al dispositivo. Considerato il rischio per la sicurezza derivante dall'esposizione di un dispositivo a Internet aperto, è consigliabile usare un servizio di inoltro, ad esempio il [servizio di inoltro del bus di servizio][service-bus-relay] di Azure, per abilitare la connessione e indirizzare il traffico. Poiché una connessione di inoltro è una connessione in uscita dal dispositivo, consente di limitare la superficie di attacco delle porte TCP aperte sul dispositivo.

* **Comando del dispositivo**: il controllo remoto tramite i comandi del dispositivo sfrutta la connessione e il canale di comunicazione esistenti stabiliti tra il dispositivo e l'hub IoT di Azure. Per abilitare il controllo remoto basato sui comandi del dispositivo, è necessario soddisfare i requisiti seguenti:
  * Il software eseguito sul dispositivo deve segnalare al servizio IoT che i comandi del dispositivo sono disponibili sul dispositivo. Questo elemento viene in genere definito come parte del modello del dispositivo.
  * Il software eseguito sul dispositivo deve implementare i comandi del controllo remoto. I comandi del dispositivo devono seguire un modello di tipo richiesta, dal servizio IoT al dispositivo, e risposta, dal dispositivo al servizio IoT. L'esecuzione dei comandi del dispositivo può influire sullo stato del dispositivo e il nuovo stato deve essere aggiornato nel registro dei dispositivi.

Quando il dispositivo è l'archivio master per la configurazione e lo stato dei dispositivi, il registro usa un modello coerente e le modifiche allo stato del dispositivo devono essere inserite nel registro dei dispositivi. Una richiesta dal servizio IoT al dispositivo può forzare l'aggiornamento dello stato del registro dei dispositivi oppure il dispositivo può aggiornare automaticamente il servizio ogni volta che riconosce una modifica nello stato del sistema. L'aggiornamento automatico del servizio dal dispositivo può generare un traffico di rete elevato e aumentare l'utilizzo del processore e dell'alimentazione disponibile del dispositivo.

## Amministrazione e monitoraggio remoti

La maggior parte dei dispositivi IoT differenzia i ruoli utente operativi dai ruoli utente gestionali. L'amministrazione remota è il metodo con cui gli amministratori possono monitorare lo stato dei propri dispositivi, configurare il flusso dei dati dai dispositivi nei processi e nelle applicazioni aziendali, ad esempio CRM, ERP, sistemi di manutenzione e così via, e aggiornare in modalità remota lo stato della configurazione dei dispositivi usando i comandi del dispositivo.

Le soluzioni preconfigurate di Azure IoT Suite includono un sito Web di Azure che crea un punto di partenza per l'esperienza di amministrazione dei dispositivi che può essere estesa a scenari nella soluzione IoT verticale.

Gli amministratori possono determinare l'integrità dei dispositivi esaminandone i dati operativi, in genere molto veloci, e i metadati, in genere molto lenti. È possibile usare le regole per abilitare il sistema di avvisi relativi all'integrità dei dispositivi implementato tramite un motore di elaborazione dei flussi, ad esempio [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/), anziché tramite una strategia di polling.

## Configurazione remota

La modifica remota della configurazione di un dispositivo può essere importante in diverse fasi del ciclo di vita di un dispositivo, dal provisioning alla diagnostica o all'integrazione con i processi aziendali. Le modifiche remote della configurazione vengono abilitate tramite una combinazione del modello di dispositivo e la capacità del servizio IoT di aggiornare in modalità remota lo stato dell'istanza di un modello di dispositivo.

Nella soluzione preconfigurata per il monitoraggio remoto il dispositivo specifica il supporto dei comandi seguenti per la configurazione remota:

* ChangeKey
* ChangeConfig
* ChangeSystemProperties

Questi comandi del dispositivo non vengono visualizzati come comandi del dispositivo disponibili nel portale di amministrazione dei dispositivi della soluzione preconfigurata, perché vengono eseguiti in modalità remota dal portale in circostanze specifiche. Quando un dispositivo riceve un comando del dispositivo, invia una risposta di acknowledgment al servizio. Dopo l'elaborazione del comando del dispositivo, il dispositivo invia una risposta risultante per segnalare al servizio che l'esecuzione del comando del dispositivo è riuscita o non è riuscita con un codice errore. A questo punto lo stato desiderato per il dispositivo viene confermato sul dispositivo e il registro dei dispositivi viene aggiornato.

## Aggiornamento remoto di firmware e software

Gli aggiornamenti remoti di firmware e software sono una funzionalità importante di un sistema IoT. Gli aggiornamenti consentono ai dispositivi di eseguire le versioni più recenti e sicure del software. L'aggiornamento remoto di firmware e software in un dispositivo è un esempio di processo con esecuzione prolungata distribuito che implica in genere l'interazione con altri processi aziendali. Ad esempio, l'aggiornamento del firmware in un dispositivo che controlla una pompa di carburante ad alta pressione può richiedere l'esecuzione di passaggi in altri sistemi per reindirizzare il carburante durante l'esecuzione e la verifica dell'aggiornamento.

### Analisi degli aggiornamenti del firmware

Ecco un esempio di implementazione degli aggiornamenti del firmware che potrebbe essere adatto alle esigenze aziendali dell'utente. Questo esempio viene fornito per illustrare le considerazioni sul processo e non per imporre un'implementazione specifica. La progettazione di requisiti di aggiornamento che tengano conto di vari fattori tecnici e aziendali non rientra nelle finalità di questo articolo.

Gli aggiornamenti ai dispositivi vengono avviati a livello di servizio IoT e i dispositivi vengono informati in un momento specifico tramite un comando del dispositivo. Quando un dispositivo supporta esplicitamente l'aggiornamento remoto del firmware o del software, il servizio IoT deve recapitare i comandi di aggiornamento in base al processo e ai criteri aziendali definiti. Quando riceve il comando di aggiornamento, il dispositivo deve:

1. Scaricare e distribuire il pacchetto di aggiornamento.
2. Eseguire il riavvio con la configurazione appena distribuita, nel caso di un aggiornamento del firmware, o avviare il nuovo pacchetto software.
3. Verificare che il nuovo firmware o software venga eseguito come previsto.

Per tutto questo processo in più fasi, il dispositivo deve tenere il servizio IoT informato sullo stato del dispositivo durante l'esecuzione dei passaggi.

Un servizio di archiviazione, ad esempio Archiviazione di Azure, o una rete per la distribuzione di contenuti (CDN) può recapitare il pacchetto di aggiornamento. Prima di applicare l'aggiornamento del firmware o del software è importante verificare l'integrità del pacchetto scaricato e la sua provenienza dall'origine prevista.

Dopo il completamento di un aggiornamento firmware, il dispositivo deve essere in grado di verificare e identificare uno stato valido. Se il dispositivo non riesce a impostare uno stato valido, il software sul dispositivo deve avviare un rollback a uno stato valido noto. Lo stato valido noto può essere l'ultimo stato valido noto o un'immagine del firmware del dispositivo nota come *stato ottimale* archiviato in una partizione di archiviazione.

## Passaggi successivi

Per altre informazioni sull'hub IoT di Azure, vedere questi collegamenti:

* [Iniziare a usare l'hub IoT][]
* [Che cos'è l'hub IoT Azure?][]
* [Connetti il tuo dispositivo][]

[Iniziare a usare l'hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Che cos'è l'hub IoT Azure?]: iot-hub-what-is-iot-hub.md
[service-bus-relay]: ../service-bus/service-bus-relay-overview.md
[Connetti il tuo dispositivo]: https://azure.microsoft.com/develop/iot/
[lnk-azure-iot-solution]: https://github.com/Azure/azure-iot-solution

<!---HONumber=AcomDC_0121_2016-->