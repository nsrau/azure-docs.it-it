<properties
	pageTitle="Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery | Microsoft Azure"
	description="Questo articolo illustra come distribuire Azure Site Recovery per orchestrare la replica, il failover e il ripristino di macchine virtuali VMware locali e server fisici Windows/Linux in Azure."
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
	ms.date="03/15/2016"
	ms.author="raynew"/>

# Eseguire la replica di macchine virtuali VMware e server fisici in Azure con Azure Site Recovery

> [AZURE.SELECTOR]
- [Portale di Azure](site-recovery-vmware-to-azure.md)
- [Portale classico](site-recovery-vmware-to-azure-classic.md)
- [Portale classico (legacy)](site-recovery-vmware-to-azure-classic-legacy.md)


Il servizio Azure Site Recovery favorisce l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR) orchestrando le operazioni di replica, failover e ripristino delle macchine virtuali e dei server fisici. È possibile replicare i computer in Azure o in un data center locale secondario. Per una panoramica rapida, vedere [Che cos'è Azure Site Recovery?](site-recovery-overview.md).

## Panoramica

Questo articolo illustra come:

- **Eseguire la replica di macchine virtuali VMware in Azure**: distribuire Site Recovery per coordinare la replica, il failover e il ripristino di macchine virtuali VMware locali nell'archiviazione di Azure.
- **Eseguire la replica di server fisici in Azure**: distribuire Azure Site Recovery per coordinare la replica, il failover e il ripristino di server fisici Windows e Linux locali in Azure.

>[AZURE.NOTE] Questo articolo illustra come eseguire la replica in Azure. Per eseguire la replica di VM VMware o di server fisici Windows/Linux in un data center secondario, seguire le istruzioni contenute in [questo articolo](site-recovery-vmware-to-vmware.md).

Per inviare commenti o domande è possibile usare la parte inferiore di questo articolo oppure il [forum sui Servizi di ripristino di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Distribuzione avanzata

Questo articolo contiene istruzioni relative alla distribuzione avanzata nel portale di Azure classico. Si consiglia di usare questa versione per tutte le nuove distribuzioni. Se è già stata eseguita la distribuzione con la versione legacy precedente è consigliabile eseguire la migrazione alla nuova versione. [Altre](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) informazioni sulla migrazione.

La distribuzione avanzata è un aggiornamento importante. Di seguito è riportato un riepilogo dei miglioramenti apportati:

- **Nessuna VM dell'infrastruttura in Azure**: i dati vengono replicati direttamente in un account di archiviazione di Azure. Per la replica e il failover non è inoltre presente alcuna configurazione di VM dell'infrastruttura (server di configurazione, server di destinazione master), necessaria invece nella distribuzione legacy.  
- **Installazione unificata**: un'unica installazione garantisce la scalabilità per i componenti locali e ne semplifica la configurazione.
- **Distribuzione protetta**: tutto il traffico viene crittografato e le comunicazioni relative alla gestione della replica vengono inviate tramite HTTPS 443.
- **Punti di ripristino**: supporto per punti di ripristino per l'arresto anomalo del sistema e coerenti con l'applicazione per ambienti Windows e Linux. Sono supportate configurazioni coerenti con VM singole e multiple.
- **Failover di test**: supporto per il failover di test che non comporta interruzioni del servizio in Azure, senza incidere sulla produzione o sospendere la replica.
- **Failover non pianificato**: supporto per il failover non pianificato in Azure con un'opzione avanzata che consente di arrestare automaticamente le VM prima del failover.
- **Failback**: failback integrato che consente di replicare solo le modifiche differenziali al sito locale.
- **vSphere 6.0**: supporto limitato per le distribuzioni di VMware vSphere 6.0.


## Azure Site Recovery contribuisce alla protezione di macchine virtuali e server fisici


- Gli amministratori VMware possono configurare in Azure la protezione esterna di carichi di lavoro aziendali e applicazioni in esecuzione su macchine virtuali VMware. I responsabili della gestione dei server possono replicare i server fisici Windows e Linux locali in Azure.
- La console Azure Site Recovery riunisce in un'unica posizione e semplifica le operazioni di configurazione e gestione dei processi di replica, failover e ripristino.
- Se si esegue la replica di macchine virtuali VMware gestite da un server vCenter, Site Recovery può rilevare automaticamente le macchine virtuali. Se i computer si trovano in un host ESXi, Site Recovery rileva i computer nell'host.
- È possibile eseguire facilmente il failover dall'infrastruttura locale in Azure ed eseguire il failback (ripristino) da Azure ai server delle macchine virtuali VMware nel sito locale.
- È possibile configurare piani di ripristino che raggruppano i carichi di lavoro delle applicazioni a più livelli su più computer. Site Recovery permette di eseguire il failover dei piani di ripristino e assicura la coerenza di più macchine virtuali, in modo che le macchine virtuali che eseguono gli stessi carichi di lavoro possano essere ripristinate insieme in un punto dati coerente.

## Architettura dello scenario

Componenti dello scenario:

- **Server di gestione locale**: il server di gestione esegue i componenti di Site Recovery seguenti.
	- **Server di configurazione**: coordina le comunicazioni e gestisce i processi di ripristino e replica dei dati.
	- **Server di elaborazione**: agisce come un gateway di replica. Riceve i dati da computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e invia i dati di replica all'archiviazione di Azure. Gestisce anche l'installazione push del servizio Mobility nei computer protetti ed esegue l'individuazione automatica delle macchine virtuali VMware.
	- **Server di destinazione master**: gestisce i dati di replica durante il failback da Azure. È anche possibile distribuire un server di gestione che agisce solamente come server di elaborazione per ridimensionare la distribuzione.
- **Servizio Mobility**: questo componente viene distribuito in ogni computer, ovvero VM VMware o server fisico, di cui si vuole eseguire la replica in Azure. Acquisisce le scritture di dati nel computer e le inoltra al server di elaborazione.
- **Azure**: non è necessario creare VM di Azure per gestire la replica e il failover. Il servizio Azure Site Recovery si occupa della gestione dati, che vengono replicati direttamente nell'archiviazione di Azure. Le macchine virtuali di Azure replicate vengono riattivate automaticamente solo quando si verifica il failover in Azure. Tuttavia, per eseguire il failback da Azure al sito locale è necessario configurare una macchina virtuale di Azure che agisca come server di elaborazione.


Il diagramma seguente mostra l'interazione tra questi componenti.

![architettura](./media/site-recovery-vmware-to-azure-classic/architecture.png)

## Pianificazione della capacità

Quando si pianifica la capacità, è necessario considerare quanto segue:

- **Ambiente di origine**: pianificazione della capacità o requisiti del computer di origine e dell'infrastruttura VMware.
- **Server di gestione**: pianificazione dei server di gestione locali che eseguono i componenti di Site Recovery.
- **Larghezza di banda di rete dall'origine alla destinazione**: pianificazione della larghezza di banda di rete necessaria per la replica tra l'origine e Azure.

### Considerazioni relative all'ambiente di origine

- **Frequenza di modifica giornaliera massima**: un computer protetto può usare un solo server di elaborazione e un singolo server di elaborazione può gestire fino a 2 TB di modifica dei dati al giorno. La massima frequenza di modifica dei dati giornaliera supportata per una macchina protetta è quindi di 2 TB.
- **Velocità effettiva massima**: una macchina replicata può appartenere a un account di archiviazione in Azure. Un account di archiviazione standard può gestire fino a 20.000 richieste al secondo ed è consigliabile fare in modo che il numero di IOPS in una macchina di origine rimanga 20.000. Se, ad esempio, si ha un computer di origine con 5 dischi e ogni disco genera 120 IOPS (con una dimensione pari a 8.000) nell'origine, il computer rientra nel limite di Azure pari a 500 IOPS per disco. Il numero di account di archiviazione richiesto è uguale al numero di IOPS di origine totali diviso 20.000.


### Considerazioni relative al server di elaborazione

Il server di gestione esegue i componenti di Site Recovery che gestiscono l'ottimizzazione, la replica e la gestione dati. Deve poter gestire la capacità della frequenza di modifica giornaliera in tutti i carichi di lavoro in esecuzione nei computer protetti e avere a disposizione una larghezza di banda sufficiente per eseguire la replica continua dei dati nell'archiviazione di Azure. In particolare:

- Il serve di elaborazione riceve i dati di replica da computer protetti e li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia prima di inviarli ad Azure. Il server di gestione deve avere risorse sufficienti per eseguire queste attività.
- Il server di elaborazione usa una cache basata su disco. È consigliabile usare un disco a parte per la cache di almeno 600 GB per gestire le modifiche ai dati archiviate in caso di colli di bottiglia o interruzioni della rete. Durante la distribuzione è possibile configurare la cache in qualsiasi unità che abbia almeno 5 GB di spazio di archiviazione disponibile ma 600 GB è il minimo consigliato.
- Come procedura consigliata, il server di gestione dovrebbe trovarsi nella stessa rete e nello stesso segmento LAN dei computer da proteggere. Può trovarsi in una rete diversa, ma i computer da proteggere devono avere la visibilità di rete L3 per il server.

Le dimensioni consigliate per il server di gestione sono riepilogate nella tabella seguente.

**CPU del server di gestione** | **Memoria** | **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core a 2,5 GHz) | 16 GB | 300 GB | 500 GB o inferiore | Distribuire un server di gestione con queste impostazioni per eseguire la replica di meno di 100 computer.
12 vCPU (2 socket * 6 core a 2,5 GHz) | 18 GB | 600 GB | Da 500 GB a 1 TB | Distribuire un server di gestione con queste impostazioni per eseguire la replica di 100-150 computer.
16 vCPU (2 socket * 8 core a 2,5 GHz) | 32 GB | 1 TB | Da 1 TB a 2 TB | Distribuire un server di gestione con queste impostazioni per eseguire la replica di 150-200 computer.
Distribuire un altro server di elaborazione | | | Superiore a 2 TB | Distribuire server di elaborazione aggiuntivi se si esegue la replica di più di 200 computer o la frequenza di modifica dei dati giornaliera è superiore a 2 TB.

Dove:

- Ogni computer di origine è configurato con 3 dischi da 100 GB.
- La risorsa di archiviazione di benchmarking usata per le misurazioni del disco della cache è di 8 unità SAS a 10.000 RPM con RAID 10.

### Larghezza di banda dall'origine alla destinazione
Assicurarsi di calcolare la larghezza di banda necessaria per la replica iniziale e la replica differenziale usando lo [strumento Capacity Planner](site-recovery-capacity-planner.md).

#### Limitazione della larghezza di banda usata per la replica

Il traffico VMware replicato in Azure passa per un server di elaborazione specifico. Per limitare la larghezza di banda disponibile per la replica di Site Recovery in tale server, è possibile procedere come indicato di seguito:

1. Aprire lo snap-in di MMC di Backup di Microsoft Azure nel server di gestione principale o in un server di gestione che esegue server di elaborazione aggiuntivi di cui è stato eseguito il provisioning. Per impostazione predefinita viene creato un collegamento sul desktop a Backup di Microsoft Azure, che è disponibile anche in: C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. Nello snap-in fare clic su **Modifica proprietà**.

	![Limitazione della larghezza di banda](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Nella scheda **Limitazione** specificare la larghezza di banda da poter usare per la replica di Site Recovery e la programmazione applicabile.

	![Limitazione della larghezza di banda](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

È anche possibile impostare la limitazione usando PowerShell. Ad esempio:

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### Ottimizzazione dell'utilizzo della larghezza di banda
Per aumentare la larghezza di banda utilizzata per la replica da Azure Site Recovery è necessario modificare una chiave del Registro di sistema.

La chiave riportata di seguito controlla il numero di thread per disco di replica usati durante la replica:

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 In una rete con provisioning eccessivo è necessario modificare i valori predefiniti di questa chiave del Registro di sistema. Il numero massimo supportato è 32.


[Altre informazioni](site-recovery-capacity-planner.md) sulla pianificazione dettagliata della capacità.

### Server di elaborazione aggiuntivi

Se è necessario proteggere più di 200 computer o la frequenza di modifica giornaliera è superiore a 2 TB è possibile aggiungere altri server per gestire il carico. Per la scalabilità orizzontale è possibile:

- Aumentare il numero di server di gestione. Ad esempio, è possibile proteggere fino a 400 computer con due server di gestione.
- Aggiungere altri server di elaborazione e usarli per gestire il traffico al posto del server di gestione o in aggiunta a tale server.

Questa tabella descrive uno scenario in cui:

- Il server di gestione originario viene configurato per l'uso esclusivo come server di configurazione.
- Viene configurato un server di elaborazione aggiuntivo.
- Le macchine virtuali protette vengono configurate per l'uso del server di elaborazione aggiuntivo.
- Ogni computer di origine protetto è configurato con tre dischi da 100 GB.

**Server di gestione originario**<br/><br/>(server di configurazione) | **Server di elaborazione aggiuntivo**| **Dimensione disco cache** | **Frequenza di modifica dei dati** | **Computer protetti**
--- | --- | --- | --- | ---
8 vCPU (2 socket * 4 core a 2,5 GHz), 16 GB di memoria | 4 vCPU (2 socket * 2 core a 2,5 GHz), 8 GB di memoria | 300 GB | 250 GB o inferiore | È possibile eseguire la replica di un massimo di 85 computer.
8 vCPU (2 socket * 4 core a 2,5 GHz), 16 GB di memoria | 8 vCPU (2 socket * 4 core a 2,5 GHz), 12 GB di memoria | 600 GB | Da 250 GB a 1 TB | È possibile eseguire la replica di 85-150 computer.
12 vCPU (2 socket * 6 core a 2,5 GHz), 18 GB di memoria | 12 vCPU (2 socket * 6 core a 2,5 GHz), 24 GB di memoria | 1 TB | Da 1 TB a 2 TB | È possibile eseguire la replica di 150-225 computer.


Il modo in cui i server vengono adattati dipende dalle preferenze personali per il modello di scalabilità orizzontale o quello verticale. La scalabilità verticale si ottiene distribuendo pochi server di gestione high-end e server di elaborazione. La scalabilità orizzontale si ottiene distribuendo più server con minori risorse. Ad esempio, per proteggere 220 computer è possibile eseguire una di queste operazioni:

- Configurare il server di gestione originario con 12 vCPU e 18 GB di memoria e un server di elaborazione aggiuntivo con 12 vCPU e 24 GB di memoria. Configurare quindi i computer protetti perché usino soltanto il server di elaborazione aggiuntivo.
- Configurare due server di gestione, 2 x 8 vCPU e 16 GB di memoria RAM, e due server di elaborazione aggiuntivi, 1 x 8 vCPU e 1 x 4 vCPU per gestire 135 + 85 (220) computer, e configurare i computer protetti perché usino soltanto i server di elaborazione aggiuntivi.


[Seguire queste istruzioni](#deploy-additional-process-servers) per configurare un server di elaborazione aggiuntivo.

## Prima di iniziare la distribuzione

La tabella seguente contiene un riepilogo dei prerequisiti per la distribuzione di questo scenario.


### Prerequisiti di Azure

**Prerequisito** | **Dettagli**
--- | ---
**Account di Azure**| È necessario un account [Microsoft Azure](https://azure.microsoft.com/). È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi di Site Recovery.
**Archiviazione di Azure** | Per archiviare i dati replicati, sarà necessario un account di archiviazione di Azure. I dati replicati vengono memorizzati nell'archiviazione di Azure e le macchine virtuali di Azure vengono attivate quando si verifica il failover. <br/><br/>È necessario un [account di archiviazione con ridondanza geografica Standard](../storage/storage-redundancy.md#geo-redundant-storage). L'account deve trovarsi nella stessa area del servizio Azure Site Recovery e deve essere associato alla stessa sottoscrizione. Si noti che la replica in account di archiviazione Premium non è attualmente supportata e non deve essere usata.<br/><br/>Non è supportato lo spostamento degli account di archiviazione creati con il [nuovo portale di Azure](../storage/storage-create-storage-account.md) tra gruppi di risorse.[Altre informazioni](../storage/storage-introduction.md) sull'Archiviazione di Azure.<br/><br/>
**Rete di Azure** | È necessaria una rete virtuale di Azure a cui le macchine virtuali di Azure possano connettersi quando si verifica il failover. La rete virtuale di Azure deve trovarsi nella stessa area dell'insieme di credenziali di Site Recovery.<br/><br/>Si noti che per eseguire il failback dopo il failover in Azure è necessaria una connessione VPN o Azure ExpressRoute configurata dalla rete di Azure al sito locale.


### Prerequisiti locali

**Prerequisito** | **Dettagli**
--- | ---
**Server di gestione** | È necessario un server Windows 2012 R2 locale in esecuzione in una macchina virtuale o un server fisico. Tutti i componenti locali di Site Recovery vengono installati in questo server di gestione.<br/><br/> È consigliabile distribuire il server come una VM VMware a disponibilità elevata. Il failback da Azure al sito locale deve essere sempre eseguito in macchine virtuali VMware indipendentemente dal fatto che il failover sia stato eseguito in macchine virtuali o in server fisici. Se non si configura il server di gestione come una macchina virtuale VMware, è necessario configurare un server di destinazione master distinto come una macchina virtuale VMware perché possa ricevere il traffico di failback.<br/><br/>Il server non deve essere un controller di dominio.<br/><br/>Il server deve avere un indirizzo IP statico.<br/><br/>Il nome host del server deve essere composto al massimo da 15 caratteri.<br/><br/>Le impostazioni locali del sistema operativo devono essere in lingua inglese.<br/><br/>Per il server di gestione è necessario l'accesso a Internet.<br/><br/>È necessario l'accesso in uscita dal server con le caratteristiche seguenti: accesso temporaneo su HTTP 80 durante la configurazione dei componenti di Site Recovery per il download di MySQL, accesso continuo in uscita su HTTPS 443 per la gestione della replica, accesso continuo in uscita su HTTPS 9443 per il traffico di replica (è possibile modificare questa porta).<br/><br/> Verificare l'accessibilità di questi URL dal server di gestione: <br/>- *.hypervrecoverymanager.windowsazure.com<br/>- *.accesscontrol.windows.net<br/>- *.backup.windowsazure.com<br/>- *.blob.core.windows.net<br/>- *.store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Se sono presenti regole firewall basate sull'indirizzo IP, verificare che consentano la comunicazione con Azure. È necessario consentire gli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653) e il protocollo HTTPS (433). e aggiungere all'elenco elementi consentiti gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione e per gli Stati Uniti occidentali. Il download di MySQL è disponibile all'URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi").
**VMware vCenter e host ESXi**: | Sono necessari uno o più hypervisor vMware vSphere ESX/ESXi che gestiscono le macchine virtuali VMware ed eseguono ESX/ESXi versione 6.0, 5.5 o 5.1 con gli aggiornamenti più recenti.<br/><br/> Si consiglia di distribuire un server VMware vCenter per gestire gli host ESXi. Il server deve eseguire vCenter versione 6.0 o 5.5 con gli aggiornamenti più recenti.<br/><br/>Si noti che Site Recovery non supporta le nuove funzionalità di vCenter e vSphere 6.0 come cross-vCenter vMotion, Virtual Volumes e Storage DRS. Site Recovery supporta unicamente le funzionalità disponibili anche nella versione 5.5.
**Computer protetti**: | **AZURE**<br/><br/>I computer da proteggere devono essere conformi ai [prerequisiti di Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) per la creazione di macchine virtuali di Azure.<br><br/>Per connettersi alle macchine virtuali di Azure dopo il failover, è necessario abilitare le connessioni Desktop remoto nel firewall locale.<br/><br/>La capacità dei singoli dischi nei computer protetti non deve essere superiore a 1023 GB. Una macchina virtuale può avere fino a 64 dischi (quindi fino a 64 TB). Se si hanno dischi di dimensioni superiori a 1 TB è consigliabile usare la replica di database, ad esempio SQL Server AlwaysOn oppure Oracle Data Guard.<br/><br/>I cluster guest in dischi condivisi non sono supportati. Se è presente una distribuzione cluster è consigliabile usare la replica di database, ad esempio SQL Server AlwaysOn oppure Oracle Data Guard.<br/><br/>L'avvio in modalità UEFI (Unified Extensible Firmware Interface)/EFI (Extensible Firmware Interface) non è supportato.<br/><br/>I nomi dei computer devono essere composti da un minimo di 1 e un massimo di 63 caratteri (lettere, numeri e trattini). Il nome deve iniziare e terminare con una lettera o un numero. Quando un computer è protetto è possibile modificare il nome di Azure.<br/><br/>**MACCHINE VIRTUALI VMware**<br/><br>È necessario installare VMware vSphere PowerCLI 6.0 nel server di gestione (server di configurazione).<br/><br/>Gli strumenti VMware devono essere installati e in esecuzione nelle macchine virtuali VMware da proteggere.<br/><br/>Se nella macchina virtuale di origine è presente un gruppo NIC, dopo il failover in Azure viene convertito in una singola scheda di interfaccia di rete.<br/><br/>Se nella macchina virtuale protetta è presente un disco iSCSI, Site Recovery lo converte in un file VHD quando la macchina virtuale esegue il failover in Azure. Se la destinazione iSCSI può essere raggiunta dalla macchina virtuale di Azure, questa si connette alla destinazione iSCSI. In tal modo sono visibili due dischi: il disco rigido virtuale nella macchina virtuale di Azure e il disco iSCSI di origine. In questo caso è necessario disconnettere la destinazione iSCSI visualizzata nella macchina virtuale di Azure di cui è stato eseguito il failover.<br/><br/>[Altre informazioni](#vmware-permissions-for-vcenter-access) sulle autorizzazioni utente VMware necessarie per Site Recovery.<br/><br/> **COMPUTER WINDOWS SERVER (in macchine virtuali VMware o server fisici)**<br/><br/>Nel server deve essere in esecuzione un sistema operativo a 64 bit supportato: Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2 con SP1 o versioni successive.<br/><br/>Il sistema operativo deve essere installato nell'unità C:\\ e il disco del sistema operativo deve essere un disco di base di Windows, ovvero il sistema operativo non deve essere installato in un disco dinamico di Windows.<br/><br/>Nei computer Windows Server 2008 R2 è necessario che sia installato .NET Framework 3.5.1.<br/><br/>È necessario specificare un account amministratore, che deve essere un amministratore locale nel computer Windows, per l'installazione push del servizio Mobility nei server Windows. Se l'account specificato non è un account di dominio, si dovrà disabilitare il Controllo dell'accesso utente remoto nel computer locale. [Altre informazioni](#install-the-mobility-service-with-push-installation).<br/><br/>Site Recovery supporta macchine virtuali con disco RDM. Se la macchina virtuale di origine e il disco RDM sono disponibili, durante il failback Site Recovery riutilizza il disco RDM. Se non sono disponibili, durante il failback Site Recovery crea un nuovo file VMDK per ogni disco.<br/><br/>**COMPUTER LINUX**<br/><br/>È necessario un sistema operativo a 64 bit supportato: Red Hat Enterprise Linux 6.7, Centos 6.5, 6.6 o 6.7, Oracle Enterprise Linux 6.4 o 6.5 con il kernel compatibile Red Hat o Unbreakable Enterprise Kernel versione 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>I file /etc/hosts nei computer protetti devono contenere voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete. <br/><br/>Per connettersi dopo il failover a una macchina virtuale di Azure che esegue Linux usando un client Secure Shell (SSH), verificare che il servizio Secure Shell nel computer protetto sia impostato per l'avvio automatico all'avvio del sistema e che le regole firewall consentano la connessione SSH al computer.<br/><br/>La protezione può essere abilitata soltanto per i computer Linux con le risorse di archiviazione seguenti: file system EXT3, EXT4, ReiserFS o XFS, software per percorsi multipli Device Mapper, gestore dei volumi LVM2. I server fisici con archiviazione del controller HP CCISS non sono supportati. Il file system ReiserFS è supportato solo su SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery supporta macchine virtuali con disco RDM. Durante il failback per Linux, Site Recovery non riutilizza il disco RDM, ma crea un nuovo file VMDK per ogni disco RDM corrispondente.

Solo per VM Linux: assicurarsi di configurare l'impostazione disk.enableUUID=true nei parametri di configurazione della VM in VMware. Se questa riga non esiste, aggiungerla perché è necessaria per fornire un valore UUID coerente al file VMDK in modo che venga installato correttamente. Si noti anche che, senza questa impostazione, il failback provocherà un download completo anche se la VM è disponibile in locale. L'aggiunta di questa impostazione assicurerà che solo le modifiche differenziali vengano trasferite durante il failback.

## Passaggio 1: Creare un insieme di credenziali

1. Accedere al [portale di gestione](https://manage.windowsazure.com/).
2. Espandere **Servizi dati** > **Servizi di ripristino** e fare clic su **Insieme di credenziali di Site Recovery**.
3. Fare clic su **Crea nuovo** > **Creazione rapida**.
4. In **Name** immettere un nome descrittivo per identificare l'insieme di credenziali.
5. In **Region** selezionare l'area geografica per l'insieme di credenziali. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Fare clic su **Crea insieme di credenziali**. ![Nuovo insieme di credenziali](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Controllare la barra di stato per verificare che l'insieme di credenziali sia stato creato correttamente. L'insieme di credenziali verrà elencato come **Attivo** nella pagina principale di **Servizi di ripristino**.

## Passaggio 2: Configurare una rete di Azure

Configurare una rete di Azure in modo che le macchine virtuali di Azure siano connesse a una rete dopo il failover e in modo che il failback al sito locale possa funzionare come previsto.

1. Nel portale di Azure passare a **Crea rete virtuale** e specificare il nome della rete, l'intervallo di indirizzi IP e il nome della subnet.
2. Per eseguire il failback è necessario aggiungere una VPN o ExpressRoute alla rete. La VPN ed ExpressRoute possono essere aggiunti alla rete anche dopo il failover.

[Altre informazioni](../virtual-network/virtual-networks-overview.md) sulle reti di Azure.

## Passaggio 3: Installare i componenti VMware

Per eseguire la replica di macchine virtuali VMware, installare i componenti VMware indicati di seguito nel server di gestione:

1. [Scaricare](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) e installare VMware vSphere PowerCLI 6.0.
2. Riavviare il server.


## Passaggio 4: Scaricare una chiave di registrazione dell'insieme di credenziali

1. Dal server di gestione aprire la console Azure Site Recovery in Azure. Nella pagina **Servizi di ripristino** fare clic sull'insieme di credenziali per aprire la pagina Avvio rapido. La pagina Avvio rapido può anche essere aperta in qualsiasi momento tramite l'icona.

	![Quick Start Icon](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Nella pagina **Avvio rapido** fare clic su **Preparare le risorse di destinazione** > **Scaricare una chiave di registrazione**. Il file di registrazione viene generato automaticamente. È valido 5 giorni dopo essere stato generato.


## Passaggio 5: Installare il server di gestione
> [AZURE.TIP] Assicurarsi che questi URL siano accessibili dal server di gestione:
>
- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Dalla pagina **Avvio rapido** scaricare nel server il file di installazione unificata.
2. Eseguire il file di installazione per avviare l'installazione guidata unificata di Azure Site Recovery.
3. In **Prima di iniziare** selezionare l'opzione **Installare il server di configurazione e il server di elaborazione**. A seconda della dimensione della distribuzione, in seguito potrebbero rendersi necessari server di elaborazione aggiuntivi, ma non quando si configura la distribuzione per la prima volta.

	![Prima di iniziare](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)

4. In **Third-Party Software Installation** fare clic su **I Accept** per scaricare e installare MySQL.

	![Software di terze parti](./media/site-recovery-vmware-to-azure-classic/combined-wiz2.png)

5. In **Impostazioni Internet** specificare in che modo il provider che verrà installato nel server dovrà connettersi ad Azure Site Recovery tramite Internet.

	- Per fare in modo che il provider si connetta direttamente, selezionare **Connetti direttamente senza un proxy**.
	- Per connettersi al proxy attualmente configurato nel server, selezionare **Connetti con le impostazioni proxy esistenti**.
	- Se per il proxy esistente è necessaria l'autenticazione o si vuole usare un proxy personalizzato per la connessione del provider, selezionare **Connetti con le impostazioni proxy personalizzate**.
	- Se si usa un proxy personalizzato è necessario specificare l'indirizzo, la porta e le credenziali.
	- L'eventuale proxy usato deve permettere di accedere agli URL seguenti:

	![Firewall](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

7. In **Controllo dei prerequisiti** il programma di installazione esegue un controllo dei prerequisiti nel server.

	![Prerequisiti](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

>[AZURE.WARNING] Se viene visualizzato un avviso per la verifica preliminare **Sincronizzazione ora globale**, verificare che l'ora del clock di sistema corrisponda al fuso orario.

![TimeSyncIssue](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. In **Configurazione MySQL** creare le credenziali per l'accesso all'istanza del server MySQL. È possibile usare questi caratteri speciali: '\_', '!', '@', '$', ' \\', '%'.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

9. In **Dettagli ambiente** specificare se si vuole eseguire la replica di macchine virtuali VMware. In caso affermativo, il programma di installazione verifica se è installato PowerCLI 6.0.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

10. In **Percorso di installazione** specificare il percorso di installazione dei file binari e di archiviazione della cache. Il disco della cache dovrebbe avere almeno 600 GB di spazio disponibile.

	![Posizione di installazione](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

11. In **Selezione rete** specificare il listener, ovvero la scheda di rete e la porta SSL, in cui il server dovrà inviare e ricevere i dati di replica. È possibile modificare la porta predefinita (9443). Oltre a questa porta, verrà aperta la porta 443 nel server per l'invio e la ricezione di informazioni sull'orchestrazione della replica. La porta 443 non deve essere usata per i dati di replica.


	![Selezione della rete](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

12. In **Registrazione** cercare e selezionare la chiave di registrazione scaricata dall'insieme di credenziali.

	![Registrazione](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)

13.  Esaminare le informazioni nella pagina **Riepilogo**.

	![Riepilogo](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)
>[AZURE.WARNING] È necessario configurare il proxy dell'agente di Servizi di ripristino di Microsoft Azure. Dopo aver completato l'installazione, avviare un'applicazione denominata "Microsoft Azure Recovery Services Shell" dal menu di avvio di Windows. Nella finestra di comando visualizzata eseguire il set di comandi seguente per configurare le impostazioni del server proxy.
>
	$pwd = ConvertTo-SecureString -String ProxyUserPassword Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\\username -ProxyPassword $pwd net stop obengine net start obengine



### Eseguire l'installazione dalla riga di comando

È anche possibile eseguire l'installazione guidata unificata dalla riga di comando, come indicato di seguito:

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Dove:

- /ServerMode. Obbligatorio. Indica al programma di installazione se installare il server di configurazione e il server di elaborazione o solo il server di elaborazione, usato per installare i server di elaborazione aggiuntivi. Valori di input: CS, PS.
- InstallDrive. Obbligatorio. Specifica la cartella in cui sono installati i componenti.
- /MySQLCredFilePath. Obbligatorio. Specifica il percorso di un file in cui sono archiviate le credenziali del server MySQL. È disponibile un modello per la creazione del file.
- /VaultCredFilePath. Obbligatorio. Percorso del file di credenziali dell'insieme di credenziali.
- /EnvType. Obbligatorio. Tipo di installazione. Valori: VMware, NonVMware.
- /PSIP e /CSIP. Obbligatorio. Indirizzo IP del server di elaborazione e del server di configurazione.
- /PassphraseFilePath. Obbligatorio. Percorso del file della passphrase.
- /ByPassProxy. Facoltativo. Specifica che il server di gestione si connette ad Azure senza un proxy.
- /ProxySettingsFilePath. Facoltativo. Specifica le impostazioni per un proxy personalizzato, ovvero un proxy predefinito nel server che richiede l'autenticazione o un proxy personalizzato.




## Passaggio 6: Impostare le credenziali per il server vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Il server di elaborazione può rilevare automaticamente le macchine virtuali VMware gestite da un server vCenter. Per l'individuazione automatica con Site Recovery sono necessari un account e credenziali di accesso al server vCenter. Questa istruzione non si applica se si esegue la replica solo di server fisici.

Procedere come segue:

1. Nel server vCenter creare un ruolo, **Azure\_Site\_Recovery**, a livello di vCenter con le [autorizzazioni necessarie](#vmware-permissions-for-vcenter-access).
2. Assegnare il ruolo **Azure\_Site\_Recovery** a un utente di vCenter.

	>[AZURE.NOTE] Un account utente di vCenter con un ruolo di sola lettura può eseguire il failover senza arrestare le macchine di origine protette. Per arrestare tali macchine è necessario il ruolo Azure\_Site\_Recovery. Se si sta eseguendo la sola migrazione delle macchine virtuali da VMware in Azure e non è necessario il failback, il ruolo di sola lettura è sufficiente.

3. Per aggiungere l'account, aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\\home\\svsystems\\bin.
2. Nella scheda **Gestisci account** fare clic su **Aggiungi account**.

	![Aggiungere un account](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. In **Dettagli dell'account** aggiungere le credenziali da usare per accedere al server vCenter. Potrebbero trascorrere più di 15 minuti prima che il nome dell'account venga visualizzato nel portale. Per aggiornarlo immediatamente, fare clic su Aggiorna nella scheda **Server di configurazione**.

	![Dettagli](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## Passaggio 7: Aggiungere server vCenter e host ESXi

Se si esegue la replica di macchine virtuali VMware è necessario aggiungere un server vCenter (o un host ESXi).

1. Nella scheda **Server** > **Server di configurazione** selezionare il server di configurazione e fare clic su **Aggiungi server vCenter**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Aggiungere i dettagli del server vCenter o dell'host ESXi, il nome dell'account specificato per accedere al server vCenter nel passaggio precedente e il server di elaborazione da usare per trovare le macchine virtuali VMware gestite dal server vCenter. Si noti che il server vCenter e l'host ESXi devono trovarsi nella stessa rete del server in cui è installato il server di elaborazione.

	>[AZURE.NOTE] Se si aggiunge il server vCenter o l'host ESXi con un account che non ha privilegi di amministratore per il server vCenter o il server host, assicurarsi che questi privilegi siano abilitati per gli account vCenter o ESXi: Datacenter, Datastore, Folder, Host, Network, Resource, Virtual machine, vSphere Distributed Switch. Il server vCenter deve avere anche il privilegio Storage views.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Al termine dell'individuazione, il server vCenter sarà elencato nella scheda **Server di configurazione**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)


## Passaggio 8: Creare un gruppo di protezione

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


I gruppi di protezione sono raggruppamenti logici di macchine virtuali che si vuole proteggere usando le stesse impostazioni di protezione. Se si applicano impostazioni di protezione a un gruppo, queste vengono automaticamente applicate a tutte le macchine virtuali/macchine fisiche aggiunte al gruppo.

1. Aprire **Elementi protetti** > **Gruppo di protezione** e fare clic per aggiungere un gruppo di protezione.

	![Creare un gruppo di protezione](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Nella pagina **Specificare le impostazioni del gruppo di protezione** immettere un nome per il gruppo e in **Da** selezionare il server di configurazione in cui creare il gruppo. La **destinazione** è Azure.

	![Impostazioni del gruppo di protezione](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Nella pagina **Specifica le impostazioni di replica** configurare le impostazioni di replica da usare per tutti i computer nel gruppo.

	![Replica del gruppo di protezione](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

	- **Coerenza di più macchine virtuali**: se si attiva questa opzione, vengono creati punti di ripristino coerenti con l'applicazione condivisi tra i computer nel gruppo di protezione. Questa impostazione è importante quando tutti i computer nel gruppo di protezione eseguono lo stesso carico di lavoro. Tutti i computer saranno ripristinati nello stesso punto dati. Questa opzione è disponibile sia per la replica di macchine virtuali VMware che per la replica di server fisici Windows/Linux.
	- **Soglia RPO**: imposta un valore RPO. Se la replica della protezione dati continua supera la soglia RPO configurata, vengono generati avvisi.
	- **Conservazione del punto di ripristino**: specifica l'intervallo di conservazione. I computer protetti possono essere ripristinati in qualsiasi punto all'interno di questo intervallo.
	- **Frequenza di snapshot coerenti con l'applicazione**: specifica con quale frequenza verranno creati i punti di ripristino contenenti snapshot coerenti con l'applicazione.

Facendo clic sul segno di spunta viene creato un gruppo di protezione con il nome specificato. Viene creato un secondo gruppo di protezione denominato <nome-gruppo-protezione-Failback>. Questo gruppo di protezione viene usato se si esegue il failback nel sito locale dopo il failover in Azure. È possibile monitorare i gruppi di protezione man mano che vengono creati nella pagina **Elementi protetti**.

## Passaggio 9: Installare il servizio Mobility

Per abilitare la protezione per le macchine virtuali e i server fisici è necessario prima di tutto installare il servizio Mobility. Questa operazione può essere eseguita in due modi:

- Eseguire il push e installare automaticamente il servizio in ogni computer dal server di elaborazione. Si noti che quando si aggiungono a un gruppo di protezione computer in cui è già in esecuzione una versione appropriata del servizio Mobility, l'installazione push non viene eseguita.
- Installare automaticamente il servizio usando il metodo push dell'organizzazione, ad esempio WSUS o System Center Configuration Manager. Assicurarsi di aver configurato il server di gestione prima di procedere.
- Eseguire manualmente l'installazione in ogni computer da proteggere. Assicurarsi di aver configurato il server di gestione prima di procedere.


### Installare il servizio Mobility con l'installazione push

Quando si aggiungono computer a un gruppo di protezione, il server di elaborazione effettua automaticamente il push e l'installazione del servizio Mobility in ogni computer.


#### Preparare il push automatico nei computer Windows

Di seguito viene illustrato come preparare i computer Windows in modo che il servizio Mobility possa essere installato automaticamente dal server di elaborazione.

1.  Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve avere privilegi di amministratore (locale o di dominio). Si noti che queste credenziali vengono usate solo per l'installazione push del servizio Mobility.

	>[AZURE.NOTE] Se non si usa un account di dominio, è necessario disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, aggiungere la voce DWORD LocalAccountTokenFilterPolicy con un valore di 1 nel Registro di sistema in HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System. Per aggiungere la voce del Registro di sistema da un'interfaccia della riga di comando, aprire il prompt dei comandi o PowerShell e immettere **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Nell'istanza di Windows Firewall del computer da proteggere selezionare **Consenti app o funzionalità attraverso Windows Firewall** e abilitare **Condivisione file e stampanti** e **Strumentazione gestione Windows**. Per i computer appartenenti a un dominio, è possibile configurare i criteri del firewall con un oggetto Criteri di gruppo.

	![Impostazioni del firewall](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Aggiungere l'account che è stato creato:

	- Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\\home\\svsystems\\bin.
	- Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
	- Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per l'aggiunta di un computer al gruppo di protezione sarà necessario fornire le credenziali.


#### Preparare il push automatico nei server Linux

1.	Verificare che il computer Linux da proteggere sia supportato come descritto in [Prerequisiti locali](#on-premises-prerequisites). Verificare la disponibilità della connettività di rete tra il computer da proteggere e il server di gestione che esegue il server di elaborazione.

2.	Creare un account utilizzabile dal server di elaborazione per accedere al computer. L'account deve essere un utente ROOT nel server Linux di origine. Si noti che queste credenziali vengono usate solo per l'installazione push del servizio Mobility.

	- Aprire **cspsconfigtool**. È disponibile come collegamento sul desktop e si trova nella cartella [PERCORSO DI INSTALLAZIONE]\\home\\svsystems\\bin.
	- Nella scheda **Gestisci account** fare clic su **Aggiungi account**.
	- Aggiungere l'account che è stato creato. Dopo aver aggiunto l'account, per l'aggiunta di un computer al gruppo di protezione sarà necessario fornire le credenziali.

3.	Assicurarsi che il file /etc/hosts nel server Linux di origine contenga le voci che eseguono il mapping del nome host locale agli indirizzi IP associati a tutte le schede di rete.
4.	Installare i pacchetti openssh, openssh-server, openssl più recenti nel computer da proteggere.
5.	Assicurarsi che SSH sia abilitato e in esecuzione sulla porta 22.
6.	Abilitare il sottosistema SFTP e l'autenticazione della password nel file sshd\_config, come segue:

	- Accedere come utente ROOT.
	- Nel file /etc/ssh/sshd\_config trovare la riga che inizia con PasswordAuthentication.
	- Rimuovere il commento dalla riga e modificare il valore da **no** a **yes**.
	- Trovare la riga che inizia con **Subsystem** e rimuovere il commento.

		![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### Installare manualmente il servizio Mobility

I programmi di installazione sono disponibili in C:\\Programmi (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository.

Sistema operativo di origine | File di installazione del servizio Mobility
--- | ---
Windows Server (solo 64 bit) | Microsoft-ASR\_UA\_9.*.0.0\_Windows\_* release.exe
CentOS 6.4, 6.5, 6.6 (solo 64 bit) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz SUSE Linux Enterprise Server 11 SP3 (solo 64 bit) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (solo 64 bit) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Installare manualmente in un server Windows


1. Scaricare ed eseguire il relativo programma di installazione.
2. In **Prima di iniziare** selezionare **Servizio Mobility**.

	![Servizio Mobility](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. In **Dettagli del server di configurazione** specificare l'indirizzo IP del server di gestione e la passphrase generata durante l'installazione dei componenti del server di gestione. Per recuperare la passphrase, eseguire **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** nel server di gestione.

	![Servizio Mobility](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. In **Percorso di installazione** lasciare il percorso predefinito e fare clic su **Avanti** per avviare l'installazione.
5. Monitorare lo stato dell'installazione in **Stato dell'installazione** e, se richiesto, riavviare il computer.

È possibile eseguire l'installazione anche dalla riga di comando:

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

Dove:

- /Role. Obbligatorio. Specifica se deve essere installato il servizio Mobility.
- /InstallLocation. Obbligatorio. Specifica dove installare il servizio.
- /PassphraseFilePath. Obbligatorio. Specifica la passphrase del server di configurazione.
- /LogFilePath. Obbligatorio. Specifica il percorso dei file di impostazione log.

#### Modificare l'indirizzo IP del server di gestione

Dopo aver eseguito la procedura guidata è possibile modificare l'indirizzo IP del server di gestione, come indicato di seguito:

1. Aprire il file hostconfig.exe (disponibile sul desktop).
2. Nella scheda **Globale** è possibile modificare l'indirizzo IP del server di gestione.

	>[AZURE.NOTE] Modificare unicamente l'indirizzo IP del server di gestione. Per le comunicazioni del server di gestione è necessario usare la porta 443 e l'opzione Use HTTPS deve essere abilitata. La passphrase non deve essere modificata.

	![Indirizzo IP del server di gestione](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### Installare manualmente in un server Linux

1. Copiare l'archivio TAR appropriato in base alla tabella precedente nel computer Linux da proteggere.
2. Aprire un programma shell ed estrarre l'archivio TAR compresso in un percorso locale eseguendo: `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Creare un file passphrase.txt nella directory locale in cui è stato estratto il contenuto dell'archivio TAR. A tale scopo, copiare la passphrase da C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase nel server di gestione e salvarla nel file passphrase.txt eseguendo *`echo <passphrase> >passphrase.txt`* nella shell.
4. Immettere *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* per installare il servizio Mobility.
5. Specificare l'indirizzo IP interno del server di gestione e assicurarsi che la porta 443 sia selezionata.

**È possibile eseguire l'installazione anche dalla riga di comando**:

1. Copiare la passphrase da C:\\Program Files (x86)\\InMage Systems\\private\\connection al server di gestione e salvarla come "passphrase.txt" nel server di gestione. Quindi eseguire questi comandi: In questo esempio l'indirizzo IP del server di gestione è 104.40.75.37 e la porta HTTPS deve essere la 443:

Per eseguire l'installazione in un server di produzione:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Per eseguire l'installazione nel server di destinazione master:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## Passaggio 10: Abilitare la protezione per un computer

Per abilitare la protezione, aggiungere macchine virtuali e server fisici a un gruppo di protezione. Prima di iniziare, tenere presente quanto segue per la protezione di macchine virtuali VMware:

- Le macchine virtuali VMware vengono rilevate ogni 15 minuti e possono essere necessari più di 15 minuti perché vengano visualizzate nel portale di Site Recovery dopo l'individuazione.
- Anche per le modifiche dell'ambiente nella macchina virtuale (ad esempio, l'installazione degli strumenti VMware) possono essere necessari oltre 15 minuti per l'aggiornamento in Site Recovery.
- È possibile controllare l'ora dell'ultima individuazione di macchine virtuali VMware nel campo **Ora ultimo contatto** per il server vCenter o l'host ESXi nella scheda **Server di configurazione**.
- Se è già stato creato un gruppo di protezione e successivamente si aggiunge un server vCenter o un host ESXi, possono essere necessari oltre 15 minuti per l'aggiornamento del portale di Azure Site Recovery e per la visualizzazione delle macchine virtuali nella finestra di dialogo **Aggiungere computer a un gruppo di protezione**.
- Se si desidera procedere immediatamente con l'aggiunta di computer al gruppo di protezione senza attendere l'individuazione pianificata, evidenziare il server di configurazione (senza fare clic su di esso) e fare clic sul pulsante **Aggiorna**.

Si noti anche che:

- È consigliabile progettare i gruppi di protezione in modo che rispecchino i carichi di lavoro. Ad esempio, eseguire un'applicazione specifica nello stesso gruppo per aggiungere computer.
- Quando si aggiungono computer a un gruppo di protezione, il server di elaborazione esegue automaticamente il push e l'installazione del servizio Mobility, se non è già installato. Si noti che è necessario preparare il meccanismo di push come descritto nel passaggio precedente.


Aggiungere computer a un gruppo di protezione:

1. Come procedura consigliata, fare clic su **Elementi protetti** > **Gruppo di protezione** > **Computer** > Aggiungi computer.
2. Se si attiva la protezione per macchine virtuali VMware, nella pagina **Seleziona macchine virtuali** selezionare il server vCenter che gestisce le macchine virtuali (o l'host EXSi in cui sono in esecuzione) e quindi selezionare le macchine.

	![Abilitare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Se si attiva la protezione per server fisici, nella pagina **Seleziona macchine virtuali** della procedura guidata **Aggiungi computer fisici** specificare l'indirizzo IP e il nome descrittivo. Selezionare quindi la famiglia di sistemi operativi.

	![Abilitare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)

4. In **Specificare le risorse di destinazione** selezionare l'account di archiviazione da usare per la replica e indicare se le impostazioni devono essere usate per tutti i carichi di lavoro. Si noti che gli account di archiviazione premium non sono attualmente supportati.

	>[AZURE.NOTE] Non è supportato lo spostamento degli account di archiviazione creati con il [nuovo portale di Azure](../storage/storage-create-storage-account.md) tra gruppi di risorse.

	![Abilitare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. In **Specificare gli account** selezionare l'account [configurato](#install-the-mobility-service-with-push-installation) per l'installazione automatica del servizio Mobility.

	![Abilitare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Fare clic sul segno di spunta per completare l'aggiunta dei computer al gruppo di protezione e avviare la replica iniziale per ogni computer.

	>[AZURE.NOTE] Se l'installazione push è stata preparata, il servizio Mobility viene installato automaticamente nei computer in cui non è presente man mano che questi vengono aggiunti al gruppo di protezione. Dopo l'installazione del servizio viene avviato un processo di protezione che avrà esito negativo. Dopo l'errore è necessario riavviare manualmente ogni computer in cui è stato installato il servizio Mobility. Dopo il riavvio, il processo di protezione viene avviato nuovamente e viene eseguita la replica iniziale.

Lo stato può essere monitorato nella pagina **Processi**.

![Abilitare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

È possibile monitorare lo stato di protezione anche da **Elementi protetti** > <protection group name> > **Macchine virtuali**. Al termine della replica iniziale, quando i dati sono sincronizzati, lo stato del computer passa a **Protetto**.

![Abilitare la protezione](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## Passaggio 11: Impostare le proprietà dei computer protetti

1. Quando lo stato della macchina virtuale è **Protetto**, sarà possibile configurarne le proprietà di failover. Nei dettagli del gruppo di protezione selezionare il computer e aprire la scheda **Configura**.
2. Site Recovery suggerisce automaticamente le proprietà per la macchina virtuale di Azure e rileva le impostazioni della rete locale.

	![Impostare le proprietà di una macchina virtuale](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. È possibile modificare le impostazioni descritte di seguito.

	-  **Nome della VM di Azure**: il nome assegnato al computer in Azure dopo il failover. Il nome deve essere conforme ai requisiti di Azure.

	-  **Dimensioni della VM di Azure**: il numero di schede di rete dipende dalle dimensioni specificate per la macchina virtuale di destinazione. [Altre informazioni](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) su dimensioni e schede di rete. Si noti che:

		- Quando si modificano le dimensioni di una macchina virtuale e si salvano le impostazioni, il numero di schede di rete cambia alla successiva apertura della scheda **Configura**. Il numero di schede di rete delle macchine virtuali di destinazione corrisponde minimo al numero di schede di rete nella macchina virtuale di origine e al numero massimo di schede di rete supportate dalla dimensione della macchina virtuale selezionata.
			- Se il numero di schede di rete nella macchina di origine è minore o uguale al numero di schede consentite per la macchina di destinazione, la destinazione avrà lo stesso numero di schede dell’origine.
			- Se il numero di schede per la macchina virtuale di origine supera il numero consentito per le dimensioni di destinazione, verrà utilizzata la dimensione di destinazione massima.
			- Ad esempio, se una macchina di origine dispone di due schede di rete e le dimensioni della macchina di destinazione ne supportano quattro, la macchina di destinazione avrà due schede. Se la macchina di origine dispone di due schede ma le dimensioni di destinazione supportate ne consentono solo una, la macchina di destinazione avrà una sola scheda.
		- Se la macchina virtuale ha più schede di rete, tutte le schede devono essere connesse alla stessa rete di Azure.
	- **Rete di Azure**: è necessario specificare una rete di Azure a cui le macchine virtuali di Azure verranno connesse dopo il failover. In caso contrario, le macchine virtuali di Azure non verranno connesse ad alcuna rete. È necessario specificare una rete di Azure anche per eseguire il failback da Azure nel sito locale. Il failback richiede una connessione VPN tra una rete di Azure e una rete locale.
	- **Indirizzo IP/subnet di Azure**: per ogni scheda di rete è necessario selezionare la subnet a cui deve connettersi la macchina virtuale di Azure. Si noti che:
		- Se la scheda di rete del computer di origine è configurata per l'uso di un indirizzo IP statico, è possibile specificare un indirizzo IP statico per la macchina virtuale di Azure. Se non si specifica un indirizzo IP statico verrà allocato un indirizzo IP disponibile. Se viene specificato un indirizzo IP di destinazione già usato da un'altra macchina virtuale in Azure, il failover avrà esito negativo. Se la scheda di rete del computer di origine è configurata per l'uso del protocollo DHCP, l'impostazione per Azure sarà DHCP.

## Passaggio 12: Creare un piano di ripristino ed eseguire un failover

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

È possibile eseguire il failover per un singolo computer o il failover di più macchine virtuali che eseguono la stessa attività o lo stesso carico di lavoro. Per eseguire il failover di più computer allo stesso tempo è necessario aggiungerli a un piano di ripristino.

### Creare un piano di ripristino

1. Nella pagina **Piani di ripristino** fare clic su **Aggiungi piano di ripristino** per aggiungere un piano di ripristino. Specificare i dettagli per il piano e selezionare **Azure** come destinazione.

	![Configurare un piano di ripristino](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. In **Seleziona macchine virtuali** selezionare un gruppo di protezione e quindi i computer nel gruppo da aggiungere al piano di ripristino.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

È possibile personalizzare il piano per creare gruppi e una sequenza dell'ordine in cui verrà eseguito il failover dei computer nel piano di ripristino. È anche possibile aggiungere script e istruzioni per azioni manuali. Gli script possono essere creati manualmente o usando i [runbook di Automazione di Azure](site-recovery-runbook-automation.md). [Altre informazioni](site-recovery-create-recovery-plans.md) sulla personalizzazione dei piani di ripristino.

## Eseguire un failover

Prima di eseguire un failover è opportuno tenere presente quanto segue.


- Assicurarsi che il server di gestione sia in esecuzione e disponibile, perché in caso contrario il failover avrà esito negativo.
- Se si esegue un failover non pianificato, si noti che:

	- È consigliabile arrestare i computer primari prima di eseguire un failover non pianificato. Questo permette di evitare che il computer di origine e quello di replica siano in esecuzione nello stesso momento. Se si esegue la replica di macchine virtuali VMware, quando si esegue un failover non pianificato è possibile specificare a Site Recovery di arrestare le macchine di origine. L'esito positivo di questa operazione dipende dallo stato del sito primario. Per la replica di server fisici questa opzione non è disponibile.
	- Quando si esegue un failover non pianificato, la replica dei dati dai computer primari viene arrestata per evitare il trasferimento di dati differenziali a failover iniziato.

- Per connettersi alla macchina virtuale di replica in Azure dopo il failover, abilitare Connessione Desktop remoto nel computer di origine prima di eseguire il failover e consentire la connessione RDP attraverso il firewall. È anche necessario consentire il protocollo RDP nell'endpoint pubblico della macchina virtuale di Azure dopo il failover. Per garantire il funzionamento del protocollo RDP dopo un failover, seguire queste [procedure consigliate](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

>[AZURE.NOTE] Per ottenere prestazioni ottimali quando si esegue un failover in Azure, assicurarsi di aver installato l'agente di Azure nel computer protetto. Questo consente un avvio più veloce e facilita anche la diagnosi nel caso di problemi. L'agente Linux è disponibile [qui](https://github.com/Azure/WALinuxAgent), mentre l'agente Windows è disponibile [qui](http://go.microsoft.com/fwlink/?LinkID=394789)

### Eseguire un failover di test

Eseguire un failover di test per simulare i processi di failover e ripristino in una rete isolata che non influisce sull'ambiente di produzione, in modo che la replica continui normalmente. Il failover di test inizia dall'origine e può essere eseguito in due modi:

- **Senza una rete di Azure**: se si esegue un failover di test senza specificare una rete, il test verifica semplicemente l'avvio e la corretta visualizzazione delle macchine virtuali in Azure. Le macchine virtuali non vengono connesse a una rete di Azure dopo il failover.
- **Con una rete di Azure**: questo tipo di failover verifica che l'intero ambiente di replica venga rilevato come previsto e che le macchine virtuali di Azure siano connesse alla rete specificata.


1. Nella pagina **Piani di ripristino** selezionare il piano e fare clic su **Failover di test**.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. In **Conferma failover di test** selezionare **Nessuna** per scegliere di non usare una rete di Azure per il failover di test oppure selezionare la rete a cui connettere le macchine virtuali di test dopo il failover. Fare clic sul segno di spunta per avviare il failover.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Monitorare lo stato di avanzamento del failover nella scheda **Processi**.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Al termine del failover sarà possibile visualizzare il computer di Azure di replica in **Macchine virtuali** nel portale di Azure. Per avviare una connessione RDP alla macchina virtuale di Azure è necessario aprire la porta 3389 nell'endpoint della macchina virtuale.

5. Quando il processo di failover raggiunge la fase conclusiva del test, fare clic su Completa test per terminare il test. Usare le Note per registrare e salvare eventuali osservazioni associate al failover di test.

6. Fare clic su **Failover di test completato** per pulire automaticamente l'ambiente di test. Al termine, lo stato del failover di test indicherà che l'operazione è **completata**. Eventuali elementi o macchine virtuali create automaticamente durante il failover di test verranno eliminate. Se un failover di test continua per più di due settimane, ne viene forzato il completamento.



### Eseguire un failover non pianificato

Il failover non pianificato viene avviato da Azure e può essere eseguito anche se il sito primario non è disponibile.


1. Nella pagina **Piani di ripristino** selezionare il piano e fare clic su **Failover** > **Failover non pianificato**.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Se si esegue la replica di macchine virtuali VMware è possibile provare ad arrestare le macchine virtuali locali. Il failover continua indipendentemente dall'esito di questo tentativo. Se l'operazione non viene eseguita correttamente, i dettagli dell'errore vengono visualizzati nella scheda **Processi** > **Processi di failover non pianificati**.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

	>[AZURE.NOTE] Questa opzione non è disponibile se si esegue la replica di server fisici. Se possibile, è necessario provare ad arrestare i computer manualmente.

3. In **Conferma failover** verificare la direzione del failover in Azure e selezionare il punto di ripristino da usare per il failover. Se nella configurazione delle proprietà di replica sono state abilitate più macchine virtuali, è possibile ripristinare fino all'ultimo punto di ripristino dell'applicazione o coerente con l'arresto anomalo del sistema. È anche possibile selezionare **Punto di ripristino personalizzato** per eseguire il ripristino a un punto precedente nel tempo. Fare clic sul segno di spunta per avviare il failover.

	![Aggiungi macchine virtuali.](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Attendere il completamento del processo di failover non pianificato. È possibile monitorare lo stato di avanzamento del failover nella scheda **Processi**. Si noti che anche se si verificano errori durante un failover non pianificato, il piano di ripristino viene eseguito fino al completamento. Al termine del failover sarà possibile visualizzare il computer di Azure di replica in Macchine virtuali nel portale di Azure.

### Connettersi a macchine virtuali di Azure replicate dopo il failover

Per connettersi a macchine virtuali replicate in Azure al termine del failover, è necessario quanto segue:

1. Una connessione Desktop remoto abilitata nel computer primario.
2. Windows Firewall nel computer primario impostato in modo da consentire il protocollo RDP.
3. Dopo il failover è necessario aggiungere il protocollo RDP all'endpoint pubblico per la macchina virtuale di Azure.

[Altre informazioni](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) su come procedere.


## Distribuire server di elaborazione aggiuntivi

Se è necessario aumentare la distribuzione oltre i 200 computer di origine oppure la varianza totale giornaliera è superiore a 2 TB, sono necessari server di elaborazione aggiuntivi per gestire il volume di traffico. Per configurare un server di elaborazione aggiuntivo, verificare i requisiti in [Server di elaborazione aggiuntivi](#additional-process-servers) e seguire le istruzioni qui riportate per configurare il server di elaborazione. Dopo aver configurato il server è possibile configurarne l'uso da parte dei computer di origine.

### Configurare un server di elaborazione aggiuntivo

Per configurare un server di elaborazione aggiuntivo, procedere come segue:

- Eseguire l'installazione guidata unificata per configurare un server di gestione solo come server di elaborazione.
- Per gestire la replica dei dati usando solo il nuovo server di elaborazione è necessario eseguire la migrazione dei computer protetti.

### Installare il server di elaborazione

1. Nella pagina Avvio rapido scaricare il file di installazione unificata per l'installazione del componente di Site Recovery. Eseguire l'installazione.
2. In **Prima di iniziare** selezionare **Aggiungere server di elaborazione per aumentare le istanze di distribuzione**.

	![Aggiungere il server di elaborazione](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Completare la procedura guidata come per la [configurazione](#step-5-install-the-management-server) del primo server di gestione.
4. In **Dettagli del server di configurazione** specificare l'indirizzo IP del server di gestione originario in cui è stato installato il server di configurazione e la relativa passphrase. Per ottenere la passphrase, eseguire **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** nel server di gestione originario.

	![Aggiungere il server di elaborazione](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### Eseguire la migrazione dei computer per usare il nuovo server di elaborazione

1. Aprire **Server di configurazione** > **Server** > nome del server di gestione originario > **Dettagli server**.

	![Aggiornare il server di elaborazione](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Nell'elenco **Server di elaborazione** fare clic su **Cambia server di elaborazione** accanto al server che si desidera modificare.

	![Aggiornare il server di elaborazione](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. In **Cambia server di elaborazione** > **Server di elaborazione di destinazione** selezionare il nuovo server di gestione, quindi selezionare le macchine virtuali che il nuovo server di elaborazione deve gestire. Fare clic sull'icona informazioni per ottenere informazioni sul server. Per consentire di prendere le decisioni relative al carico, viene visualizzato lo spazio medio necessario per replicare ogni macchina virtuale selezionata nel nuovo server di elaborazione. Fare clic sul segno di spunta per avviare la replica nel nuovo server di elaborazione.

	![Aggiornare il server di elaborazione](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)




## Autorizzazioni di VMware per l'accesso a vCenter

Il server di elaborazione può rilevare automaticamente le macchine virtuali in un server vCenter. Per eseguire l'individuazione automatica è necessario definire un ruolo (Azure\_Site\_Recovery) a livello di vCenter per permettere a Site Recovery di accedere al server vCenter. Si noti che per eseguire la sola migrazione di computer VMware in Azure, senza eseguire il failback da Azure, è sufficiente definire un ruolo di sola lettura. Configurare le autorizzazioni come descritto nel [Passaggio 6: Impostare le credenziali per il server vCenter](#step-6-set-up-credentials-for-the-vcenter-server). Le autorizzazioni relative ai ruoli sono riepilogate nella tabella seguente.

**Ruolo** | **Dettagli** | **Autorizzazioni**
--- | --- | ---
Ruolo Azure\_Site\_Recovery | Individuazione di macchine virtuali VMware |Assegnare i privilegi seguenti per il server vCenter:<br/><br/>Datastore -> Allocate space, Browse datastore, Low level file operations, Remove file, Update virtual machine files<br/><br/>Rete -> Network assign<br/><br/>Risorsa -> Assign virtual machine to resource pool, Migrate powered off virtual machine, Migrate powered on virtual machine<br/><br/>Attività -> Create task, Update task<br/><br/>Macchina virtuale -> Configuration<br/><br/>Macchina virtuale -> Interact -> Answer question, Device connection, Configure CD media, Configure floppy media, Power off, Power on, VMware tools install<br/><br/>Macchina virtuale -> Inventory -> Create, Register, Unregister<br/><br/>Macchina virtuale -> Provisioning -> Allow virtual machine download, Allow virtual machine files upload<br/><br/>Macchina virtuale -> Snapshots -> Remove snapshots
Ruolo vCenter User | Individuazione della macchina virtuale VMware/Failover senza arresto della macchina virtuale di origine | Assegnare i privilegi seguenti per il server vCenter:<br/><br/>Oggetto data center –> Propagate to child object, ruolo = Read-only <br/><br/>L'utente viene assegnato a livello di data center e ha quindi accesso a tutti gli oggetti nel data center. Se si vuole limitare l'accesso, assegnare il ruolo **No access** con l'oggetto **Propagate to child object** agli oggetti figlio (host ESX, archivi dati, VM e reti).
Ruolo vCenter User | Failover e failback | Assegnare i privilegi seguenti per il server vCenter:<br/><br/>Oggetto data center –> Propagate to child object, ruolo = Azure\_Site\_Recovery<br/><br/>L'utente viene assegnato a livello di data center e ha quindi accesso a tutti gli oggetti nel data center. Se si vuole limitare l'accesso, assegnare il ruolo **No access** con **Propagate to child object** agli oggetti figlio (host ESX, archivi dati, VM e reti). 



## Informazioni e comunicazioni sul software di terze parti

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## Passaggi successivi

[Altre informazioni sul failback](site-recovery-failback-azure-to-vmware-classic.md) per rendere nuovamente disponibili nell'ambiente locale i computer sottoposti a failover in esecuzione in Azure.

<!---HONumber=AcomDC_0525_2016-->