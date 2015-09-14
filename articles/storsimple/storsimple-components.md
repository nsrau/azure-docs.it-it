<properties 
   pageTitle="Cosa sono i componenti di StorSimple? | Microsoft Azure"
	description="Descrive il dispositivo StorSimple, il dispositivo virtuale, i servizi e le tecnologie di gestione e definisce i termini chiave utilizzati nella soluzione."
	services="storsimple"
	documentationCenter="NA"
	authors="SharS"
	manager="AdinaH"
	editor=""/>

<tags
   ms.service="storsimple"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="TBD"
	ms.date="08/26/2015"
	ms.author="v-sharos"/>


# Quali sono i componenti di StorSimple e come funzionano? 

## Panoramica

Microsoft Azure StorSimple è una soluzione di archiviazione integrata che gestisce le attività di archiviazione tra i dispositivi locali e l'archiviazione cloud di Microsoft Azure. StorSimple è stato progettato per ridurre i costi e semplificare la gestione dell'archiviazione, migliorare le funzionalità e l'efficienza del ripristino di emergenza e assicurare la mobilità dei dati.

Le sezioni seguenti descrivono i componenti di Microsoft Azure StorSimple e illustrano il modo in cui la soluzione organizza i dati, alloca le risorse di archiviazione e facilita la gestione dell'archiviazione e la protezione dei dati. Nell'ultima sezione vengono fornite le definizioni di alcuni termini importanti relativi ai componenti StorSimple e della loro gestione.

