<properties
	pageTitle="Configurare la protezione tra macchine virtuali VMware o server fisici locali e Azure" 
	description="Viene descritto Azure Site Recovery per il coordinamento della replica, del failover e del ripristino di macchine virtuali VMware in locale o di server fisici Windows/Linux in Azure." 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/07/2015"
	ms.author="raynew"/>

# Configurare la protezione tra macchine virtuali VMware o server fisici locali e Azure

Questo articolo descrive come distribuire Site Recovery per:

- **Proteggere le macchine virtuali VMware** - Coordinare la replica, il failover e il ripristino di macchine virtuali VMware locali in Azure
- **Proteggere server fisici** - Coordinare la replica, il failover e il ripristino di server fisici Windows e Linux locali in Azure tramite il servizio Azure Site Recovery.

Questo articolo include una panoramica, i prerequisiti per la distribuzione e istruzioni per l'installazione. Alla fine dell'articolo le macchine virtuali VMware o i server fisici eseguiranno la replica in Azure. Nel caso di problemi, inviare domande al [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Che cos'è Azure Site Recovery?

Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Altre informazioni su [Azure Site Recovery](site-recovery-overview.md).

## Come protegge le risorse locali?

Site Recovery consente di proteggere le risorse locali orchestrando e semplificando la replica, il failover e il failback in numerosi [scenari di distribuzione](site-recovery-overview.md). Se si desidera proteggere macchine virtuali VMware o server fisici Windows o Linux locali, ecco i vantaggi che Site Recovery può offrire:

- Consente agli utenti di VMware di replicare macchine virtuali in Azure
- Consente la replica di server fisici locali in Azure.
- Fornisce un'unica posizione per l'impostazione e la gestione di replica, failover e ripristino.
- Fornisce una soluzione semplice per il failover dall'infrastruttura locale in Azure e il failback (ripristino) da Azure in locale.
- Implementa piani di ripristino per il failover semplificato di carichi di lavoro suddivisi in livelli su più computer.
- Assicura la coerenza di più macchine virtuali, in modo che macchine virtuali e server fisici che eseguono carichi di lavoro specifici possano essere ripristinati insieme a un punto dati coerente.
- Supporta la replica dei dati su Internet, mediante una connessione VPN da sito a sito oppure tramite Azure ExpressRoute.
- Fornisce l'individuazione semplificata di macchine virtuali VMware.


## Che cosa occorre?

Questo diagramma illustra i componenti della distribuzione.

![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

Sono necessari gli elementi seguenti:

**Componente** | **Distribuzione** | **Dettagli**
--- | --- | ---
**Server di configurazione** | <p>Distribuire come una macchina virtuale di Azure A3 standard nella stessa sottoscrizione di Site Recovery.</p> <p>Viene configurato nel portale di Site Recovery</p> | Questo server coordina la comunicazione tra i computer protetti, il server di elaborazione e i server di destinazione master in Azure. Configura la replica e coordina il ripristino in Azure quando si verifica il failover.
**Server di destinazione master** | <p>Distribuire come una macchina virtuale di Azure: come server Windows basato su un'immagine della raccolta di Windows Server 2012 R2 (per proteggere i computer Windows) o come server Linux basato su un'immagine della raccolta di OpenLogic CentOS 6.6 (per proteggere i computer Linux).</p> <p>Sono disponibili tre opzioni di dimensionamento: A4 standard, D14 standard e DS4 standard.<p><p>Il server è connesso alla stessa rete Azure del server di configurazione.</p><p>Viene configurato nel portale di Site Recovery</p> | <p>Riceve e mantiene i dati replicati dai computer protetti tramite VHD collegati e creati nell'archivio BLOB dell'account di archiviazione di Azure.</p> <p>Selezionare DS4 Standard in modo specifico per la configurazione della protezione dei carichi di lavoro che richiedono prestazioni omogenee e a bassa latenza utilizzando l'account di archiviazione Premium.</p>
**Server di elaborazione** | <p>Distribuire come un server virtuale o fisico locale che esegue Windows Server 2012 R2</p> <p>È consigliabile collocarlo nella stessa rete e nello stesso segmento di LAN in cui risiedono i computer da proteggere, ma è possibile eseguirlo in una rete diversa, a condizione che i computer protetti possano avere la visibilità di rete L3 per tale server.<p>Viene impostato e registrato nel server di configurazione nel portale di Site Recovery.</p> | <p>I computer protetti inviano dati di replica al server di elaborazione locale. Dispone di una cache basata su disco per la memorizzazione dei dati di replica ricevuti. Esegue una serie di azioni su tali dati.</p><p>Ottimizza i dati eseguendone il caching, la compressione e la crittografia prima di inviarli al server di destinazione master.</p><p>Gestisce l'installazione push del servizio Mobility.</p><p>Esegue l'individuazione automatica delle macchine virtuali VMware.</p>
**Computer locali** | Macchine virtuali in esecuzione su un hypervisor VMware oppure server fisici che eseguono Windows o Linux. | È possibile configurare le impostazioni di replica che si applicano a macchine virtuali e server. Può essere eseguito il failover di un singolo computer o in modo più ampio, nell'ambito di un piano di ripristino che contiene più macchine virtuali sottoposte a failover insieme.
**Servizio Mobility** | <p>Viene installato in ciascuna macchina virtuale o server fisico da proteggere</p><p>Può essere installato manualmente o trasferito tramite push e installato automaticamente dal server di elaborazione quando la protezione è abilitata per il server. | Il servizio Mobility invia dati al server di elaborazione come parte della replica iniziale (resync.) Una volta che il server raggiunge uno stato protetto (dopo il completamento della risincronizzazione) il servizio Mobility esegue un'acquisizione in memoria di scritture su disco e la invia al server di elaborazione. La coerenza con l’applicazione per il server Windows viene raggiunta tramite il framework VSS.
**Insieme di credenziali di Azure Site Recovery** | Viene configurato dopo avere eseguito la sottoscrizione al servizio Site Recovery. | I server vengono registrati in un insieme di credenziali di Site Recovery. L'insieme di credenziali coordina e orchestra la replica, il failover e il ripristino dei dati tra il sito locale e Azure.
**Meccanismo di replica** | <p>**Tramite Internet**: comunica e replica i dati dai server locali protetti e Azure mediante un canale di comunicazione SSL/TLS sicuro tramite una connessione Internet pubblica. Questa è l'opzione predefinita.</p><p>**VPN/ExpressRoute**: comunica e replica i dati tra i server locali e Azure tramite una connessione VPN. È necessario configurare una VPN da sito a sito o una connessione [ExpressRoute](../expressroute-introduction.md) tra il sito locale e la rete di Azure.</p><p>La modalità di replica verrà selezionata durante la distribuzione di Site Recovery. Non è possibile modificare il meccanismo dopo averlo configurato senza influire sulla protezione dei server già protetti.| <p>Nessuna delle due opzioni richiede l'apertura delle porte di rete in ingresso sui computer protetti. Tutte le comunicazioni di rete vengono avviate dal sito locale.</p> 

Altre informazioni su componenti, i provider e gli agenti di Site Recovery sono disponibili in [Componenti di Site Recovery](site-recovery-components.md).

## Pianificazione della capacità

Le aree principali da considerare sono:

- **Ambiente di origine** - Infrastruttura VMware, impostazioni dei computer di origine e requisiti.
- **Server dei componenti** - Server di elaborazione, server di configurazione e server di destinazione master 

### Considerazioni per l'ambiente di origine

- **Dimensione massima del disco** - La dimensione massima del disco che attualmente può essere collegato a una macchina virtuale è 1 TB. Pertanto, anche la dimensione massima di un disco di origine che può essere replicato è limitata a 1 TB.
- **Dimensione massima per ogni origine** - La dimensione massima di un singolo computer di origine è 31 TB (con 31 dischi) e con il provisioning di un'istanza D14 per il server di destinazione master. 
- **Numero di origini per ogni server di destinazione master** - È possibile proteggere più computer di origine con un singolo server di destinazione master. Tuttavia, non è possibile proteggere un singolo computer di origine in più server di destinazione master. Infatti, quando i dischi vengono replicati, nell'archivio BLOB di Azure viene creato un VHD che rispecchia le dimensioni del disco e che viene collegato come disco dati al server di destinazione master.  
- **Frequenza di modifica giornaliera massima per ogni origine** - Esistono tre fattori da considerare quando si valuta la frequenza di modifica consigliata per ogni origine. Per le considerazioni relative alla destinazione, sono necessarie due operazioni IOPS sul disco di destinazione per ogni operazione nell'origine. Questo avviene perché vengono eseguite un'operazione di lettura dei dati precedenti e un'operazione di scrittura dei nuovi dati sul disco di destinazione. 
	- **Frequenza di modifica giornaliera supportata dal server di elaborazione** - Un computer di origine non può estendersi su più server di elaborazione. Un singolo server di elaborazione può supportare fino a 1 TB di frequenza di modifica giornaliera. Pertanto 1 TB è la frequenza massima di modifica giornaliera dei dati supportata per una macchina di origine. 
	- **Velocità effettiva massima supportata dal disco di destinazione** - La varianza massima per ogni disco di origine non può essere superiore a 144 GB al giorno (con una dimensione di scrittura di 8 K). Vedere la tabella nella sezione relativa al server di destinazione master per la velocità effettiva e gli IOPS della destinazione per le varie dimensioni di scrittura. Questo numero deve essere diviso per due, perché ogni operazione IOP di origine genera 2 IOPS sul disco di destinazione. Fare riferimento a [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) durante la configurazione della destinazione per l'account di archiviazione Premium.
	- **Velocità effettiva massima supportata dall'account di archiviazione** - Un'origine non può estendersi su più account di archiviazione. Dato che un account di archiviazione accetta al massimo 20.000 richieste al secondo e che ogni IOP di origine genera 2 IOPS nel server di destinazione master, è consigliabile mantenere il numero di IOPS nell'origine su 10.000. Fare riferimento a [Obiettivi di scalabilità e prestazioni durante l'uso di Archiviazione Premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) durante la configurazione dell'origine per l'account di archiviazione Premium.

### Considerazioni relative ai server dei componenti

Nella tabella 1 sono riepilogate le dimensioni delle macchine virtuali per i server di configurazione e i server di destinazione master.

**Componente** | **Istanze di Azure distribuite** | **Core** | **Memoria** | **Numero massimo di dischi** | **Dimensioni disco**
--- | --- | --- | --- | --- | ---
Server di configurazione | A3 standard | 4 | 7 GB | 8 | 1023 GB
Server di destinazione master | A4 standard | 8 | 14 GB | 16 | 1023 GB
 | D14 standard | 16 | 112 GB | 32 | 1023 GB
 | DS4 standard | 8 | 28 GB | 16 | 1023 GB

**Tabella 1**

#### Considerazioni sui server di elaborazione

In genere il dimensionamento del server di elaborazione dipende dalla frequenza di modifica giornaliera per tutti i carichi di lavoro protetti. Le considerazioni principali includono:

-	È necessaria una potenza di calcolo sufficiente per eseguire operazioni quali la crittografia e la compressione inline.
-	Il server di elaborazione usa una cache basata su disco. Verificare che siano disponibili lo spazio della cache e la velocità effettiva del disco consigliate per facilitare le modifiche dei dati archiviate in caso di interruzione o collo di bottiglia della rete. 
-	Verificare che sia disponibile larghezza di banda sufficiente, in modo che il server di elaborazione possa caricare i dati nel server di destinazione master per garantire la protezione dei dati continua. 

Nella tabella 2 sono riepilogate le linee guida per il server di elaborazione.

**Frequenza di modifica dei dati** | **CPU** | **Memoria** | **Dimensione disco cache**| **Velocità effettiva disco cache** | **Ingresso/uscita larghezza di banda**
--- | --- | --- | --- | --- | ---
Meno di 300 GB | 4 vCPU (2 socket * 2 core a 2,5 GHz) | 4 GB | 600 GB | Da 7 a 10 MB al secondo | 30 Mbps/21 Mbps
Da 300 a 600 GB | 8 vCPU (2 socket * 4 core a 2,5 GHz) | 6 GB | 600 GB | Da 11 a 15 MB al secondo | 60 Mbps/42 Mbps
Da 600 GB a 1 TB | 12 vCPU (2 socket * 6 core a 2,5 GHz) | 8 GB | 600 GB | Da 16 a 20 MB al secondo | 100 Mbps/70 Mbps
Più di 1 TB | Distribuire un altro server di elaborazione | | | | 

**Tabella 2**

Dove:

- Per ingresso si intende la larghezza di banda in download (rete Intranet tra l'origine e il server di elaborazione).
- Per uscita si intende la larghezza di banda in caricamento (rete Internet tra il server di elaborazione e il server di destinazione master). I valori relativi all'uscita presuppongono una compressione media del server di elaborazione del 30%.
- Per il disco cache è consigliabile un disco separato dal sistema operativo di almeno 128 GB per tutti i server di elaborazione.
- Per la velocità effettiva del disco cache è stata usata l'archiviazione seguente a scopo di benchmark: 8 unità SAS a 10.000 RPM con configurazione RAID 10.

#### Considerazioni relative ai server di configurazione

Ogni server di configurazione può supportare fino a 100 computer di origine con 3 o 4 volumi. Se si superano questi valori, è consigliabile distribuire un altro server di configurazione. Per le proprietà della macchina virtuale predefinita del server di configurazione, vedere la tabella 1.

#### Considerazioni relative al server di destinazione master e all'account di archiviazione

L'archiviazione per ogni server di destinazione master è costituita da un disco del sistema operativo, un volume di conservazione e i dischi dati. L'unità di conservazione mantiene il journal delle modifiche dei dischi per la durata dell'intervallo di conservazione definito nel portale di Site Recovery. Per le proprietà della macchina virtuale del server di destinazione master, fare riferimento alla tabella 1. Nella tabella 3 viene illustrato l'uso dei dischi per A4.

**Istanza** | **Disco del sistema operativo** | **Conservazione** | **Dischi dati**
--- | --- | --- | ---
 | | **Conservazione** | **Dischi dati**
A4 standard | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 dischi (15 * 1023 GB)
D14 standard | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 31 dischi (15 * 1023 GB)
DS4 standard | 1 disco (1 * 1023 GB) | 1 disco (1 * 1023 GB) | 15 dischi (15 * 1023 GB)

**Tabella 3**

La pianificazione della capacità per il server di destinazione master dipende da:

- Limitazioni e prestazioni dell'archiviazione di Azure
	- Il numero massimo di dischi a utilizzo elevato per una VM di livello Standard è di circa 40 (20.000/500 IOPS per disco) in un singolo account di archiviazione. Fare riferimento a [Obiettivi di scalabilità per account di archiviazione standard](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts) per ulteriori informazioni. In modo analogo, fare riferimento a [Obiettivi di scalabilità per gli account di archiviazione Premium](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) per ulteriori informazioni sull'account di archiviazione Premium.
-	Frequenza di modifica giornaliera 
-	Archiviazione del volume di conservazione.

Si noti che:

- Un'origine non può estendersi su più account di archiviazione. Questo vale per il disco dati assegnato agli account di archiviazione selezionati quando si configura la protezione. I dischi del sistema operativo e di conservazione in genere sono assegnati all'account di archiviazione distribuito automaticamente.
- Il volume di archiviazione di conservazione richiesto dipende dalla frequenza di modifica giornaliera e dal numero di giorni di conservazione. L'archiviazione di conservazione richiesta per ogni server di destinazione master è uguale alla varianza totale dall'origine al giorno per il numero di giorni di conservazione. 
- Ogni server di destinazione master ha un solo volume di conservazione. Il volume di conservazione è condiviso tra i dischi collegati al server di destinazione master. Ad esempio:
	- Se è presente una macchina di origine con 5 dischi e ogni disco genera 120 IOPS (dimensioni di 8K) nell'origine, questo si traduce in 240 IOPS per disco (2 operazioni sul disco di destinazione per ogni IO di origine). 240 IOPS è entro il limite di IOPS per disco di Azure, pari a 500.
	- Nel volume di conservazione questo diventa 120 * 5 = 600 IOPS e ciò può rappresentare un collo di bottiglia. In questo scenario, una strategia valida potrebbe essere aggiungere ulteriori dischi al volume di conservazione ed estenderlo, come una configurazione di striping RAID. Ciò consente di migliorare le prestazioni, poiché le operazioni di IOPS vengono distribuite in più unità. Il numero di unità da aggiungere al volume di conservazione sarà come segue:
		- IOPS totali dall'ambiente di origine / 500
		- Varianza totale al giorno dall'ambiente di origine (senza compressione) / 287 GB. 287 GB è la velocità effettiva massima supportata da un disco di destinazione al giorno. Questa metrica varia in base alla dimensione di scrittura con un fattore di 8K, poiché in questo caso 8K è la dimensione di scrittura presupposta. Ad esempio, se la dimensione di scrittura è 4K, la velocità effettiva sarà 287/2. Se la dimensione di scrittura è 16K, la velocità effettiva sarà 287*2.
- Il numero di account di archiviazione richiesto = IOPS di origine totali/10000.


## Prima di iniziare

**Componente** | **Requisiti** | **Dettagli**
--- | --- | --- 
**Account di Azure** | È necessario un account [Microsoft Azure](http://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](pricing/free-trial/).
**Archiviazione di Azure** | <p>Per archiviare i dati replicati, sarà necessario un account di archiviazione di Azure</p><p>Questo deve essere un [account di archiviazione con ridondanza geografica standard](../storage/storage-redundancy.md#geo-redundant-storage) o un [account di archiviazione Premium](../storage/storage-premium-storage-preview-portal.md).</p><p>L'account deve trovarsi nella stessa area geografica del servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione.</p><p>Per ulteriori informazioni, vedere [Introduzione ad Archiviazione di Microsoft Azure](../storage/storage-introduction.md)</p>
**Rete virtuale di Azure** | Sarà necessaria una rete virtuale di Azure in cui saranno distribuiti il server di configurazione e il server di destinazione master. Dovrà trovarsi nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali di Azure Site Recovery. Se si desidera replicare i dati tramite una connessione VPN o ExpressRoute, la rete virtuale di Azure deve essere connessa alla rete locale tramite una connessione ExpressRoute o una VPN da sito a sito.
**Risorse di Azure** | Accertarsi di avere risorse di Azure sufficienti per distribuire tutti i componenti. Per altre informazioni, vedere [Limiti relativi alle sottoscrizioni di Azure](../azure-subscription-service-limits.md).
**Macchine virtuali di Azure** | <p>Le macchine virtuali da proteggere devono essere conformi ai [prerequisiti di Azure](site-recovery-best-practices.md).</p><p>**Numero di dischi** - Possono essere supportati al massimo 31 dischi in un singolo server protetto</p><p>**Dimensioni del disco** - La capacità del singolo disco non deve essere superiore a 1023 GB</p><p>**Clustering** - I server di cluster non sono supportati</p><p>**Avvio** - L'avvio UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) non è supportato</p><p>**Volumi** - I volumi crittografati con Bitlocker non sono supportati</p><p> **Nomi dei server** - I nomi devono contenere da 1 a 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare e terminare con una lettera o un numero. Dopo aver protetto un computer, è possibile modificare il nome di Azure.</p>
**Server di configurazione** | <p>Nella sottoscrizione del server di configurazione verrà creata una macchina virtuale A3 standard basata su un'immagine della raccolta di Windows Server 2012 R2 con Azure Site Recovery. Viene creata come prima istanza in un nuovo servizio cloud. Se si seleziona Internet pubblico come tipo di connettività per il server di configurazione, il servizio cloud verrà creato con un indirizzo IP pubblico riservato.</p><p>Il percorso di installazione dovrà essere specificato solo con caratteri italiani.</p>
**Server master di destinazione** | <p>Macchina virtuale di Azure, A4, D14 o DS4 standard.</p><p>Il percorso di installazione deve essere specificato solo con caratteri italiani. Ad esempio, il percorso per un server di destinazione master che esegue Linux dovrà essere **/usr/local/ASR**.</p></p>
**Server di elaborazione** | <p>È possibile distribuire il server di elaborazione in macchine virtuali o computer fisici che eseguono Windows Server 2012 R2 con gli aggiornamenti più recenti. Eseguire l'installazione in C:/.</p><p>È consigliabile collocare il server nella stessa rete e subnet dei computer da proteggere.</p><p>Installare VMware vSphere CLI 5.5.0 nel server di elaborazione. Il componente VMware vSphere CLI è necessario nel server di elaborazione per individuare le macchine virtuali gestite da un server vCenter o le macchine virtuali in esecuzione in un host ESXi.</p><p>Il percorso di installazione deve essere specificato solo con caratteri italiani.</p>
**VMware** | <p>Server VMware vCenter per la gestione degli hypervisor VMware vSphere. Deve eseguire vCenter versione 5.1 o 5.5 con gli aggiornamenti più recenti.</p><p>Uno o più hypervisor vSphere contenenti le macchine virtuali VMware da proteggere. L'hypervisor deve eseguire ESX/ESXi versione 5.1 o 5.5 con gli aggiornamenti più recenti.</p><p>Nelle macchine virtuali VMware devono essere installati e in esecuzione gli strumenti VMware.</p>
**Computer Windows** | <p>Per le macchine virtuali VMware o i server fisici protetti che eseguono Windows sono previsti diversi requisiti.</p><p>Sistemi operativi a 64 bit supportati: **Windows Server 2012 R2**, **Windows Server 2012** o **Windows Server 2008 R2 con SP1 o successivo**.</p><p>Il nome host, i punti di montaggio, i nomi dei dispositivi e il percorso di sistema di Windows (ad esempio, C:\\Windows) devono essere specificati solo con caratteri italiani.</p><p>Il sistema operativo deve essere installato nell'unità C:\\.</p><p>Sono supportati solo i dischi di base. Non sono supportati i dischi dinamici.</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>Sarà necessario specificare un account amministratore (deve essere un amministratore locale nel computer Windows) per l'installazione push del servizio Mobility in Windows Server. Se l'account specificato non è un account di dominio, si dovrà disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, aggiungere la voce del Registro di sistema DWORD LocalAccountTokenFilterPolicy con un valore di 1 in HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Per aggiungere la voce del Registro di sistema da un'interfaccia della riga di comando, aprire il prompt dei comandi o PowerShell e digitare **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. [Altre informazioni](https://msdn.microsoft.com/library/aa826699.aspx) sul controllo di accesso.</p><p>Dopo il failover, per connettersi alle macchine virtuali Windows in Azure con Desktop remoto, verificare che per il computer locale sia abilitato Desktop remoto. Se non si effettuano connessioni tramite VPN, le regole del firewall dovranno permettere l'esecuzione di connessioni Desktop remoto tramite Internet.</p>
**Computer Linux** | <p> Sistemi operativi a 64 bit supportati: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5 che esegue il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3)**, **SUSE Linux Enterprise Server 11 SP3**.</p><p>Le regole del firewall nei computer protetti devono consentire a tali computer di raggiungere i server di configurazione e di destinazione master in Azure.</p><p>I file /etc/hosts nei computer protetti devono contenere le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede NIC </p><p>Per connettersi a una macchina virtuale di Azure che esegue Linux dopo il failover usando un client Secure Shell (SSH), assicurarsi che il servizio Secure Shell nel computer protetto sia impostato per l'avvio automatico all'avvio del sistema e che le regole del firewall permettano una connessione SSH a tale computer.</p><p>Il nome host, i punti di montaggio, i nomi dei dispositivi e i nomi di file e i percorsi di sistema di Linux (ad esempio /etc/, /usr) dovranno essere specificati solo con caratteri italiani.</p><p>La protezione può essere abilitata per i computer locali con le risorse di archiviazione indicate di seguito: <br>File system: EXT3, ETX4, ReiserFS, XFS<br>Software per percorsi multipli-Device Mapper (percorsi multipli)<br>Archiviazione volumi: LVM2<br>I server fisici con archiviazione del controller HP CCISS non sono supportati.</p>
**Terze parti** | Alcuni componenti della distribuzione in questo scenario dipendono da software di terze parti per funzionare correttamente. Per un elenco completo, vedere [Informazioni e comunicazioni sul software di terze parti](#third-party)

## Distribuzione

Nel grafico vengono riepilogati i passaggi di distribuzione.

![Passaggi di distribuzione](./media/site-recovery-vmware-to-azure/VMWare2AzureSteps.png)

## Tipo di connettività di rete

Sono disponibili due opzioni per configurare la connettività di rete tra il sito locale e la rete virtuale di Azure in cui vengono distribuiti i componenti dell'infrastruttura (server di configurazione, server di destinazione master). È necessario decidere quale opzione di connettività di rete utilizzare prima di distribuire il server di configurazione. Questa è un’opzione del momento della distribuzione e non può essere modificata successivamente.

**Internet pubblico:** la comunicazione e la replica dei dati tra i server locali (server di elaborazione, server protetti) e i server dei componenti dell'infrastruttura di Azure (server di configurazione, server di destinazione master) avvengono tramite una connessione SSL/TLS sicura da endpoint locali a endpoint pubblici nel server di configurazione e nel server di destinazione master. (L'unica eccezione è la connessione tra il server di elaborazione e il server di destinazione master sulla porta TCP 9080 che è non crittografata. Solo le informazioni di controllo relative al protocollo di replica correlato utilizzate per la configurazione della replica vengono scambiate tramite questa connessione).

![Diagramma di distribuzione Internet](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentInternet.png)

**VPN:** la comunicazione e la replica dei dati tra i server locali (server di elaborazione, server protetti) e i server dei componenti dell'infrastruttura di Azure (server di configurazione, server di destinazione master) avvengono tramite una connessione VPN tra la rete locale e la rete virtuale di Azure su cui vengono distribuiti il server di configurazione e i server di destinazione master. Assicurarsi che la rete locale sia connessa alla rete virtuale di Azure tramite una connessione ExpressRoute o una connessione VPN da sito a sito.

![Diagramma di distribuzione VPN](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentVPN.png)


## Passaggio 1: Creare un insieme di credenziali

1. Accedere al [portale di gestione](https://portal.azure.com).


2. Espandere **Servizi dati** > **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.


3. Fare clic su **Crea nuovo** > **Creazione rapida**.

4. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.

5. In **Region** selezionare l'area geografica per l'insieme di credenziali. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](pricing/details/site-recovery/).

6. Fare clic su **Create vault**.

	![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

## Passaggio 2: Distribuire un server di configurazione

### Configurare le impostazioni del server

1. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Nell'elenco a discesa selezionare **Tra un sito locale con server VMware/fisici e Azure**.
3. In **Preparare le risorse (Azure) di destinazione** fare clic su **Distribuisci server di configurazione**.

	![Distribuire un server di configurazione](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS2.png)

4. In **Dettagli del nuovo server di configurazione** specificare:

	- Un nome per il server di configurazione e le credenziali per la connessione.
	- Nel tipo di connettività di rete a discesa selezionare Internet pubblico o VPN.[AZURE.NOTE]Questa impostazione è un’opzione del tempo di distribuzione e non può essere modificata successivamente.  
	- Selezionare la rete di Azure in cui dovrà essere collocato il server. Se VPN è stato specificato come il tipo di connettività di rete verificare che la rete virtuale di Azure sia connessa al sito locale tramite una connessione ExpressRoute o una VPN da sito a sito.
	- Specificare l'indirizzo IP interno e la subnet da assegnare al server. Si noti che i primi quattro indirizzi IP in qualsiasi subnet sono riservati per l'uso interno in Azure. Usare gli altri indirizzi IP disponibili.
	
	![Distribuire un server di configurazione](./media/site-recovery-vmware-to-azure/ASRVMware_CSDetails2.png)

5. Quando si fa clic su **OK**, nella sottoscrizione del server di configurazione verrà creata una macchina virtuale A3 standard basata su un'immagine della raccolta di Windows Server 2012 R2 con Azure Site Recovery. Viene creata come prima istanza in un nuovo servizio cloud. Se è stato specificato il tipo di connettività di rete come Internet pubblico, il servizio cloud viene creato con un indirizzo IP pubblico riservato. Lo stato di avanzamento può essere monitorato nella scheda **Processi**.

	![Monitorare lo stato](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

6.  **Questo passaggio è applicabile solo se il tipo di connettività è Internet pubblico.** Dopo la distribuzione del server di configurazione, annotare l'indirizzo IP pubblico assegnato al server nella pagina **Macchine virtuali** nel portale di Azure. Nella scheda **Endpoint** annotare quindi la porta HTTPS pubblica mappata alla porta privata 443. Queste informazioni saranno necessarie in seguito per la registrazione dei server di destinazione master e di elaborazione con il server di configurazione. Il server di configurazione viene distribuito con gli endpoint indicati di seguito.

	- HTTPS: la porta pubblica viene usata per coordinare la comunicazione tra i server dei componenti e Azure via Internet. La porta privata 443 viene usata per coordinare la comunicazione tra i server dei componenti e Azure tramite VPN.
	- Personalizzato: la porta pubblica viene usata per la comunicazione con lo strumento di failback via Inter
	- net. La porta privata 9443 viene usata per la comunicazione con lo strumento di failback tramite VPN.
	- PowerShell: porta privata 5986
	- Desktop remoto: porta privata 3389
	
	![Endpoint VM](./media/site-recovery-vmware-to-azure/ASRVMWare_VMEndpoints.png)

    >[AZURE.WARNING]Non eliminare o modificare il numero della porta pubblica o privata di qualsiasi endpoint creato durante la distribuzione del server di configurazione.

Il server di configurazione viene distribuito in un servizio cloud di Azure creato automaticamente con un indirizzo IP riservato. L'indirizzo riservato è necessario per garantire che l'indirizzo IP del servizio cloud del server di configurazione rimanga invariato tra i riavvii delle macchine virtuali (incluso il server di configurazione) sul servizio cloud. La prenotazione dell'indirizzo IP pubblico riservato dovrà essere annullata manualmente al momento della rimozione del server di configurazione. In caso contrario, l'indirizzo IP resterà riservato. Esiste un limite predefinito di 20 indirizzi IP pubblici riservati per ogni sottoscrizione. [Altre informazioni](../virtual-network/virtual-networks-reserved-private-ip.md) sugli indirizzi IP riservati.

### Registrare il server di configurazione nell'insieme di credenziali

1. Nella pagina **Avvio rapido** fare clic su **Preparare le risorse di destinazione** > **Scaricare una chiave di registrazione**. Il file di chiave viene generato automaticamente. È valido 5 giorni dopo essere stato generato. Copiare il file nel server di configurazione.
2. In **Macchine virtuali** selezionare il server di configurazione nell'elenco delle macchine virtuali. Aprire la scheda **Dashboard** e fare clic su **Connetti**. Per accedere al server di configurazione con Desktop remoto, **aprire** il file RDP scaricato. Se il server di configurazione viene distribuito in una rete VPN, utilizzare l'indirizzo IP interno (questo è l'indirizzo IP specificato durante la distribuzione del server di configurazione e può essere visualizzato anche nella pagina del dashboard delle macchine virtuali per la macchina virtuale del server di configurazione) del server di configurazione per connettersi con desktop remoto dalla rete locale. Quando si accede per la prima volta, viene eseguita automaticamente la procedura guidata Azure Site Recovery Configuration Server Setup Wizard.

	![Registrazione](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationSplashscreen.png)

3. In **Third-Party Software Installation** fare clic su **I Accept** per scaricare e installare MySQL.

	![Installazione di MySQL](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLEULA.png)

4. In **MySQL Server Details** creare le credenziali per l'accesso all'istanza del server MySQL.

	![Credenziali di MySQL](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLPWD.png)

5. In **Internet Settings** specificare la modalità di connessione a Internet del server di configurazione. Si noti che:

	- Se si vuole usare un server proxy personalizzato, configurarlo prima di installare il provider.
	- Facendo clic su **Next**, verrà eseguito un test per verificare la connessione al proxy.
	- Se si usa un proxy personalizzato oppure se il proxy predefinito richiede l'autenticazione, sarà necessario immettere i dettagli del proxy, tra cui l'indirizzo, la porta e le credenziali.
	- Gli URL seguenti dovranno essere accessibili tramite il proxy:
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **. store.core.windows.net
- Se sono presenti regole firewall basate sull'indirizzo IP, verificare che siano impostate per autorizzare la comunicazione tra il server di configurazione e gli indirizzi IP descritti in [Intervalli IP dei data center di Azure](https://msdn.microsoft.com/IT-IT/library/azure/dn175718.aspx) e per il protocollo HTTPS (443). È necessario aggiungere all'elenco di indirizzi consentiti gli IP dell'area Azure che si prevede di utilizzare e quello degli Stati Uniti occidentali.

	![Registrazione del proxy](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationProxy.png)

6. In **Provider Error Message Localization Settings** specificare in quale lingua visualizzare i messaggi di errore.

	![Registrazione di messaggi di errore](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationLocale.png)

7. In **Azure Site Recovery Registration** individuare e selezionare il file di chiave copiato nel server.

	![Registrazione del file di chiave](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationVault.png)

8. Nella pagina di completamento della procedura guidata selezionare le opzioni seguenti:

	- Selezionare **Launch Account Management Dialog** per specificare che deve essere aperta la finestra Gestisci account dopo il completamento della procedura guidata.
	- Selezionare **Create a desktop icon for Cspsconfigtool** per aggiungere un collegamento sul desktop nel server di configurazione, in modo da poter aprire la finestra di dialogo **Gestisci account** in qualsiasi momento senza dover ripetere la procedura guidata.

	![Completare la registrazione](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationFinal.png)

9. Fare clic su**Finish**per completare la procedura guidata. Verrà generata una passphrase. Copiarla in una posizione sicura. Sarà necessaria per autenticare e registrare i server di elaborazione e di destinazione master con il server di configurazione. Verrà usata anche per assicurare l'integrità del canale nelle comunicazioni del server di configurazione. La passphrase può essere rigenerata, ma si dovrà registrare di nuovo i server di elaborazione e di destinazione master con la nuova passphrase.

	![Passphrase](./media/site-recovery-vmware-to-azure/passphrase.png)

Dopo la registrazione, il server di configurazione verrà elencato nella pagina **Server di configurazione** nell'insieme di credenziali.

### Impostare e gestire gli account

Durante la distribuzione, Site Recovery richiede le credenziali per le azioni seguenti:

- Quando si aggiunge un server vCenter per il rilevamento automatico delle macchine virtuali gestite dal server vCenter. È necessario per il rilevamento automatico delle macchine virtuali di un account di vCenter.
- Quando si aggiungono computer per la protezione, in modo che Site Recovery possa installare il servizio Mobility su di essi.

Dopo aver registrato il server di configurazione, è possibile aprire la finestra di dialogo **Gestisci account** per aggiungere e gestire gli account che devono essere usati per queste azioni. Esistono due modi per eseguire questa operazione:

- Aprire il collegamento che si è scelto di creare per la finestra di dialogo nell'ultima pagina dell'installazione per il server di configurazione (cspsconfigtool).
- Aprire la finestra di dialogo al termine della dell'installazione del server di configurazione.

1. In **Gestisci account** fare clic su **Aggiungi account**. È inoltre possibile modificare ed eliminare account esistenti.

	![Gestire account](./media/site-recovery-vmware-to-azure/ASRVMWare_ManageAccount.png)

2. In **Dettagli account** specificare un nome di account da usare in Azure e le credenziali (dominio/nome utente).

	![Gestire account](./media/site-recovery-vmware-to-azure/ASRVMWare_AccountDetails.png)

### Connettersi al server di configurazione 

Esistono due modi per connettersi al server di configurazione:

- Tramite una connessione VPN da sito a sito o ExpressRoute
- Via Internet 

Si noti che:

- Una connessione Internet usa gli endpoint della macchina virtuale insieme all'indirizzo IP virtuale pubblico del server.
- Una connessione VPN usa l'indirizzo IP interno del server insieme alle porte private dell'endpoint.
- È necessario decidere se eseguire la connessione (dati di controllo e replica) dai server locali ai vari server dei componenti (server di configurazione, server di destinazione master) in esecuzione in Azure tramite una connessione VPN o via Internet. Non è possibile modificare questa impostazione in un secondo momento. Se la si modifica, sarà necessario ridistribuire lo scenario e proteggere nuovamente i computer.  


## Passaggio 3: Distribuire il server di destinazione master

1. In **Preparare le risorse (Azure) di destinazione** fare clic su **Distribuisci server di destinazione master**.
2. Specificare i dettagli e le credenziali del server di destinazione master. Il server sarà distribuito nella stessa rete di Azure in cui risiede il server di configurazione con il quale è stata effettuata la registrazione. Quando si fa clic per confermare, verrà creata una macchina virtuale di Azure con un'immagine della raccolta di Windows o Linux.

	![Impostazioni del server di destinazione](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

Si noti che i primi quattro indirizzi IP in qualsiasi subnet sono riservati per l'uso interno in Azure. Specificare gli altri indirizzi IP disponibili.

>[AZURE.NOTE]Selezionare DS4 standard durante la configurazione della protezione dei carichi di lavoro che richiedono prestazioni di IO elevate e omogenee e bassa latenza per ospitare i carichi di lavoro con numerose operazioni di IO mediante [account di archiviazione Premium](../storage/storage-premium-storage-preview-portal.md).


3. Viene creata una macchina virtuale server di destinazione master di Windows con questi endpoint (gli endpoint pubblici vengono creati solo se il tipo di distribuzione è Internet pubblico):

	- Personalizzato: la porta pubblica viene usata dal server di elaborazione per inviare dati di replica tramite Internet. La porta privata 9443 viene usata dal server di elaborazione per inviare dati di replica al server di destinazione master tramite VPN.
	- Personalizzato1: la porta pubblica viene usata dal server di elaborazione per inviare metadati di controllo tramite Internet. La porta privata 9080 viene usata dal server di elaborazione per inviare metadati di controllo al server di destinazione master tramite VPN.
	- PowerShell: porta privata 5986
	- Desktop remoto: porta privata 3389

4. Viene creata una macchina virtuale server di destinazione master di Linux con questi endpoint (gli endpoint pubblici vengono creati solo se il tipo di distribuzione è Internet pubblico):

	- Personalizzato: la porta pubblica viene usata dal server di elaborazione per inviare dati di replica tramite Internet. La porta privata 9443 viene usata dal server di elaborazione per inviare dati di replica al server di destinazione master tramite VPN.
	- Personalizzato1: la porta pubblica viene usata dal server di elaborazione per inviare metadati di controllo tramite Internet. La porta privata 9080 viene usata dal server di elaborazione per inviare dati di controllo al server di destinazione master tramite VPN.
	- SSH: porta privata 22

    >[AZURE.WARNING]Non eliminare o modificare il numero della porta pubblica o privata di qualsiasi endpoint creato durante la distribuzione del server di destinazione master.

5. In **Macchine virtuali** attendere l'avvio della macchina virtuale.

	- Se il server è stato configurato con Windows, annotare i dettagli relativi a Desktop remoto.
	- Se è stato configurato con Linux e si stabilisce la connessione tramite VPN, annotare l'indirizzo IP interno della macchina virtuale. Se si stabilisce la connessione tramite Internet, annotare l'indirizzo IP pubblico.

6.  Accedere al server per completare l'installazione ed eseguirne la registrazione con il server di configurazione.
7.  Se si usa Windows:

	1. Avviare una connessione Desktop remoto alla macchina virtuale. La prima volta che si accede, verrà eseguito uno script nella finestra di PowerShell. Non chiuderla. Al termine, viene aperto automaticamente lo strumento di configurazione dell'agente host (Host Agent Config) per registrare il server.
	2. Nella finestra **Host Agent Config** specificare l'indirizzo IP interno del server di configurazione e la porta 443. È possibile usare l'indirizzo interno e la porta privata 443 anche se non si usa la modalità VPN per la connessione, perché la macchina virtuale è collegata alla stessa rete di Azure in cui risiede il server di configurazione. Lasciare abilitata l'opzione **Use HTTPS**. Immettere la passphrase per il server di configurazione annotata in precedenza. Fare clic su **OK** per registrare il server. È possibile ignorare le opzioni NAT presenti nella pagina, in quanto non vengono usate.
	3. Se l'unità di conservazione stimata richiede più di 1 TB, è possibile configurare il volume di conservazione (R:) usando un disco virtuale e [spazi di archiviazione](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx)
	
	![Server di destinazione master Windows](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

8. Se si usa Linux:
	1. Assicurarsi di aver installato il LIS (Linux Integration Services) più recente prima dell'installazione del software del server di destinazione master. È possibile trovare la versione più recente di LIS insieme alle istruzioni su come installarlo [qui](https://www.microsoft.com/IT-IT/download/details.aspx?id=46842). Riavviare il computer dopo l’installazione di LIS.
	2. In **Preparare le risorse (Azure) di destinazione** fare clic su **Scarica e installa il software aggiuntivo (solo per il server di destinazione master Linux)** per scaricare il pacchetto del server di destinazione master Linux. Copiare il file TAR scaricato nella macchina virtuale usando un client SFTP. In alternativa, è possibile accedere al server di destinazione master Linux distribuito e usare *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* per scaricare il file.
2. Accedere al server con un client Secure Shell. Se si è connessi alla rete di Azure tramite VPN, usare l'indirizzo IP interno. In caso contrario, usare l'indirizzo IP esterno e l'endpoint pubblico SSH.
	3. Estrarre i file dal programma di installazione compresso con gzip eseguendo: **tar –xvzf Microsoft-ASR\_UA\_8.4.0.0\_RHEL6-64*** ![Server di destinazione master Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)
	4. Accertarsi di essere nella directory nella quale è stato estratto il contenuto del file TAR.
	5. Copiare la passphrase del server di configurazione in un file locale usando il comando **echo *`<passphrase>`* >passphrase.txt**
	6. Eseguire il comando “**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**”.

	![Registrare il server di destinazione](./media/site-recovery-vmware-to-azure/Linux-MT-install.png)

9. Attendere alcuni minuti (da 10 a 15), quindi nella pagina **Server** > **Server di configurazione** verificare che il server di destinazione master sia elencato come registrato nella scheda **Dettagli server**. Se si usa Linux e il server non è stato registrato, eseguire di nuovo lo strumento di configurazione dell'host da /usr/local/ASR/Vx/bin/hostconfigcli. Sarà necessario impostare le autorizzazioni di accesso eseguendo chmod come utente ROOT.

	![Verificare il server di destinazione](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

>[AZURE.NOTE]Si noti che possono essere necessari fino a 15 minuti dopo la registrazione perché il server di destinazione master venga elencato nel server di configurazione. Per aggiornare immediatamente, aggiornare il server di configurazione facendo clic sul pulsante Aggiorna nella parte inferiore della pagina Server di configurazione.

## Passaggio 4: Distribuire il server di elaborazione locale

>[AZURE.NOTE]È consigliabile configurare un indirizzo IP statico nel server di elaborazione in modo da renderlo persistente tra i riavvii.

1. Fare clic su Avvio rapido > **Installare il server di elaborazione locale** > **Scaricare e installare il server di elaborazione**.

	![Installare il server di elaborazione](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2.  Copiare il file ZIP scaricato nel server in cui si installerà il server di elaborazione. Il file ZIP contiene due file di installazione:

	- Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*
	- Microsoft-ASR\_CX\_8.4.0.0\_Windows*

3. Decomprimere il file compresso e copiare i file di installazione in un percorso del server.
4. Eseguire il file di installazione **Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*** e seguire le istruzioni. Verranno installati i componenti di terze parti necessari per la distribuzione.
5. Eseguire quindi **Microsoft-ASR\_CX\_8.4.0.0\_Windows***.
6. Nella pagina **Modalità server** selezionare **Server di elaborazione**.

	![Modalità di selezione del server](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSelection.png)

7. Nella pagina **Dettagli ambiente** eseguire le operazioni seguenti:


	- Se si desidera proteggere macchine virtuali VMware, fare clic su **Sì**.
	- Se si desidera proteggere solo server fisici e pertanto non è necessario installare VMware vCLI nel server di elaborazione, Fare clic su **No** e continuare.
		
	![Registrare il server di configurazione](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerVirtualPhysical.png)

8. Quando si installa VMware vCLI, tenere presente quanto segue:

	- **È supportato solo VMware vSphere CLI 5.5.0**. Il server di elaborazione non funziona con altre versioni o aggiornamenti di vSphere CLI.
	- Scaricare vSphere CLI 5.5.0 da [qui.](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352)
	- Se vSphere CLI è stato installato subito prima di avviare l'installazione del server di elaborazione e non viene rilevato dal programma di installazione, attendere fino a cinque minuti prima di riprovare a eseguire l'installazione. Questo garantisce che tutte le variabili di ambiente necessarie per il rilevamento di vSphere CLI siano state inizializzate correttamente.

9.	In **Selezione della NIC per il server di elaborazione** selezionare la scheda di rete che deve essere usata dal server di elaborazione.

	![Selezionare la scheda](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerNICSelection.png)

10.	In **Dettagli del server di configurazione**:

	- Per l'indirizzo IP e la porta, se si stabilisce la connessione tramite VPN, specificare l'indirizzo IP interno del server di configurazione e la porta 443. In caso contrario, specificare l'indirizzo IP virtuale e l'endpoint HTTP pubblico mappato.
	- Digitare la passphrase del server di configurazione.
	- Per disabilitare la verifica quando si usa il push automatico per installare il servizio, deselezionare **Verifica firma del software del servizio Mobility**. La verifica della firma richiede la connettività Internet dal server di elaborazione.
	- Fare clic su **Avanti**.

	![Registrare il server di configurazione](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerConfigServer.png)


11. In **Selezionare l'unità di installazione** selezionare un'unità cache. Il server di elaborazione richiede un'unità cache con almeno 600 GB di spazio libero. Fare clic su **Installa**.

	![Registrare il server di configurazione](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerCacheConfig.png)

12. Si noti che potrebbe essere necessario riavviare il server per completare l'installazione. In **Server di configurazione** > **Dettagli server** verificare che il server di elaborazione sia visualizzato e risulti registrato correttamente nell'insieme di credenziali.

>[AZURE.NOTE]Possono essere necessari fino a 15 minuti dopo la registrazione perché il server di elaborazione venga elencato nel server di configurazione. Per aggiornare immediatamente, aggiornare il server di configurazione facendo clic sul pulsante Aggiorna nella parte inferiore della pagina Server di configurazione.
 
![Convalidare il server di elaborazione](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Se la verifica della firma per il servizio Mobility non è stata disabilitata al momento della registrazione del server di elaborazione, è possibile farlo in un secondo momento come descritto di seguito:

1. Accedere al server di elaborazione come amministratore e aprire il file C:\\pushinstallsvc\\pushinstaller.conf per la modifica. Nella sezione **[PushInstaller.transport]** aggiungere la riga seguente: **SignatureVerificationChecks=”0”**. Salvare e chiudere il file.
2. Riavviare il servizio InMage PushInstall.


## Passaggio 5: Installare gli aggiornamenti più recenti

Prima di procedere, assicurarsi di avere installato gli aggiornamenti più recenti. È necessario installare gli aggiornamenti nell'ordine seguente:

1. Server di configurazione
2. Server di elaborazione
3. Server di destinazione master

È possibile ottenere gli aggiornamenti nella * * Dashboard * * di Site Recovery. Per l'installazione in Linux, estrarre i file dal programma di installazione gzipped ed eseguire il comando “sudo ./install” per installare l'aggiornamento

Se si eseguono macchine virtuali o server fisici in cui è già installato il servizio Mobility, è possibile ottenere gli aggiornamenti per il servizio come segue:

- Scaricare gli aggiornamenti per il servizio come segue:
	- [Windows Server (solo 64 bit)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
	- [CentOS 6.4,6.5,6.6 (solo 64 bit)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
	- [Oracle Enterprise Linux 6.4,6.5 (solo 64 bit)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
	- [SUSE Linux Enterprise Server SP3 (solo 64 bit)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
- In alternativa, dopo l'aggiornamento del server di elaborazione è possibile ottenere la versione aggiornata del servizio Mobility dalla cartella C:\\pushinstallsvc\\repository nel server di elaborazione.
- Se si dispone di un computer già protetto con una versione precedente del servizio Mobility installato, è possibile anche aggiornare automaticamente il servizio Mobility nei computer protetti dal portale di gestione. A tale scopo, selezionare il gruppo di protezione a cui appartiene il computer, evidenziare il computer protetto e fare clic sul pulsante Aggiorna servizio Mobility nella parte inferiore. Il pulsante Aggiorna servizio Mobility verrà attivato solo se è disponibile una versione più recente del servizio Mobility. Assicurarsi che sul server di elaborazione sia in esecuzione la versione più recente del software del server di elaborazione prima di aggiornare il servizio Mobility. Affinché l'aggiornamento del servizio Mobility funzioni, il server protetto deve soddisfare tutti i [prerequisiti di installazione push automatico](#install-the-mobility-service-automatically).

![Selezionare il server vCenter](./media/site-recovery-vmware-to-azure/ASRVmware_UpdateMobility1.png)

In Seleziona account specificare l'account amministratore da utilizzare per aggiornare il servizio Mobility sul server protetto. Fare clic su OK e attendere il completamento del processo attivato.


## Passaggio 6: Aggiungere server vCenter o host ESXi

1. Nella scheda **Server** > **Server di configurazione** selezionare il server di configurazione e fare clic su **AGGIUNGI SERVER VCENTER** per aggiungere un server vCenter o un host ESXi.

	![Selezionare il server vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Specificare i dettagli del server vCenter o dell'host ESXi e selezionare il server di elaborazione da usare per individuarlo.

	- Se il server vCenter non è in esecuzione sulla porta 443 predefinita, specificare il numero di porta su cui è in esecuzione il server vCenter.
	- È necessario che il server di elaborazione si trovi nella stessa rete del server vCenter o dell'host ESXi e che sia installato VMware vSphere CLI 5.5.0.

	![Impostazioni del server vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter4.png)


3. Dopo il completamento dell'individuazione, il server vCenter sarà elencato nei dettagli del server di configurazione.

	![Impostazioni del server vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)

4. Se si usa un account senza privilegi di amministratore per aggiungere il server vCenter o l'host ESXi, assicurarsi che l'account disponga dei privilegi seguenti:

	- Per gli account vCenter devono essere abilitati i privilegi Datacenter, Datastore, Folder, Host, Network, Resource, Storage views, Virtual machine e vSphere Distributed Switch abilitati.
	- Per gli account di host ESXi devono essere abilitati i privilegi Datacenter, Datastore, Folder, Host, Network, Resource, Virtual machine e vSphere Distributed Switch.



## Passaggio 7: Creare un gruppo di protezione

1. Aprire **Elementi protetti** > **Gruppo di protezione** e fare clic per aggiungere un gruppo di protezione.

	![Creare un gruppo di protezione](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Nella pagina **Specificare le impostazioni del gruppo di protezione** immettere un nome per il gruppo e selezionare il server di configurazione in cui creare il gruppo.

	![Impostazioni del gruppo di protezione](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Nella pagina **Specifica le impostazioni di replica** configurare le impostazioni di replica da usare per tutti i computer nel gruppo.

	![Replica del gruppo di protezione](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Impostazioni:
	- **Coerenza di più VM**: se si attiva questa opzione, vengono creati punti di ripristino coerenti con l'applicazione condivisi tra i computer nel gruppo di protezione. Questa impostazione è importante quando tutti i computer nel gruppo di protezione eseguono lo stesso carico di lavoro. Tutti i computer saranno ripristinati nello stesso punto dati. Disponibile solo per i server Windows.
	- **Soglia RPO**: se il valore RPO della replica per la protezione dati continua supera la soglia RPO configurata, vengono generati avvisi.
	- **Conservazione del punto di ripristino**: specifica l'intervallo di conservazione. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di questo intervallo.
	- **Frequenza di snapshot coerenti con l'applicazione**: specifica con quale frequenza verranno creati i punti di ripristino contenenti snapshot coerenti con l'applicazione.

È possibile monitorare il gruppo di protezione in quanto la creazione avviene nella pagina **Elementi protetti**.

## Passaggio 8: Configurare i computer da proteggere

È necessario installare il servizio Mobility nelle macchine virtuali e nei server fisici che si desidera proteggere. Questa operazione può essere eseguita in due modi:

- Eseguire il push e installare automaticamente il servizio in ogni computer dal server di elaborazione.
- Installare manualmente il servizio. 

### Installare automaticamente il servizio Mobility

Quando si aggiungono computer a un gruppo di protezione, il server di elaborazione effettua automaticamente il push e l'installazione del servizio Mobility in ogni computer.

**Eseguire automaticamente l'installazione push del servizio Mobility nei server Windows:**

1. Installare gli aggiornamenti più recenti per il server di elaborazione, come descritto in [Passaggio 5: Installare gli aggiornamenti più recenti](#step-5-install-latest-updates) e assicurarsi che il server di elaborazione sia disponibile. 
2. Verificare che sia presente la connettività di rete tra il computer di origine e il server di elaborazione e che il computer di origine sia accessibile dal server di elaborazione.  
3. Configurare Windows Firewall per abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows**. Nelle impostazioni di Windows Firewall selezionare l'opzione "Consenti app o funzionalità attraverso Windows Firewall" e selezionare le applicazioni, come illustrato nella figura seguente. Per i computer appartenenti a un dominio, è possibile configurare i criteri del firewall con un oggetto Criteri di gruppo.

	![Impostazioni del firewall](./media/site-recovery-vmware-to-azure/ASRVMWare_PushInstallFirewall.png)

4. L'account usato per eseguire l'installazione push deve appartenere al gruppo Administrators nel computer da proteggere. Queste credenziali vengono usate solo per l'installazione push del servizio Mobility e verranno specificate quando si aggiunge un computer a un gruppo di protezione.
5. Se l'account specificato non è un account di dominio, si dovrà disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, aggiungere la voce del Registro di sistema DWORD LocalAccountTokenFilterPolicy con un valore di 1 in HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Per aggiungere la voce del Registro di sistema da un’interfaccia della riga di comando, aprire il prompt dei comandi o PowerShell e immettere **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**. 

**Eseguire automaticamente l'installazione push del servizio Mobility nei server Linux:**

1. Installare gli aggiornamenti più recenti per il server di elaborazione, come descritto in [Passaggio 5: Installare gli aggiornamenti più recenti](#step-5-install-latest-updates) e assicurarsi che il server di elaborazione sia disponibile.
2. Verificare che sia presente la connettività di rete tra il computer di origine e il server di elaborazione e che il computer di origine sia accessibile dal server di elaborazione.  
3. Verificare che l'account sia un utente ROOT nel server Linux di origine.
4. Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede NIC.
5. Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da proteggere.
6. Assicurarsi che SSH sia abilitato e in esecuzione sulla porta 22. 
7. Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd\_config, come segue: 

	- a) Accedere come utente ROOT.
	- b) Nel file /etc/ssh/sshd\_config trovare la riga che inizia con **PasswordAuthentication**.
	- c) Rimuovere il commento dalla riga e modificare il valore da "no" in "yes".

		![Mobility di Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	- d) Trovare la riga che inizia con Subsystem e rimuovere il commento.
	
		![Mobility push Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility2.png)

8. Verificare che la variante Linux del computer di origine sia supportata.
 
### Installare manualmente il servizio Mobility

I pacchetti software usati per installare il servizio Mobility sono nel server di elaborazione, in C:\\pushinstallsvc\\repository. Accedere al server di elaborazione e copiare il pacchetto di installazione appropriato per il computer di origine in base alla tabella seguente:

| Sistema operativo di origine | Pacchetto del servizio Mobility nel server di elaborazione |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server (solo 64 bit) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 (solo 64 bit) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 (solo 64 bit) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 (solo 64 bit) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |


**Per installare manualmente il servizio Mobility in un server Windows**, eseguire le operazioni seguenti:

1. Copiare il pacchetto **Microsoft-ASR\_UA\_8.4.0.0\_Windows\_GA\_28Jul2015\_release.exe** dal percorso di directory del server di elaborazione elencato nella tabella precedente nel computer di origine.
2. Installare il servizio Mobility eseguendo il file eseguibile nel computer di origine.
3. Seguire le istruzioni del programma di installazione.
4. Selezionare **Servizio Mobility** come ruolo e scegliere **Avanti**.
	
	![Installare il servizio Mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall1.png)

5. Mantenere la directory di installazione come percorso di installazione predefinito e fare clic su **Installa**.
6. Nella finestra **Host Agent Config** specificare l'indirizzo IP e la porta HTTPS del server di configurazione.

	- Se si sta eseguendo la connessione via Internet, specificare l'indirizzo IP virtuale pubblico e l'endpoint HTTPS pubblico come porta.
	- Se si sta eseguendo la connessione tramite VPN, specificare l'indirizzo IP interno e 443 per la porta. Lasciare selezionata l'opzione **Use HTTPS**.

	![Installare il servizio Mobility](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall2.png)

7. Specificare la passphrase del server di configurazione e fare clic su **OK** per registrare il servizio Mobility con il server di configurazione.

**Per eseguire dalla riga di comando:**

1. Copiare la passphrase da CX nel file "C:\\connection.passphrase" sul server ed eseguire questo comando. Nel nostro esempio CX è 104.40.75.37 e la porta HTTPS è 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Installare manualmente il servizio Mobility in un server Linux**:

1. Copiare l'archivio TAR appropriato in base alla tabella precedente dal server di elaborazione nel computer di origine.
2. Aprire un programma shell ed estrarre l'archivio TAR compresso in un percorso locale eseguendo `tar -xvzf Microsoft-ASR_UA_8.2.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio TAR immettendo *`echo <passphrase> >passphrase.txt`* dalla shell.
4. Installare il servizio Mobility immettendo *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Specificare l'indirizzo IP e la porta:

	- Se si sta eseguendo la connessione al server di configurazione via Internet, specificare indirizzo IP pubblico virtuale e l'endpoint HTTPS pubblico del server di configurazione in `<IP address>` e `<port>`.
	- Se si sta eseguendo la connessione tramite VPN, specificare l'indirizzo IP interno e 443.

**Per eseguire dalla riga di comando:**

1. Copiare la passphrase da CX nel file "passphrase.txt" sul server ed eseguire questi comandi. Nel nostro esempio CX è 104.40.75.37 e la porta HTTPS è 62519:

Per eseguire l'installazione in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
Per eseguire l'installazione nel server di destinazione:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE]Quando si aggiungono computer a un gruppo di protezione in cui è già in esecuzione una versione appropriata del servizio Mobility, l'installazione push viene ignorata.


## Passaggio 9: Abilitare la protezione

Per abilitare la protezione, aggiungere macchine virtuali e server fisici a un gruppo di protezione. Prima di iniziare, tenere presente quanto segue:

- Le macchine virtuali vengono individuate ogni 15 minuti e possono essere necessari fino a 15 minuti perché vengano visualizzate in Azure Site Recovery dopo l'individuazione.
- Anche le modifiche dell'ambiente nella macchina virtuale (ad esempio, l'installazione degli strumenti VMware) possono richiedere fino a 15 minuti per l'aggiornamento in Site Recovery.
- È possibile controllare l'ora dell'ultima individuazione nel campo **ULTIMO CONTATTO IN** per il server vCenter o l'host ESXi nella pagina **Server di configurazione**.
- Se è già stato creato un gruppo di protezione e successivamente si aggiunge un server vCenter o un host ESXi, sono necessari 15 minuti per l'aggiornamento del portale di Azure Site Recovery e per la visualizzazione delle macchine virtuali nella finestra di dialogo **Aggiungi macchine virtuali al gruppo di protezione**.
- Se si desidera procedere immediatamente con l'aggiunta di computer al gruppo di protezione senza attendere l'individuazione pianificata, evidenziare il server di configurazione (senza fare clic su di esso) e fare clic sul pulsante **Aggiorna**.
- Quando si aggiungono macchine virtuali o computer fisici a un gruppo di protezione, il server di elaborazione esegue automaticamente il push e l'installazione del servizio Mobility nel server di origine, se non è già installato.
- Per il funzionamento del meccanismo di push automatico, accertarsi di avere configurato i computer protetti come descritto nel passaggio precedente.

Aggiungere i computer come segue:

1. **Elementi protetti** > **Gruppo di protezione** > scheda **Computer**. Fare clic su **AGGIUNGI COMPUTER**. Come procedura consigliata, i gruppi di protezione dovranno riflettere i carichi di lavoro, in modo da aggiungere allo stesso gruppo i computer che eseguono un'applicazione specifica.
2. In **Seleziona macchine virtuali**, se si stanno proteggendo server fisici, nella procedura guidata **Aggiungi computer fisici** specificare l'indirizzo IP e il nome descrittivo. Selezionare quindi la famiglia di sistemi operativi.

	![Aggiungere un server V-Center](./media/site-recovery-vmware-to-azure/ASRVMWare_PhysicalProtect.png)

3. In **Seleziona macchine virtuali**, se si stanno proteggendo macchine virtuali VMware, selezionare un server vCenter che gestisce le macchine virtuali (o l'host EXSi in cui sono in esecuzione) e quindi selezionare le macchine.

	![Aggiungere un server V-Center](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

4. In **Specificare le risorse di destinazione** selezionare i server di destinazione master e l'archiviazione da usare per la replica e indicare se le impostazioni devono essere usate per tutte le macchine virtuali. Selezionare [Account di archiviazione Premium](../storage/storage-premium-storage-preview-portal.md) durante la configurazione della protezione dei carichi di lavoro che richiedono prestazioni di IO elevate e omogenee e bassa latenza per ospitare i carichi di lavoro con numerose operazioni di IO. Se si desidera utilizzare un account di archiviazione Premium per i dischi dei carichi di lavoro, è necessario usare destinazioni master della serie DS. Non è possibile usare dischi di archiviazione Premium su destinazioni master diverse dalla serie DS.

	![Server vCenter](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. In **Specificare gli account** selezionare l'account da usare per l'installazione del servizio Mobility nei computer protetti. Sono necessarie le credenziali dell'account per l'installazione automatica del servizio Mobility. Se non è possibile selezionare un account, assicurarsi di configurare uno come descritto nel passaggio 2. Si noti che questo account non è accessibile da Azure. Per i server Windows è richiesto un account con privilegi di amministratore nel server di origine. Per Linux l'account deve essere l'utente ROOT.

	![Credenziali Linux](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Fare clic sul segno di spunta per completare l'aggiunta dei computer al gruppo di protezione e avviare la replica iniziale per ogni computer. Lo stato può essere monitorato nella pagina **Processi**.

	![Aggiungere un server V-Center](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs2.png)

7. È inoltre possibile monitorare lo stato della protezione facendo clic su **Elementi protetti** > nome del gruppo di protezione > **Macchine virtuali**. Dopo il completamento della replica iniziale e la sincronizzazione dei dati nei computer, verrà visualizzato lo stato **Protetto**.

	![Processi di macchine virtuali](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)


### Impostare le proprietà dei computer protetti

1. Dopo che per un computer viene visualizzato lo stato **Protetto**, è possibile configurarne le proprietà di failover. Nei dettagli del gruppo di protezione selezionare il computer e aprire la scheda **Configura**.
2. È possibile modificare il nome assegnato al computer in Azure dopo il failover e le dimensioni della macchina virtuale di Azure. È anche possibile selezionare la rete di Azure alla quale verrà connesso il computer dopo il failover.

	![Impostare le proprietà di una macchina virtuale](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

Si noti che:

- Il nome del computer in Azure deve rispettare i requisiti di Azure.
- Per impostazione predefinita, le macchine virtuali replicate in Azure non sono connesse a una rete di Azure. Per permettere la comunicazione delle macchine virtuali, accertarsi di impostare la stessa rete di Azure.
- Se si ridimensiona un volume in una macchina virtuale VMware o un server fisico, questo entra in uno stato critico. Se è necessario modificare la dimensione, eseguire le operazioni seguenti:

	- a) Modificare l'impostazione della dimensione.
	- b) Nella scheda **Macchine virtuali** selezionare la macchina virtuale e fare clic su **Rimuovi**.
	- c) In **Rimuovi macchina virtuale** selezionare l'opzione **Disabilita protezione (Usare per analisi ripristino e ridimensionamento volume)**. Questa opzione disabilita la protezione ma mantiene i punti di ripristino in Azure.

		![Impostare le proprietà di una macchina virtuale](./media/site-recovery-vmware-to-azure/ASRVMWare_RemoveVM.png)

	- d) Abilitare nuovamente la protezione per la macchina virtuale. Quando si abilita nuovamente la protezione, i dati per il volume ridimensionato verranno trasferiti in Azure.

	

## Passaggio 10: Eseguire un failover

Attualmente è possibile eseguire solo failover non pianificati per le macchine virtuali VMware e i server fisici protetti. Tenere presente quanto segue:



- Prima di avviare un failover, assicurarsi che i server di configurazione e di destinazione master siano in esecuzione e integri. In caso contrario, il failover avrà esito negativo.
- I computer di origine non vengono arrestati nell'ambito di un failover non pianificato. L'esecuzione di un failover non pianificato interrompe la replica dei dati per i server protetti. È necessario eliminare i computer dal gruppo di protezione e aggiungerli nuovamente per iniziare a proteggerli di nuovo dopo il failover non pianificato.
- Se si desidera eseguire il failover senza perdite di dati, assicurarsi che le macchine virtuali del sito primario siano disattivate prima di avviare il failover.

1. Nella pagina **Piani di ripristino** aggiungere un piano di ripristino. Specificare i dettagli per il piano e selezionare **Azure** come destinazione.

	![Configurare un piano di ripristino](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. In **Seleziona macchine virtuali** selezionare un gruppo di protezione e quindi i computer nel gruppo da aggiungere al piano di ripristino. [Ulteriori informazioni](site-recovery-create-recovery-plans.md) sui piani di ripristino.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Se necessario, è possibile personalizzare il piano per creare gruppi e una sequenza dell'ordine in cui verrà eseguito il failover dei computer nel piano di ripristino. È anche possibile aggiungere istruzioni per azioni manuali e script. Gli script durante il ripristino in Azure possono essere aggiunti tramite i [runbook di automazione di Azure](site-recovery-runbook-automation.md).

4. Nella pagina **Piani di ripristino** selezionare il piano e fare clic su **Failover non pianificato**.
5. In **Conferma failover** verificare la direzione del failover (in Azure) e selezionare il punto di ripristino in cui eseguire il failover.
6. Attendere il completamento del processo di failover e quindi verificare che sia stato eseguito nel modo previsto e che le macchine virtuali replicate vengano avviate in Azure.




## Passaggio 11: Eseguire il failback dei computer sottoposti a failover da Azure

[Altre informazioni](site-recovery-failback-azure-to-vmware.md) su come rendere nuovamente disponibili nell'ambiente locale i computer sottoposti a failover in esecuzione in Azure.


## Gestire i server di elaborazione

Il server di elaborazione invia i dati di replica al server di destinazione master in Azure e individua le nuove macchine virtuali VMware aggiunte a un server vCenter. Nelle seguenti circostanze può essere necessario cambiare il server di elaborazione nella distribuzione:

- Se il server di elaborazione corrente non è disponibile
- Se l'obiettivo del punto di ripristino (RPO) raggiunge un livello non accettabile per l'organizzazione.

Se necessario, è possibile spostare in un server di elaborazione diverso la replica di alcuni o tutti i server fisici e le macchine virtuali VMware in locale. Ad esempio:

- **Errore** - Se si verifica un errore in un server di elaborazione o questo risulta non disponibile, è possibile spostare la replica dei computer protetti in un altro server di elaborazione. I metadati del computer di origine e del computer di replica vengono spostati nel nuovo server di elaborazione e i dati vengono sincronizzati di nuovo. Il nuovo server di elaborazione si connetterà automaticamente al server vCenter per eseguire l'individuazione automatica. È possibile monitorare lo stato dei server di elaborazione nel dashboard di Site Recovery.
- **Bilanciamento del carico per regolare l'obiettivo del punto di ripristino** - Per migliorare il bilanciamento del carico, è possibile selezionare un server di elaborazione diverso nel portale di Site Recovery e spostare in tale server la replica di uno o più computer per il bilanciamento del carico manuale. In questo caso, i metadati dei computer di origine e di replica selezionati vengono spostati nel nuovo server di elaborazione. Il server di elaborazione originale rimane connesso al server vCenter. 

### Monitorare il server di elaborazione

Se un server di elaborazione è in stato critico, nel dashboard di Site Recovery verrà visualizzato un avviso di stato. È possibile fare clic sullo stato per aprire la scheda Eventi e quindi eseguire il drill-down a processi specifici nella scheda Processi.

### Modificare il server di elaborazione usato per la replica

1. Andare in **SERVER DI CONFIGURAZIONE** in **SERVER**
2. Fare clic sul nome del server di configurazione e passare a **Dettagli server**.
3. Nell'elenco **Server di elaborazione** fare clic su **Cambia server di elaborazione** accanto al server da modificare. ![Modificare il server di elaborazione 1](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS1.png)
4. Nella finestra di dialogo **Cambia server di elaborazione** selezionare il nuovo server in **Server di elaborazione di destinazione** e quindi selezionare le macchine virtuali da replicare nel nuovo server. Fare clic sull'icona delle informazioni accanto al nome del server per ottenere informazioni su di esso, inclusi lo spazio libero e la memoria usata. Per consentire di prendere le decisioni relative al carico, viene visualizzato lo spazio medio che sarà necessario per replicare ogni macchina virtuale selezionata nel nuovo server di elaborazione. ![Modificare il server di elaborazione 2](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS2.png)
5. Fare clic sul segno di spunta per avviare la replica nel nuovo server di elaborazione. Se si rimuovono tutte le macchine virtuali da un server di elaborazione in stato critico, per tale server non dovrebbe più essere visualizzato un avviso critico nel dashboard.


## Informazioni e comunicazioni sul software di terze parti

Non tradurre o localizzare

Il software e il firmware eseguito nel prodotto o nel servizio Microsoft si basa su materiale incorporato dai progetti elencati di seguito (collettivamente, "Codice di terze parti"). Microsoft non è l'autore originale del Codice di terze parti. Le informazioni sul copyright e le condizioni di licenza originali in base alle quali Microsoft ha ricevuto il Codice di terze parti sono definite di seguito.

Le informazioni nella sezione A riguardano i componenti del Codice di terze parti dai progetti elencati di seguito. Le licenze e le informazioni vengono fornite a scopo esclusivamente informativo. Il Codice di terze parti viene riconcesso in licenza all'utente da Microsoft in base alle condizioni di licenza del software definite da Microsoft per il prodotto o il servizio Microsoft.

Le informazioni nella sezione B riguardano i componenti del Codice di terze parti resi disponibili all'utente da Microsoft in base alle condizioni di licenza originali.

Il file completo è disponibile nell'[Area download Microsoft](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft si riserva tutti i diritti non espressamente disciplinati dal presente documento, sia tacitamente, per preclusione o per qualsivoglia altro motivo.

<!---HONumber=Oct15_HO2-->