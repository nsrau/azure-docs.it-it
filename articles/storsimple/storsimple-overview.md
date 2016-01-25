<properties 
   pageTitle="Che cos'è StorSimple? | Microsoft Azure" 
   description="Descrive la suddivisione in livelli, il dispositivo, il dispositivo virtuale, i servizi e la gestione dell'archiviazione di StorSimple e introduce i termini chiave usati in Azure StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carolz" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="12/14/2015"
   ms.author="v-sharos@microsoft.com"/>

# Serie 8000 StorSimple: una soluzione di archiviazione cloud ibrida

## Panoramica

Microsoft Azure StorSimple è una soluzione di archiviazione integrata che gestisce le attività di archiviazione tra i dispositivi locali e l'archiviazione cloud di Microsoft Azure. StorSimple è una soluzione SAN (storage area network) efficiente, dai costi contenuti e facilmente gestibile che elimina molti problemi e spese associati agli archivi dell'organizzazione e alla protezione dei dati. Usa il dispositivo proprietario serie StorSimple 8000, si integra con i servizi cloud e fornisce un set di strumenti di gestione integrati per offrire una visualizzazione semplice di tutti gli archivi dell'organizzazione, inclusa l'archiviazione cloud. Le informazioni sulla distribuzione di StorSimple pubblicate nel sito Web di Microsoft Azure si applicano solo ai dispositivi di StorSimple serie 8000. Se si utilizza un dispositivo di serie StorSimple 5000/7000, andare alla [Guida di StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple utilizza la [suddivisione in livelli](#automatic-storage-tiering) di archiviazione per gestire i dati archiviati in vari supporti di memorizzazione. Il working set corrente è archiviato localmente su unità a stato solido, i dati utilizzati meno frequentemente vengono archiviati su unità a disco rigido e i dati di archiviazione vengono spostati nel cloud. Inoltre, StorSimple utilizza la deduplicazione e la compressione per ridurre la quantità di spazio di archiviazione che i dati utilizzano. Per ulteriori informazioni, vedere [Deduplicazione e compressione](#deduplication-and-compression). Per le definizioni di altri termini e concetti chiave utilizzati nella documentazione della serie 8000 StorSimple, visitare [Terminologia di StorSimple](#storsimple-terminology) alla fine di questo articolo.

Con l'aggiornamento 2 di StorSimple, è possibile identificare i volumi appropriati come *aggiunti in locale* per garantire che i dati primari rimangano a livello locale per il dispositivo e non a livello cloud. Questo consente di eseguire carichi di lavoro sensibili alla latenza cloud, ad esempio carichi di lavoro SQL e delle macchine virtuali, su volumi aggiunti in locale, pur continuando a usare il cloud per il backup. Per altre informazioni sui volumi aggiunti in locale, vedere l'articolo relativo all'[uso del servizio StorSimple Manager per gestire i volumi](storsimple-manage-volumes-u2.md).

L'aggiornamento 2 consente anche di creare dispositivi virtuali StorSimple che sfruttano le basse latenze e le alte prestazioni offerte dall'archiviazione Premium di Azure. Per altre informazioni sui dispositivi virtuali Premium StorSimple, vedere [Distribuire e gestire un dispositivo virtuale StorSimple in Azure](storsimple-virtual-device-u1.md). Per altre informazioni sull'archiviazione Premium di Azure, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

Oltre alla gestione dell'archiviazione, le funzioni di protezione dei dati di StorSimple consentono di creare backup su richiesta e pianificati e quindi di archiviarli in locale o nel cloud. I backup vengono eseguiti sotto forma di snapshot incrementali, il che significa che possono essere creati e ripristinati rapidamente. Gli snapshot cloud possono essere fondamentali in scenari di ripristino di emergenza perché sostituiscono i sistemi di archiviazione secondaria (come il backup su nastro) e consentono di ripristinare i dati nel data center o in siti alternativi, se necessario.

![icona video](./media/storsimple-overview/video_icon.png) Guardare il video per una breve introduzione a Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## Vantaggi dell'uso di StorSimple

Nella tabella seguente vengono descritti alcuni dei vantaggi principali forniti con Microsoft Azure StorSimple.

| Funzionalità | Vantaggi |
|---------|---------|
|Integrazione trasparente | Microsoft Azure StorSimple usa il protocollo iSCSI per collegare in modo invisibile le strutture di archiviazione dati. Questo assicura che i dati archiviati nel cloud, nel data center o in server remoti appaiano come archiviati in un'unica posizione.|
|Riduzione dei costi di archiviazione|Microsoft Azure StorSimple alloca una quantità di memoria locale o cloud sufficiente a soddisfare le richieste correnti ed estende la memoria cloud solo quando è necessario. Riduce ulteriormente i requisiti e le spese di archiviazione eliminando le versioni ridondanti degli stessi dati (deduplicazione) e usando la compressione.|
|Gestione dell'archiviazione semplificata|Microsoft Azure StorSimple offre strumenti di amministrazione di sistema che è possibile usare per configurare e gestire i dati archiviati in locale, su un server remoto e nel cloud. Inoltre, è possibile gestire backup e ripristino di funzioni da uno snap-in Microsoft Management Console (MMC). StorSimple fornisce un’interfaccia opzionale separata che è possibile usare per estendere i servizi di gestione dei dati di StorSimple al contenuto archiviato su server di SharePoint. |
|Miglioramento del ripristino di emergenza e della conformità|Microsoft Azure StorSimple non richiede tempi di recupero estesi. Ripristina invece i dati quando è necessario. Le normali operazioni possono quindi continuare con un'interruzione minima. È anche possibile configurare criteri per specificare le pianificazioni dei backup e la conservazione dei dati.
|Mobilità dei dati|I dati caricati nei servizi cloud di Microsoft Azure sono accessibili da altri siti a scopo di ripristino e migrazione. È anche possibile usare StorSimple per configurare dispositivi virtuali StorSimple in macchine virtuali (VM, Virtual Machine) in esecuzione in Microsoft Azure. Le VM possono quindi usare i dispositivi virtuali per accedere ai dati archiviati a scopo di test o recupero.|
|Supporto per altri provider di servizi cloud |StorSimple serie 8000 con aggiornamento del software 1 o seguenti supporta Amazon S3 con RRS, HP, e i servizi cloud di OpenStack, nonché Microsoft Azure. (È comunque necessario un account di archiviazione di Microsoft Azure per la gestione dei dispositivi) Per altre informazioni, vedere [Novità dell'aggiornamento 1.2](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuità aziendale | L'aggiornamento 1 o successivo offre una nuova funzionalità di migrazione che consente agli utenti della serie StorSimple 5000-7000 di eseguire la migrazione dei dati in un dispositivo StorSimple serie 8000.|
|Disponibilità nel portale di Azure Government | L'aggiornamento 1 di StorSimple o versione successiva è ora disponibile nel portale di Azure per enti pubblici. Per altre informazioni, vedere l'articolo relativo alla [distribuzione di un dispositivo StorSimple locale nel portale di Azure per enti pubblici](storsimple-deployment-walkthrough-gov.md).|
|Disponibilità e protezione dei dati | Con l'aggiornamento 1 o successivo, StorSimple serie 8000 supporta l'archiviazione con ridondanza della zona (ZRS) oltre all'archiviazione con ridondanza locale (LRS) e all'archiviazione con ridondanza geografica (GRS). Fare riferimento a questo [articolo sulle opzioni di ridondanza dell'archiviazione di Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) per informazioni dettagliate sull'archiviazione con ridondanza della zona.|
|Supporto per applicazioni critiche | Con l'aggiornamento 2 di StorSimple, è possibile identificare i volumi appropriati come aggiunti in locale. Questa funzionalità garantisce che i dati richiesti dalle applicazioni critiche non si trovino a livello cloud. I volumi aggiunti in locale non sono soggetti a latenze cloud o a problemi di connettività. Per altre informazioni sui volumi aggiunti in locale, vedere l'articolo relativo all'[uso del servizio StorSimple Manager per gestire i volumi](storsimple-manage-volumes-u2.md).|
|Bassa latenza e alte prestazioni | L'aggiornamento 2 di StorSimple consente anche di creare dispositivi virtuali che sfruttano le funzionalità di bassa latenza e alte prestazioni dell'archiviazione Premium di Azure. Per altre informazioni sui dispositivi virtuali Premium StorSimple, vedere [Distribuire e gestire un dispositivo virtuale StorSimple in Azure](storsimple-virtual-device-u2.md).|

![icona video](./media/storsimple-overview/video_icon.png) Guardare [questo video](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) per una panoramica dei vantaggi e delle funzionalità della serie StorSimple 8000.

## Componenti di StorSimple

La soluzione Microsoft Azure StorSimple include i componenti seguenti:

- **Dispositivo Microsoft Azure StorSimple**: array di archiviazione ibrida locale contenente unità SSD e unità disco rigido, oltre a controller ridondanti e funzionalità di failover automatico. I controller gestiscono il tiering di archiviazione, inserendo i dati attualmente usati (o hot data) nella risorsa di archiviazione locale (nel dispositivo o nei server locali) e spostando nel cloud i dati usati meno di frequente.
- **Dispositivo virtuale StorSimple**: noto anche come dispositivo virtuale StorSimple, è una versione software del dispositivo StorSimple che replica l'architettura e la maggior parte delle funzionalità del dispositivo di archiviazione ibrida fisica. Il dispositivo virtuale StorSimple viene eseguito su un singolo nodo in una macchina virtuale di Azure. I dispositivi virtuali Premium, che sfruttano i vantaggi dell'archiviazione Premium di Azure, sono disponibili nell'aggiornamento 2 e versioni successive.
- **Servizio StorSimple Manager**: estensione del portale di Azure classico che consente di gestire un dispositivo StorSimple o un dispositivo virtuale StorSimple da una singola interfaccia Web. È possibile usare il servizio StorSimple Manager per creare e gestire servizi, visualizzare e gestire dispositivi, visualizzare avvisi, gestire volumi e visualizzare e gestire criteri di backup e il catalogo di backup.
- **Windows PowerShell per StorSimple **– Un’interfaccia della riga di comando che può essere usata per gestire il dispositivo StorSimple. Windows PowerShell per StorSimple include funzionalità che permettono di registrare il dispositivo StorSimple, configurare l'interfaccia di rete nel dispositivo, installare determinati tipi di aggiornamento, risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto e modificare lo stato del dispositivo. È possibile accedere a Windows PowerShell per StorSimple connettendosi alla console seriale o usando la comunicazione remota di Windows PowerShell.
- **i cmdlet PowerShell di azure StorSimple** – un insieme di cmdlet Windows PowerShell che consente di automatizzare le attività a livello di servizio e la migrazione dalla riga di comando. Per altre informazioni sui cmdlet di Azure PowerShell per StorSimple, visitare la [Documentazione di riferimento relativa ai cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **Gestione snapshot StorSimple **– Snap-in MMC (Microsoft Management Console) che usa gruppi di volumi e il servizio Copia Shadow del volume di Windows per generare backup coerenti con l'applicazione. È possibile usare Gestione snapshot StorSimple anche per creare pianificazioni di backup e clonare o ripristinare volumi. 
- **Adattatore StorSimple per SharePoint** – Uno strumento che estende in modo trasparente la risorsa di archiviazione di Microsoft Azure StorSimple e la protezione dei dati al server farm SharePoint, rendendo la risorsa di archiviazione di StorSimple visualizzabile e gestibile dal portale di amministrazione di SharePoint.

Nel diagramma seguente viene fornita una panoramica di alto livello dell'architettura e dei componenti di Microsoft Azure StorSimple.

![Architettura di StorSimple](./media/storsimple-overview/overview-big-picture.png)

Le sezioni seguenti descrivono dettagliatamente ciascuno dei componenti e illustrano il modo in cui la soluzione organizza i dati, alloca le risorse di archiviazione e facilita la gestione dell'archiviazione e la protezione dei dati. Nell'ultima sezione vengono fornite le definizioni di alcuni termini e concetti importanti relativi ai componenti StorSimple e della loro gestione.

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

Per altre informazioni, vedere [Stato e componenti hardware di StorSimple](storsimple-monitor-hardware-status.md).

## Dispositivo virtuale StorSimple

È possibile usare StorSimple per creare un dispositivo virtuale che replica l'architettura e le funzionalità del dispositivo di archiviazione ibrida fisico. Il dispositivo virtuale StorSimple (anche noto come appliance virtuale StorSimple) viene eseguito in un unico nodo in una macchina virtuale di Azure. Un dispositivo virtuale può essere creato solo in una macchina virtuale di Azure e non in un dispositivo StorSimple o in un server locale.

Il dispositivo virtuale presenta le seguenti funzionalità:

- Si comporta come un dispositivo fisico ed è in grado di offrire un'interfaccia iSCSI alle macchine virtuali nel cloud. 
- È possibile creare un numero illimitato di dispositivi virtuali nel cloud e attivarli e disattivarli in base alle esigenze. 
- Consente di simulare ambienti locali in scenari di ripristino di emergenza, sviluppo e test ed è in grado di facilitare il recupero a livello di elementi dai backup. 

Con l'aggiornamento 2 e versioni successive, il dispositivo virtuale StorSimple è disponibile in due modelli: il dispositivo 8010 (precedentemente noto come modello 1100) e il dispositivo 8020. Il dispositivo 8010 ha una capacità massima di 30 TB. Il dispositivo 8020, che sfrutta i vantaggi dell'archiviazione Premium di Azure, ha una capacità massima di 64 TB (l'archiviazione Premium di Azure archivia i dati su unità SSD, mentre l'archiviazione standard archivia i dati nelle unità disco rigido). Si noti che è necessario un account di archiviazione Premium di Azure per usare l'archiviazione Premium. Per altre informazioni sull'archiviazione Premium, vedere [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](storage-premium-storage-preview-portal.md).

Per altre informazioni sul dispositivo virtuale StorSimple, vedere [Distribuire e gestire un dispositivo virtuale StorSimple in Azure](storsimple-virtual-device-u1.md).

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

Per altre informazioni, vedere l'articolo relativo all'[uso del servizio StorSimple Manager per gestire il dispositivo StorSimple](storsimple-manager-service-administration.md).

## Windows PowerShell per StorSimple

Windows PowerShell per StorSimple fornisce un'interfaccia della riga di comando che è possibile usare per creare e gestire il servizio Microsoft Azure StorSimple e configurare e monitorare i dispositivi StorSimple. Si tratta di un'interfaccia della riga di comando basata su Windows PowerShell, che include cmdlet dedicati per la gestione del dispositivo StorSimple. Windows PowerShell per StorSimple offre funzionalità che consentono di:

- Registrare un dispositivo.
- Configurare l'interfaccia di rete in un dispositivo.
- Installare determinati tipi di aggiornamento.
- Risolvere i problemi del dispositivo tramite l'accesso alla sessione di supporto.
- Modificare lo stato del dispositivo.

È possibile accedere a Windows PowerShell per StorSimple da una console seriale (in un computer host connesso direttamente al dispositivo) o da una posizione remota usando la gestione remota di Windows PowerShell. Alcune attività di Windows PowerShell per StorSimple, ad esempio la registrazione iniziale del dispositivo, possono essere eseguite soltanto sulla console seriale.

Per altre informazioni, vedere l'articolo relativo all'[uso di Windows PowerShell per StorSimple per gestire il dispositivo](storsimple-windows-powershell-administration.md).

## Cmdlet PowerShell StorSimple di Azure

I cmdlet StorSimple di Azure PowerShell sono una raccolta di diversi cmdlet di Windows PowerShell che consentono di automatizzare le attività a livello di servizio e di migrazione dalla riga di comando. Per altre informazioni sui cmdlet di Azure PowerShell per StorSimple, visitare la [Documentazione di riferimento relativa ai cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## Gestione snapshot StorSimple

Gestione snapshot StorSimple è uno snap-in di Microsoft Management Console (MMC) che consente di creare copie di backup coerenti e temporizzate di dati locali o cloud. Lo snap-in viene eseguito su un host basato su Windows Server. Gestione snapshot StorSimple può essere usato per:

- Configurare, eseguire il backup ed eliminare volumi.
- Configurare gruppi di volumi per assicurarsi che i dati di cui è stato eseguito il backup siano coerenti con l'applicazione.
- Gestire i criteri di backup in modo che il backup venga eseguito secondo una pianificazione prestabilita e che i dati vengano archiviati in una posizione designata (in locale o sul cloud).
- Ripristinare volumi e singoli file.

I backup vengono acquisiti come snapshot: questo significa che vengono registrate solo le modifiche apportate dopo l'ultimo snapshot, richiedendo uno spazio di archiviazione notevolmente ridotto rispetto a quello necessario per i backup completi. A seconda delle necessità, è possibile creare pianificazioni dei backup o eseguire backup immediati. È possibile usare Gestione snapshot StorSimple anche per definire criteri di conservazione che controllano il numero di snapshot da salvare. Se in un secondo tempo è necessario ripristinare i dati da un backup, Gestione snapshot StorSimple consente di effettuare una selezione dal catalogo degli snapshot locali o cloud.

Se si verifica un'emergenza o è necessario ripristinare i dati per un altro motivo, Gestione snapshot StorSimple esegue il ripristino in modo incrementale, in base alla necessità di disponibilità dei dati. Il ripristino dei dati non richiede l'arresto dell'intero sistema durante il recupero di un file, la sostituzione di un componente o lo spostamento delle operazioni in un altro sito.

Per altre informazioni, vedere [Che cos'è Gestione snapshot StorSimple?](storsimple-what-is-snapshot-manager.md).

## Adattatore StorSimple per SharePoint

Microsoft Azure StorSimple include l'adattatore StorSimple per SharePoint, un componente opzionale che estende in modo trasparente le funzionalità di archiviazione e protezione dati di StorSimple alle server farm di SharePoint. L'adattatore funziona con un provider RBS (Remote Blob Storage) e la funzionalità RBS di SQL Server, consentendo di spostare i BLOB in un server di cui il sistema Microsoft Azure StorSimple ha eseguito il backup. Microsoft Azure StorSimple archivia quindi i dati BLOB localmente o nel cloud, in base all'utilizzo.

L'adattatore StorSimple per SharePoint viene gestito dal portale Amministrazione centrale SharePoint. Di conseguenza, la gestione di SharePoint rimane centralizzata e l'intera archiviazione risulta essere nella farm di SharePoint.

Per altre informazioni, vedere [Adattatore StorSimple per SharePoint](storsimple-adapter-for-sharepoint.md).

## Tecnologie di gestione dell'archiviazione
 
Oltre al dispositivo StorSimple, al dispositivo virtuale e ad altri componenti dedicati, Microsoft Azure StorSimple usa le seguenti tecnologie software per fornire un accesso rapido ai dati e ridurre il consumo di risorse di archiviazione:

- [Suddivisione automatica in livelli dell'archiviazione](#automatic-storage-tiering) 
- [Thin provisioning](#thin-provisioning) 
- [Deduplicazione e compressione](#deduplication-and-compression) 

### Suddivisione automatica in livelli dell'archiviazione

Microsoft Azure StorSimple organizza automaticamente i dati in livelli logici in base all'uso corrente, alla data di creazione e alle relazioni con altri dati. I dati più attivi vengono archiviati in locale, mentre quelli meno attivi e non attivi vengono automaticamente migrati nel cloud. Il diagramma seguente illustra questo approccio all'archiviazione.
 
![Livelli di archiviazione di StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Per abilitare l'accesso rapido, StorSimple archivia i dati molto attivi (hot data) nelle unità SSD del dispositivo StorSimple. Archivia invece i dati usati occasionalmente (warm data) nelle unità disco rigido del dispositivo o nei server del data center. Infine, i dati inattivi, i dati di backup e quelli conservati per scopi di archiviazione o conformità vengono spostati nel cloud.

>[AZURE.NOTE]Nell'aggiornamento 2 o versioni successive, è possibile specificare un volume come aggiunto in locale, nel qual caso i dati rimangono sul dispositivo locale e non a livello di cloud.

StorSimple ordina e riorganizza i dati e le assegnazioni delle risorse di archiviazione in base ai cambiamenti dei modelli di utilizzo. Alcune informazioni, ad esempio, possono diventare meno attive nel corso del tempo. Con il diminuire della frequenza d'uso, viene prima eseguita la migrazione di tali informazioni dalle unità SSD alle unità disco rigido e infine nel cloud. Se ritornano attive, le informazioni vengono di nuovo migrate nel dispositivo di archiviazione.

Il processo di suddivisione in livelli di archiviazione avviene nel modo seguente:

1. Un amministratore di sistema configura un account di archiviazione cloud di Microsoft Azure.
2. L'amministratore utilizza la console seriale e il servizio StorSimple Manager (in esecuzione nel portale di Azure classico) per configurare il dispositivo e il file server, creando volumi e criteri di protezione dei dati. I computer locali (come i file server) utilizzano l'interfaccia iSCSI (Internet Small Computer System Interface) per accedere al dispositivo StorSimple.
3. Inizialmente, StorSimple archivia i dati nel livello SSD rapido del dispositivo.
4. Quando il livello SSD raggiunge il limite di capacità, StorSimple deduplica e comprime i blocchi di dati meno recenti, quindi li sposta nel livello HDD.
5. Quando il livello HDD raggiunge il limite di capacità, StorSimple esegue la crittografia dei blocchi di dati meno recenti e li invia in modo sicuro all'account di archiviazione di Microsoft Azure tramite HTTPS.
6. Microsoft Azure crea più repliche dei dati nel data center e in un data center remoto, assicurando che i dati possono essere recuperati in caso di emergenza. 
7. Quando il file server richiede i dati archiviati nel cloud, StorSimple li restituisce facilmente e ne archivia una copia nel livello SSD del dispositivo StorSimple.

### Thin provisioning

Il thin provisioning è una tecnologia di virtualizzazione in cui le risorse di archiviazione disponibili risultano maggiori delle risorse fisiche. Anziché riservare in anticipo spazio di archiviazione sufficiente, StorSimple usa il thin provisioning per allocare esattamente la quantità di spazio necessaria per soddisfare i requisiti correnti. La natura elastica dell'archiviazione cloud facilita questo approccio, consentendo a StorSimple di aumentare o diminuire lo spazio cloud in base alle variazioni nella domanda.

>[AZURE.NOTE]Non viene eseguito il thin provisioning dei volumi aggiunti in locale. Per l'archiviazione allocata a un volume solo locale, viene eseguito il provisioning completo quando viene creato il volume.

### Deduplicazione e compressione

Microsoft Azure StorSimple usa tecniche di deduplicazione e compressione dei dati per ridurre ulteriormente i requisiti di archiviazione.

La deduplicazione riduce la quantità complessiva di dati archiviati eliminando la ridondanza nel set di dati archiviato. Se i dati subiscono delle variazioni, StorSimple ignora quelli non modificati e acquisisce soltanto le differenze. StorSimple riduce inoltre la quantità di dati archiviati identificando e rimuovendo le informazioni non necessarie.

>[AZURE.NOTE]I dati sui volumi aggiunti in locale non sono deduplicati o compressi. Tuttavia, i backup dei volumi aggiunti in locale sono deduplicati e compressi.

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
|volume aggiunto in locale | volume che risiede sul dispositivo e mai a livello cloud. |
| snapshot locale | Copia temporizzata dei dati di un volume archiviati nel dispositivo Microsoft Azure StorSimple.|
| Microsoft Azure StorSimple | Soluzione ottimale costituita da un dispositivo di archiviazione data center e da un software che consente alle organizzazioni IT di utilizzare al meglio l'archiviazione cloud come se fosse l'archiviazione data center. StorSimple consente di semplificare la protezione e la gestione dei dati riducendo contemporaneamente i costi. Questa soluzione consolida l'archiviazione primaria, l'archiviazione, il backup e il ripristino di emergenza mediante la piena integrazione con il cloud. Combinando l'archiviazione SAN con la gestione dati nel cloud in una piattaforma di livello professionale, i dispositivi StorSimple garantiscono velocità, semplicità e affidabilità per tutte le esigenze di archiviazione.|
| modulo di alimentazione e raffreddamento (PCM, Power and Cooling Module) | Componenti hardware del dispositivo StorSimple costituiti dagli alimentatori e dalla ventola di raffreddamento, da cui il nome modulo di alimentazione e raffreddamento. Lo chassis principale del dispositivo dispone di due PCM da 764 W, mentre lo chassis EBOD dispone di due PCM da 580 W.|
| chassis principale | Chassis principale del dispositivo StorSimple contenente i controller della piattaforma dell'applicazione.|
| obiettivo tempo di ripristino (RTO, Recovery Time Objective) | Intervallo massimo di tempo che deve trascorrere prima che un processo aziendale o un sistema venga completamente ripristinato dopo un'emergenza.| 
|SAS (Serial Attached SCSI) | Tipo di unità disco rigido.|
| chiave DEK del servizio | Chiave disponibile per ogni nuovo dispositivo StorSimple che effettua la registrazione con il servizio StorSimple Manager. I dati di configurazione trasferiti tra il servizio StorSimple Manager e il dispositivo vengono crittografati con una chiave pubblica e quindi possono venire decrittografati solo sul dispositivo con una chiave privata. La chiave DEK del servizio consente al servizio di ottenere questa chiave privata per la decrittografia.|
| chiave di registrazione del servizio | Un chiave che aiuta a registrare il dispositivo StorSimple nel servizio StorSimple Manager, in modo che sia disponibile nel portale di Azure classico per ulteriori azioni di gestione.|
| SCSI (Small Computer System Interface) | Set di standard per connettere fisicamente i computer e passare i dati da uno all'altro.|
| unità SSD | Disco senza componenti mobili, ad esempio un'unità flash.|
| archiviazione di Azure | Set di credenziali di accesso collegato all'account di archiviazione di un determinato provider di servizi cloud.| 
| Adattatore StorSimple per SharePoint| Componente di Microsoft Azure StorSimple che estende in modo trasparente l'archiviazione e la protezione dei dati di StorSimple a farm di SharePoint Server.|
| Servizio StorSimple Manager | Estensione del portale di Azure classico che consente di gestire i dispositivi Azure StorSimple locali e virtuali.|
| Gestione snapshot StorSimple | Snap-in di Microsoft Management Console (MMC) per la gestione di operazioni di backup e ripristino in Microsoft Azure StorSimple.|
| Esegui backup | Funzionalità che consente all'utente di eseguire un backup interattivo di un volume. È un'alternativa al backup manuale di un volume invece del backup automatizzato con un criterio definito.|
| thin provisioning | Metodo per ottimizzare l'efficienza con cui lo spazio di archiviazione disponibile viene utilizzato nei sistemi di archiviazione. Nel thin provisioning lo spazio di archiviazione viene allocato tra più utenti in base allo spazio minimo richiesto da ogni utente in un determinato momento. Vedere anche *fat provisioning*.|
| suddivisione in livelli | Disposizione dei dati in raggruppamenti logici basati sull'utilizzo corrente, l'età e relazioni con altri dati. StorSimple organizza automaticamente i dati in livelli. |
| volume | Aree di archiviazione logiche presentate sotto forma di unità. I volumi StorSimple corrispondono ai volumi montati dall'host, inclusi quelli individuati utilizzando iSCSI e un dispositivo StorSimple.|
 | contenitore del volume | Raggruppamento di volumi con le impostazioni applicabili. Tutti i volumi nel dispositivo StorSimple sono raggruppati in contenitori di volumi. Le impostazioni dei contenitori di volumi includono gli account di archiviazione, le impostazioni di crittografia per i dati inviati al cloud con le chiavi di crittografia associate e la larghezza di banda utilizzata per le operazioni che riguardano il cloud.|
| gruppo di volumi | In Gestione snapshot StorSimple, un gruppo di volumi è una raccolta di volumi configurati per facilitare l'elaborazione di backup.|
| Servizio Copia Shadow del volume (VSS)| Servizio del sistema operativo Windows Server che facilita la coerenza delle applicazioni comunicando con applicazioni compatibili con il Servizio Copia Shadow del volume per coordinare la creazione di snapshot incrementali. Il Servizio Copia Shadow del volume assicura che le applicazioni siano temporaneamente inattive quando vengono eseguiti gli snapshot.|
| Windows PowerShell per StorSimple | Interfaccia della riga di comando basata su Windows PowerShell utilizzata per far funzionare il dispositivo StorSimple e per gestirlo. Pur mantenendo alcune funzionalità di base di Windows PowerShell, questa interfaccia dispone di altri cmdlet dedicati che sono pensati per la gestione di un dispositivo StorSimple.|


## Passaggi successivi

Informazioni sulla [sicurezza di StorSimple](storsimple-security.md).




 

 

<!---HONumber=AcomDC_0114_2016-->