> [AZURE.NOTE]Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Per informazioni sui dispositivi di serie 7000, vedere: [Guida StorSimple](http://onlinehelp.storsimple.com/).

## Dispositivo StorSimple

Il dispositivo Microsoft Azure StorSimple è un array di archiviazione ibrido locale che fornisce archiviazione primaria e accesso iSCSI ai dati archiviati su di esso. Gestisce le comunicazioni con l'archiviazione cloud e consente di garantire la sicurezza e la riservatezza di tutti i dati archiviati nella soluzione Microsoft Azure StorSimple.

Il dispositivo StorSimple include unità SSD (Solid State Drive) e unità disco rigido, oltre al supporto per il clustering e il failover automatico. Contiene un processore condiviso, uno spazio di archiviazione condiviso e due controller con mirroring. Ciascun controller fornisce quanto segue:

- Connessione a un computer host
- Fino a sei porte di rete per la connessione alla rete LAN (Local Area Network)
- Monitoraggio hardware
- Memoria NVRAM (Non-Volatile Random Access Memory), per mantenere le informazioni anche in caso di interruzione dell'alimentazione
- Aggiornamento compatibile con cluster per gestire gli aggiornamenti software dei server in un cluster di failover, in modo che gli aggiornamenti abbiano un impatto minimo o nullo sulla disponibilità dei servizi
- Servizio cluster, con funzionamento analogo a quello di un cluster back-end, in grado di fornire disponibilità elevata e ridurre al minimo gli effetti negativi provocati da un eventuale errore o disconnessione di un'unità disco rigido o SSD

Solo uno dei due controller è sempre attivo. Il caso di guasto del controller attivo, il secondo si attiva automaticamente.

Per ulteriori informazioni, vedere [Stato e componenti hardware di StorSimple](storsimple-monitor-hardware-status.md).

## Dispositivo virtuale StorSimple

È possibile usare StorSimple per creare un dispositivo virtuale che replica l'architettura e le funzionalità del dispositivo di archiviazione ibrido fisico.

Il dispositivo virtuale StorSimple (anche noto come appliance virtuale StorSimple) viene eseguito in un unico nodo in una macchina virtuale di Azure. Un dispositivo virtuale può essere creato solo in una macchina virtuale di Azure e non in un dispositivo StorSimple o in un server locale. Un dispositivo virtuale StorSimple si distingue da un dispositivo StorSimple fisico per le seguenti caratteristiche:

- Il dispositivo virtuale ha una sola interfaccia di rete, mentre il dispositivo fisico ne ha sei. 
- Il dispositivo virtuale può essere registrato durante la configurazione del dispositivo e non con un'attività separata.
- Non è possibile rigenerare la chiave DEK del servizio da un dispositivo virtuale. Durante il rollover della chiave, l'utente rigenera la chiave nel dispositivo fisico, quindi aggiorna il dispositivo virtuale con la nuova chiave.
- Se è necessario applicare aggiornamenti al dispositivo virtuale, potrebbero esserci dei tempi di inattività. Questo inconveniente non si verifica con un dispositivo StorSimple fisico.

È consigliabile utilizzare il dispositivo virtuale StorSimple per gli scenari di ripristino di emergenza in cui non è disponibile un dispositivo fisico, come lo sviluppo cloud e gli scenari di test.

Per ulteriori informazioni, vedere [Dispositivo virtuale StorSimple](storsimple-virtual-device.md).


## Tecnologie di gestione dell'archiviazione
 
Oltre al dispositivo StorSimple e al dispositivo virtuale dedicati, Microsoft Azure StorSimple usa le seguenti tecnologie software per fornire un accesso rapido ai dati e ridurre il consumo di risorse di archiviazione:

- Suddivisione automatica in livelli dell'archiviazione 
- Thin provisioning 
- Deduplicazione e compressione 

### Suddivisione automatica in livelli dell'archiviazione

Microsoft Azure StorSimple organizza automaticamente i dati in livelli logici in base all'uso corrente, alla data di creazione e alle relazioni con altri dati. I dati più attivi vengono archiviati in locale, mentre quelli meno attivi e non attivi vengono automaticamente migrati nel cloud. Il diagramma seguente illustra questo approccio all'archiviazione.
 
![Livelli di archiviazione di StorSimple](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

Per abilitare l'accesso rapido, StorSimple archivia i dati molto attivi (hot data) nelle unità SSD del dispositivo StorSimple. Archivia invece i dati usati occasionalmente (warm data) nelle unità disco rigido del dispositivo o nei server del data center. Infine, i dati inattivi, i dati di backup e quelli conservati per scopi di archiviazione o conformità vengono spostati nel cloud.

StorSimple ordina e riorganizza i dati e le assegnazioni delle risorse di archiviazione in base ai cambiamenti dei modelli di utilizzo. Alcune informazioni, ad esempio, possono diventare meno attive nel corso del tempo. Con il diminuire della frequenza di uso, tali informazioni vengono prima migrate dalle unità SSD alle unità disco rigido e infine vengono spostate nel cloud. Se ritornano attive, le informazioni vengono di nuovo migrate nel dispositivo di archiviazione.

Il processo di suddivisione in livelli di archiviazione avviene nel modo seguente:

1. Un amministratore di sistema configura un account di archiviazione cloud di Microsoft Azure.
2. L'amministratore utilizza la console seriale e il servizio StorSimple Manager (in esecuzione nel portale di gestione di Azure) per configurare il dispositivo e il file server, creando volumi e criteri di protezione dei dati. Il file server locale utilizza l'interfaccia iSCSI (Internet Small Computer System Interface) per accedere al dispositivo StorSimple.
3. Inizialmente, StorSimple archivia i dati nel livello SSD rapido del dispositivo.
4. Quando il livello SSD raggiunge il limite di capacità, StorSimple deduplica e comprime i blocchi di dati meno recenti, quindi li sposta nel livello HDD.
5. Quando il livello HDD raggiunge il limite di capacità, StorSimple esegue la crittografia dei blocchi di dati meno recenti e li invia in modo sicuro all'account di archiviazione di Microsoft Azure tramite HTTPS.
6. Microsoft Azure crea più repliche dei dati nel data center e in un data center remoto, assicurando che i dati possono essere recuperati in caso di emergenza. 
7. Quando il file server richiede i dati archiviati nel cloud, StorSimple li restituisce facilmente e ne archivia una copia nel livello SSD del dispositivo StorSimple.

### Thin provisioning

Il thin provisioning è una tecnologia di virtualizzazione in cui le risorse di archiviazione disponibili risultano maggiori delle risorse fisiche. Anziché riservare in anticipo spazio di archiviazione sufficiente, StorSimple usa il thin provisioning per allocare esattamente la quantità di spazio necessaria per soddisfare i requisiti correnti. La natura elastica dell'archiviazione cloud facilita questo approccio, consentendo a StorSimple di aumentare o diminuire lo spazio cloud in base alle variazioni nella domanda.

### Deduplicazione e compressione

Microsoft Azure StorSimple usa tecniche di deduplicazione e compressione dei dati per ridurre ulteriormente i requisiti di archiviazione.

La deduplicazione riduce la quantità complessiva di dati archiviati eliminando la ridondanza nel set di dati archiviato. Se i dati subiscono delle variazioni, StorSimple ignora quelli non modificati e acquisisce soltanto le differenze. StorSimple riduce inoltre la quantità di dati archiviati identificando e rimuovendo le informazioni non necessarie.

## Windows PowerShell per StorSimple

Windows PowerShell per StorSimple fornisce un'interfaccia della riga di comando che è possibile usare per creare e gestire il servizio Microsoft Azure StorSimple e configurare e monitorare i dispositivi StorSimple. Si tratta di un'interfaccia della riga di comando basata su Windows PowerShell, che include cmdlet dedicati per la gestione del dispositivo StorSimple. Windows PowerShell per StorSimple offre funzionalità che consentono di:

- Registrare un dispositivo.
- Configurare l'interfaccia di rete in un dispositivo.
- Installare determinati tipi di aggiornamento.
- Risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto.
- Modificare lo stato del dispositivo.

È possibile accedere a Windows PowerShell per StorSimple da una console seriale (in un computer host connesso direttamente al dispositivo) o da una posizione remota usando la gestione remota di Windows PowerShell. Alcune attività di Windows PowerShell per StorSimple, ad esempio la registrazione iniziale del dispositivo, possono essere eseguite soltanto sulla console seriale.

Per ulteriori informazioni, vedere [Utilizzo di Windows PowerShell per StorSimple per amministrare il dispositivo](storsimple-windows-powershell-administration.md).

## Cmdlet PowerShell StorSimple di Azure

I cmdlet StorSimple di Azure PowerShell sono una raccolta di diversi cmdlet di Windows PowerShell che consentono di automatizzare le attività a livello di servizio e di migrazione dalla riga di comando. Per altre informazioni sui cmdlet di Azure PowerShell per StorSimple, visitare la [Documentazione di riferimento relativa ai cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## Servizio StorSimple Manager

Microsoft Azure StorSimple fornisce l'accesso a un'interfaccia utente basata sul Web (il servizio StorSimple Manager) che consente di gestire in modo centralizzato il data center e l'archiviazione cloud. È possibile usare il servizio StorSimple Manager per eseguire le attività seguenti:

- Configurare le impostazioni di sistema per i dispositivi StorSimple.
- Configurare e gestire le impostazioni di sicurezza per i dispositivi StorSimple.
- Configurare le credenziale e le proprietà cloud.
- Configurare e gestire volumi su un server.
- Configurare gruppi di volumi.
- Eseguire il backup e il ripristino dei dati.
- Monitorare le prestazioni.
- Rivedere le impostazioni di sistema e identificare possibili problemi.

È possibile usare il servizio StorSimple Manager per eseguire tutte le attività amministrative, ad eccezione di quelle che richiedono un tempo di inattività del sistema, ad esempio la configurazione iniziale o l'installazione di aggiornamenti.

Per ulteriori informazioni, vedere [Utilizzo del servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).

## Gestione snapshot StorSimple

Gestione snapshot StorSimple è uno snap-in di Microsoft Management Console (MMC) che consente di creare copie di backup coerenti e temporizzate di dati locali o cloud. Lo snap-in viene eseguito su un host basato su Windows Server. Gestione snapshot StorSimple può essere usato per:

- Configurare, eseguire il backup ed eliminare volumi.
- Configurare gruppi di volumi per assicurarsi che i dati di cui è stato eseguito il backup siano coerenti con l'applicazione.
- Gestire i criteri di backup in modo che il backup venga eseguito secondo una pianificazione prestabilita e che i dati vengano archiviati in una posizione designata (in locale o sul cloud).
- Ripristinare volumi e singoli file.

I backup vengono acquisiti come snapshot: questo significa che vengono registrate solo le modifiche apportate dopo l'ultimo snapshot, richiedendo uno spazio di archiviazione notevolmente ridotto rispetto a quello necessario per i backup completi. A seconda delle necessità, è possibile creare pianificazioni dei backup o eseguire backup immediati. È possibile usare Gestione snapshot StorSimple anche per definire criteri di conservazione che controllano il numero di snapshot da salvare. Se in un secondo tempo è necessario ripristinare i dati da un backup, Gestione snapshot StorSimple consente di effettuare una selezione dal catalogo degli snapshot locali o cloud.

Se si verifica un'emergenza o è necessario ripristinare i dati per un altro motivo, Gestione snapshot StorSimple esegue il ripristino in modo incrementale, in base alla necessità di disponibilità dei dati. Il ripristino dei dati non richiede l'arresto dell'intero sistema durante il recupero di un file, la sostituzione di un componente o lo spostamento delle operazioni in un altro sito.

Per ulteriori informazioni, vedere [Che cos'è Gestione snapshot StorSimple?](storsimple-what-is-snapshot-manager.md).

## Adattatore StorSimple per SharePoint

Microsoft Azure StorSimple include l'adattatore StorSimple per SharePoint, un componente opzionale che estende in modo trasparente le funzionalità di archiviazione e protezione dati di StorSimple alle server farm di SharePoint. L'adattatore funziona con un provider RBS (Remote Blob Storage) e la funzionalità RBS di SQL Server, consentendo di spostare i BLOB in un server di cui il sistema Microsoft Azure StorSimple ha eseguito il backup. Microsoft Azure StorSimple archivia quindi i dati BLOB localmente o nel cloud, in base all'utilizzo.

L'adattatore StorSimple per SharePoint viene gestito dal portale Amministrazione centrale SharePoint. Di conseguenza, la gestione di SharePoint rimane centralizzata e l'intera archiviazione risulta essere nella farm di SharePoint.

Per ulteriori informazioni, vedere [Adattatore StorSimple per SharePoint](storsimple-adapter-for-sharepoint.md).


## Terminologia di StorSimple 

Prima di distribuire la soluzione Microsoft Azure StorSimple, è consigliabile esaminare i seguenti termini e definizioni.

### Termini e definizioni chiave

| Termine (acronimo o abbreviazione) | Descrizione |
| ------------------------------ | ---------------- |
| record di controllo di accesso | Record associato a un volume sul dispositivo Microsoft Azure StorSimple che determina quali host vi si possono connettere. Per determinarli, viene usato il nome qualificato iSCSI (IQN) degli host (contenuto nel record di controllo di accesso) che si connettono al dispositivo StorSimple.|
| AES-256 | Algoritmo AES (Advanced Encryption Standard) a 256 bit per la crittografia dei dati in entrata e in uscita dal cloud. |
| dimensioni delle unità di allocazione | Quantità minima di spazio su disco che può essere allocata per conservare un file nei file system di Windows. Se le dimensioni del file non sono un multiplo pari delle dimensioni del cluster, è necessario utilizzare altro spazio per conservare il file (fino al multiplo successivo delle dimensioni del cluster) con conseguenti perdita di spazio e frammentazione del disco rigido. <br>Le dimensioni delle unità di allocazione consigliate per i volumi di Azure StorSimple sono di 64 KB, che sono ideali per gli algoritmi di deduplicazione.|
| suddivisione in livelli di archiviazione automatizzata | Spostamento automatico di dati meno attivi dalle SSD alle HDD e poi in un livello del cloud e abilitazione della gestione di tutta la memoria da un'interfaccia utente centrale.|
| catalogo di backup | Raccolta di backup, in genere correlati dal tipo di applicazione utilizzato. Questa raccolta viene visualizzata nella pagina Catalogo di backup dell'interfaccia utente di servizio StorSimple Manager.|
| file del catalogo di backup | File contenente un elenco di snapshot disponibili attualmente archiviati nel database di backup di Gestione snapshot StorSimple. |
| criterio di backup | Selezione di volumi, tipo di backup e orari che consente di creare backup in base a una pianificazione predefinita.|
| BLOB (Binary Large Object) | Raccolta di dati binari archiviati come singola entità in un sistema di gestione database. I BLOB sono in genere immagini, audio o altri oggetti multimediali, anche se a volte il codice eseguibile binario viene archiviato come BLOB.|
| CHAP (Challenge Handshake Authentication Protocol) | Protocollo utilizzato per autenticare il peer di una connessione, in base al peer che condivide una password o un segreto. CHAP può essere unidirezionale o reciproco. Con CHAP unidirezionale, la destinazione autentica un iniziatore. Per l'autenticazione CHAP reciproca è necessario che la destinazione autentichi l'iniziatore e viceversa. | 
| clone | Copia duplicata di un volume. |
|CaaT (Cloud as a Tier) | Archiviazione cloud integrata come livello nell'architettura di archiviazione in modo che tutta la memoria sia parte di una rete di archiviazione aziendale.|
| provider di servizi cloud (CSP) | Provider di servizi di cloud computing.|
| snapshot cloud | Copia temporizzata dei dati di un volume archiviati nel cloud. Uno snapshot cloud equivale a uno snapshot replicato su un diverso sistema di archiviazione fuori sede. Gli snapshot cloud sono particolarmente utili negli scenari di ripristino di emergenza.|
| chiave di crittografia di archiviazione cloud | Password o chiave utilizzata dal dispositivo StorSimple per accedere ai dati crittografati inviati dal dispositivo al cloud.|
| aggiornamento compatibile con cluster | Gestione degli aggiornamenti software sui server in un cluster di failover in modo che gli aggiornamenti abbiano un effetto minimo o nullo sulla disponibilità dei servizi.|
| percorso dati | Raccolta di unità funzionali che eseguono operazioni di elaborazione dei dati interconnessi.|
| disattivare | Azione definitiva che interrompe la connessione tra il dispositivo StorSimple e il servizio cloud associato. Gli snapshot cloud del dispositivo permangono dopo questo processo e possono essere clonati o usati per il ripristino di emergenza.|
| mirroring disco | Replica in tempo reale di volumi di dischi logici su dischi rigidi separati per garantire la disponibilità continua.|
| mirroring disco dinamico | Replica di volumi di dischi logici su dischi dinamici.|
| dischi dinamici | Formato del volume del disco che utilizza Gestione dischi logici (LDM) per archiviare e gestire i dati tra più dischi fisici. È possibile aumentare le dimensioni dei dischi dinamici per fornire più spazio disponibile.|
| Chassis EBOD (Extended Bunch of Disks) | Chassis secondario del dispositivo Microsoft Azure StorSimple che contiene dischi rigidi aggiuntivi per un maggiore spazio di archiviazione.|
| fat provisioning | Provisioning di archiviazione tradizionale in cui lo spazio di archiviazione viene allocato in base a esigenze previste (ed è in genere maggiore di quello attualmente necessario). Vedere anche *thin provisioning*.|
| unità disco rigido | Unità che usa dischi rotanti per archiviare i dati.|
| risorsa di archiviazione cloud ibrida | Architettura di archiviazione che utilizza risorse locali e fuori sede, inclusa l'archiviazione cloud.|
| iSCSI (Internet Small Computer System Interface) | Standard di rete per l'archiviazione basata sul protocollo IP per collegare le apparecchiature o le strutture di archiviazione dei dati.|
| iniziatore iSCSI | Componente software che consente a un computer host che esegue Windows di connettersi a una rete di archiviazione esterna basata su iSCSI.|
| nome qualificato iSCSI (IQN) | Nome univoco che identifica una destinazione o un iniziatore iSCSI.|
| destinazione iSCSI | Componente software che fornisce sottosistemi di dischi iSCSI centralizzati nelle reti di archiviazione.|
| archiviazione live | Approccio di archiviazione in cui i dati di archivio sono sempre accessibili (ad esempio, non vengono archiviati fuori sede su nastro). Microsoft Azure StorSimple utilizza l'archiviazione live.|
| snapshot locale | Copia temporizzata dei dati di un volume archiviati nel dispositivo Microsoft Azure StorSimple.|
| Microsoft Azure StorSimple | Soluzione ottimale costituita da un dispositivo di archiviazione data center e da un software che consente alle organizzazioni IT di utilizzare al meglio l'archiviazione cloud come se fosse l'archiviazione data center e di semplificare la protezione e la gestione dei dati riducendo contemporaneamente i costi. Questa soluzione consolida l'archiviazione primaria, l'archiviazione, il backup e il ripristino di emergenza mediante la piena integrazione con il cloud. Combinando l'archiviazione SAN con la gestione dati nel cloud in una piattaforma di livello professionale, i dispositivi StorSimple garantiscono velocità, semplicità e affidabilità per tutte le esigenze di archiviazione.|
| modulo di alimentazione e raffreddamento (PCM, Power and Cooling Module) | Componenti hardware del dispositivo StorSimple costituiti dagli alimentatori e dalla ventola di raffreddamento, da cui il nome modulo di alimentazione e raffreddamento. Lo chassis principale del dispositivo dispone di due PCM da 764 W, mentre lo chassis EBOD dispone di due PCM da 580 W.|
| chassis principale | Chassis principale del dispositivo StorSimple contenente i controller della piattaforma dell'applicazione.|
| obiettivo tempo di ripristino (RTO, Recovery Time Objective) | Intervallo massimo di tempo che deve trascorrere prima che un processo aziendale o un sistema venga completamente ripristinato dopo un'emergenza.| 
|SAS (Serial Attached SCSI) | Tipo di unità disco rigido.|
| chiave DEK del servizio | Chiave disponibile per ogni nuovo dispositivo StorSimple che effettua la registrazione con il servizio StorSimple Manager. I dati di configurazione trasferiti tra il servizio StorSimple Manager e il dispositivo vengono crittografati con una chiave pubblica e quindi possono venire decrittografati solo sul dispositivo con una chiave privata. La chiave DEK del servizio consente al servizio di ottenere questa chiave privata per la decrittografia.|
| chiave di registrazione del servizio | Chiave che aiuta a registrare il dispositivo StorSimple nel servizio StorSimple Manager, in modo che sia disponibile nel portale di gestione per ulteriori azioni di gestione.|
| SCSI (Small Computer System Interface) | Set di standard per connettere fisicamente i computer e passare i dati da uno all'altro.|
| unità SSD | Disco senza componenti mobili, ad esempio un'unità flash.|
| archiviazione di Azure | Set di credenziali di accesso collegato all'account di archiviazione di un determinato provider di servizi cloud.| 
| Adattatore StorSimple per SharePoint| Componente di Microsoft Azure StorSimple che estende in modo trasparente l'archiviazione e la protezione dei dati di StorSimple a farm di SharePoint Server.|
| Servizio StorSimple Manager | Estensione del portale di gestione di Azure che consente di gestire i dispositivi Azure StorSimple locali e virtuali.|
| Gestione snapshot StorSimple | Snap-in di Microsoft Management Console (MMC) per la gestione di operazioni di backup e ripristino in Microsoft Azure StorSimple.|
| Esegui backup | Funzionalità che consente all'utente di eseguire un backup interattivo di un volume. È un'alternativa al backup manuale di un volume invece del backup automatizzato con un criterio definito.|
| thin provisioning | Metodo per ottimizzare l'efficienza con cui lo spazio di archiviazione disponibile viene utilizzato nei sistemi di archiviazione. Nel thin provisioning lo spazio di archiviazione viene allocato tra più utenti in base allo spazio minimo richiesto da ogni utente in un determinato momento. Vedere anche *fat provisioning*.|
| volume | Aree di archiviazione logiche presentate sotto forma di unità. I volumi StorSimple corrispondono ai volumi montati dall'host, inclusi quelli individuati utilizzando iSCSI e un dispositivo StorSimple.|
 | contenitore del volume | Raggruppamento di volumi con le impostazioni applicabili. Tutti i volumi nel dispositivo StorSimple sono raggruppati in contenitori di volumi. Le impostazioni dei contenitori di volumi includono gli account di archiviazione, le impostazioni di crittografia per i dati inviati al cloud con le chiavi di crittografia associate e la larghezza di banda utilizzata per le operazioni che riguardano il cloud.|
| gruppo di volumi | In Gestione snapshot StorSimple, un gruppo di volumi è una raccolta di volumi configurati per facilitare l'elaborazione di backup.|
| Servizio Copia Shadow del volume (VSS)| Servizio del sistema operativo Windows Server che facilita la coerenza delle applicazioni comunicando con applicazioni compatibili con il Servizio Copia Shadow del volume per coordinare la creazione di snapshot incrementali. Il Servizio Copia Shadow del volume assicura che le applicazioni siano temporaneamente inattive quando vengono eseguiti gli snapshot.|
| Windows PowerShell per StorSimple | Interfaccia della riga di comando basata su Windows PowerShell utilizzata per far funzionare il dispositivo StorSimple e per gestirlo. Pur mantenendo alcune funzionalità di base di Windows PowerShell, questa interfaccia dispone di altri cmdlet dedicati che sono pensati per la gestione di un dispositivo StorSimple.|


## Passaggi successivi

Informazioni sulla [sicurezza di StorSimple](storsimple-security.md).




 

<!---HONumber=September15_HO1-->