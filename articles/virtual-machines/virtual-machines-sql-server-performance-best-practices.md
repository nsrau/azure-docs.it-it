<properties 
	pageTitle="Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure￼￼￼￼￼￼￼.￼"
	description="In questo argomento vengono fornite le procedure consigliate per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Microsoft Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/24/2015"
	ms.author="jroth"/>

# Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure￼￼￼￼￼￼￼.￼

## Panoramica

In questo argomento vengono fornite le procedure consigliate per ottimizzare le prestazioni di SQL Server nella macchina virtuale di Microsoft Azure. Durante l'esecuzione di SQL Server in Macchine virtuali di Azure, è consigliabile continuare a usare le stesse opzioni di ottimizzazione delle prestazioni dei database applicabili a SQL Server nell'ambiente server locale. Tuttavia, le prestazioni di un database relazionale in un cloud pubblico dipendono da molti fattori, ad esempio dalle dimensioni della macchina virtuale e dalla configurazione dei dischi dati.

>[AZURE.NOTE]Questo documento offre informazioni su come ottenere le migliori prestazioni per SQL Server in Macchine virtuali di Azure. Se il carico di lavoro è contenuto, potrebbero non essere necessarie tutte le ottimizzazione elencate di seguito. Prendere in considerazione le esigenze di prestazioni e i modelli di carico di lavoro durante la valutazione di queste indicazioni.

Per informazioni complementari su questo argomento, è possibile [scaricare il white paper seguente](http://download.microsoft.com/download/D/2/0/D20E1C5F-72EA-4505-9F26-FEF9550EFD44/Performance%20Guidance%20for%20SQL%20Server%20in%20Windows%20Azure%20Virtual%20Machines.docx). Si noti che le informazioni contenute in questo white paper sono obsolete in alcune aree. Ad esempio, il white paper è stato scritto prima dell'introduzione di Archiviazione Premium che ora è consigliato per le prestazioni ottimali.

## Elenco di controllo rapido

Di seguito è riportato un elenco controllo rapido per ottimizzare le prestazioni di SQL Server in Macchine virtuali di Azure:

- Usare [Archiviazione Premium](../storage/storage-premium-storage-preview-portal.md).

- Usare le [dimensioni della macchina virtuale](virtual-machines-size-specs.md) DS3 o superiore per SQL Enterprise Edition e DS2 o superiore per SQL Standard Edition.

- Usare almeno 2 [dischi P30](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whit-ITing-premium-storage) (1 per i file di log, 1 per i file di dati e TempDB).

- Mantenere l'[account di archiviazione](../storage/storage-create-storage-account.md) e la macchina virtuale di SQL Server nella stessa area.

- Disabilitare l'[archiviazione con ridondanza geografica](../storage/storage-redundancy.md) (replica geografica) di Azure nell'account di archiviazione.

- Evitare l'uso del sistema operativo o di dischi temporanei per la registrazione o l'archiviazione di database.

- Abilitare la lettura della cache sui dischi che ospitano i file di dati e TempDB.

- Non abilitare la memorizzazione nella cache sui dischi che ospitano il file di log.

- Eseguire lo striping di più dischi dati di Azure per ottenere una maggiore velocità effettiva I/O.

- Formattare con dimensioni di allocazione documentate.

- Abilitare la compressione di pagina di database.

- Abilitare l'inizializzazione immediata dei file per i file di dati.

- Limitare o disabilitare l'aumento automatico delle dimensioni per il database.

- Disabilitare la compattazione automatica per il database.

- Spostare tutti i database su dischi dati, inclusi i database di sistema.

- Spostare le directory dei file di traccia e dei log degli errori di SQL Server sui dischi dati.

- Applicare le correzioni delle prestazioni di SQL Server.

- Configurare i percorsi predefiniti.

- Abilitare le pagine bloccate.

- Eseguire il backup direttamente nell'archivio BLOB.

Per altre informazioni, seguire le linee guida fornite nelle sottosezioni seguenti.

## Considerazioni relative alle dimensioni della macchina virtuale e all'account di archiviazione

Per le applicazioni sensibili alle prestazioni, è consigliabile usare le dimensioni delle macchine virtuali seguenti:

- **SQL Server Enterprise Edition**: DS3 o superiore

- **SQL Server Standard Edition**: DS2 o superiore

Per informazioni aggiornate sulle dimensioni di macchina virtuale supportate, vedere [Dimensioni per le macchine virtuali](virtual-machines-size-specs.md).

È inoltre consigliabile creare account di archiviazione di Azure nello stesso data center delle macchine virtuali di SQL Server per ridurre i tempi di trasferimento. Quando si crea un account di archiviazione, disabilitare la replica geografica in quanto l'ordine di scrittura coerente su più dischi non è garantito. In alternativa, si consiglia di configurare una tecnologia di ripristino di emergenza di SQL Server tra due data center di Azure. Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Considerazioni su dischi e prestazioni

Quando si crea una macchina virtuale di Azure, la piattaforma associa almeno un disco alla macchina virtuale per il disco del sistema operativo. Si tratta di un disco rigido virtuale memorizzato come BLOB di pagine nell'archivio. È possibile associare dischi aggiuntivi alla macchina virtuale come dischi dati che vengono memorizzati nell'archivio come BLOB di pagine. Esiste un altro disco in Macchine virtuali di Azure denominato disco temporaneo. Questo è un disco sul nodo che può essere usato come area scratch.

### Disco del sistema operativo

Per disco del sistema operativo si intende un disco rigido virtuale che è possibile avviare e montare come versione in esecuzione di un sistema operativo ed è etichettato come unità **C**.

I criteri predefiniti di memorizzazione nella cache per il disco del sistema operativo predefinito sono **Lettura/Scrittura**. Per le applicazioni sensibili alle prestazioni, è consigliabile usare dischi dati anziché il disco del sistema operativo. Vedere la sezione relativa ai dischi di dati riportata di seguito.

### Disco temporaneo

L'unità di archiviazione temporanea, etichettata come unità **D**: non è persistente nell'archiviazione BLOB di Azure. Non archiviare i file di dati o di log nell'unità **D**:.

Archiviare solo TempDB e/o le estensioni del pool di buffer nell'unità **D** quando si usano macchine virtuali serie D o G. Diversamente da altre serie di macchine virtuali, l'unità **D** nelle macchine virtuali serie D e G è basata su SSD. Ciò può migliorare le prestazioni dei carichi di lavoro che usano pesantemente oggetti temporanei o che hanno set di lavoro che non rientrano nella memoria. Per altre informazioni, vedere [Utilizzo di SSD in Macchine virtuali di Azure per archiviare estensioni del pool di buffer e TempDB di SQL Server](http://blogs.technet.com/b/dataplatforminsider/archive/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions.aspx).

### Disco dati

- **Numero di dischi dati per file di dati e di log**: come minimo, usare 2 [dischi P30](../storage/storage-premium-storage-preview-portal.md), dove un disco contiene i file del log e l'altro contiene i file di dati e TempDB. Per aumentare la velocità effettiva, è possibile richiedere dischi dati aggiuntivi. Per determinare il numero di dischi dati, è necessario analizzare il numero di operazioni di input/output al secondo disponibili per i dischi dati e di log. Per altre informazioni, vedere le tabelle di operazioni di input/output al secondo per tutte le [dimensioni di macchina virtuale](virtual-machines-size-specs.md) e dimensioni di disco nel seguente articolo: [Uso di Archiviazione Premium per dischi](../storage/storage-premium-storage-preview-portal.md). Se è necessaria una maggiore larghezza di banda, è possibile associare dischi aggiuntivi usando lo striping del disco. Se non si usa Archiviazione Premium, è consigliabile aggiungere il numero massimo di dischi dati supportati dalle [dimensioni della macchina virtuale](virtual-machines-size-specs.md) e usare lo striping del disco. Per altre informazioni sullo striping del disco, vedere la relativa sezione riportata di seguito.

- **Criteri di memorizzazione nella cache**: abilitare la lettura della cache per i dischi dati che ospitano solo file di dati e TempDB. Se non si usa Archiviazione Premium, non abilitare la memorizzazione nella cache per i dischi dati. Per istruzioni sulla configurazione della memorizzazione nella cache su disco, vedere gli argomenti seguenti: [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

- **Dimensioni unità di allocazione NTFS**: quando si formatta il disco dati, è consigliabile usare una dimensione di unità di allocazione di 64 KB per file di log e dati, nonché per TempDB.

- **Striping del disco**: è consigliabile attenersi alle seguenti linee guida.

	- Per Windows 8 e Windows Server 2012 o versioni successive, usare gli [spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx). Impostare la dimensione di striping su 64 KB per carichi di lavoro OLTP e su 256 KB per carichi di lavoro di data warehouse per evitare effetti sulle prestazioni causati dal disallineamento delle partizioni. Impostare il numero di colonne sul numero di dischi fisici. Per configurare uno spazio di archiviazione con più di 8 dischi è necessario usare PowerShell (non l'interfaccia utente di Gestione server) per impostare in modo esplicito il numero di colonne in modo che corrisponda al numero di dischi. Per altre informazioni su come configurare gli [spazi di archiviazione](https://technet.microsoft.com/library/hh831739.aspx), vedere [Cmdlet per spazi di archiviazione in Windows PowerShell](https://technet.microsoft.com/library/jj851254.aspx)
	
	- Per Windows 2008 R2 o versioni precedenti, è possibile usare i dischi dinamici (volumi con striping del sistema operativo) e la dimensione di striping è sempre di 64 KB. Si noti che questa opzione è deprecata a partire da Windows 8 e Windows Server 2012. Per informazioni, vedere l'informativa di supporto relativa al [passaggio dal servizio disco virtuali all'API di gestione archivi di Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).
	
	- Se il carico di lavoro non usa in modo intensivo i log e non sono necessarie operazioni di input/output al secondo dedicate, è possibile configurare un solo pool di archiviazione. In caso contrario, creare due pool di archiviazione, uno per i file di log e un altro pool di archiviazione per il file di dati e TempDB. Determinare il numero di dischi associati a ogni pool di archiviazione in base alle aspettative di carico. Tenere presente che le diverse dimensioni di macchine virtuali consentono diversi numeri di dischi dati associati. Per altre informazioni, vedere [Dimensioni per le macchine virtuali](virtual-machines-size-specs.md).

## Considerazioni sulle prestazioni I/O

- Quando l'applicazione e le richieste vengono eseguite in parallelo, si ottengono i migliori risultati con Archiviazione Premium. Archiviazione Premium è progettato per scenari in cui la profondità della coda I/O è maggiore di 1, pertanto si noteranno eventualmente impercettibili miglioramenti delle prestazioni per le richieste seriali a thread singolo (anche se usano in modo intensivo l'archiviazione). Ad esempio, ciò potrebbe influire sui risultati di test a thread singolo degli strumenti di analisi delle prestazioni, ad esempio SQLIO.

- È consigliabile usare la [compressione di pagina del database](https://msdn.microsoft.com/library/cc280449.aspx) in quanto consente di migliorare le prestazioni dei carichi di lavoro con utilizzo intensivo di I/O. Tuttavia, la compressione dei dati potrebbe incrementare l'utilizzo della CPU nel server di database.

- È consigliabile comprimere i file di dati durante il trasferimento in entrata e in uscita di Azure.

- È possibile abilitare l'inizializzazione immediata dei file per ridurre il tempo necessario per l'allocazione iniziale dei file. Per sfruttare l'inizializzazione immediata dei file, concedere SE\_MANAGE\_VOLUME\_NAME all'account di servizio SQL Server (MSSQLSERVER) e aggiungerlo ai criteri di sicurezza **Esecuzione attività di manutenzione volume**. Se si usa un'immagine della piattaforma SQL Server per Azure, l'account del servizio predefinito (NT Service\\MSSQLSERVER) non viene aggiunto ai criteri di sicurezza **Esecuzione attività di manutenzione volume**. In altre parole, l'inizializzazione file immediata non è abilitata in un'immagine della piattaforma Server SQL di Azure. Dopo aver aggiunto l'account del servizio SQL Server ai criteri di sicurezza **Esecuzione attività di manutenzione volume**, riavviare il servizio SQL Server. Potrebbero essere presenti indicazioni sulla sicurezza da considerare per l'utilizzo di questa funzionalità. Per altre informazioni, vedere [Inizializzazione di file di database](https://msdn.microsoft.com/library/ms175935.aspx).

- L'**aumento automatico delle dimensioni** è considerato una semplice contingenza di crescita imprevista. La crescita dei dati e dei log non viene gestita quotidianamente con l'aumento automatico delle dimensioni. Se si usa questa funzionalità, impostare la precrescita del file mediante l'opzione Dimensioni.

- Verificare che la **compattazione automatica** sia disabilitata per evitare inutili sovraccarichi che possono influire negativamente sulle prestazioni.

- Se si esegue SQL Server 2012, installare l'aggiornamento cumulativo 10 del Service Pack 1. Questo aggiornamento contiene la correzione per le prestazioni ridotte delle operazioni I/O quando si esegue select nell'istruzione di una tabella temporanea in SQL Server 2012. Per informazioni, vedere questo [articolo della Knowledge Base](http://support.microsoft.com/kb/2958012).

- Spostare i database di sistema (ad esempio msdb e TempDB), i backup e le directory predefinite per dati e log di SQL Server su dischi dati senza memorizzazione nella cache per migliorare le prestazioni. Quindi, eseguire le azioni seguenti:

	- Modificare i percorsi di file per XEvent e Trace.
	
	- Modificare il percorso dei log degli errori di SQL.
	
	- Modificare il percorso di backup predefinito.
	
	- Modificare il percorso predefinito del database.

- Stabilire pagine bloccate per ridurre le operazioni di I/O e qualsiasi attività di paging.

## Considerazioni sulle prestazioni specifiche di funzionalità

Alcune distribuzioni possono ottenere ulteriori miglioramenti delle prestazioni usando tecniche di configurazione più avanzate. Nell'elenco seguente vengono evidenziate alcune funzionalità di SQL Server che consentono di ottenere prestazioni migliori:

- **Backup nell'archiviazione di Azure**: quando si eseguono backup per SQL Server in esecuzione in macchine virtuali di Azure, è possibile usare [Backup di SQL Server nell'URL](https://msdn.microsoft.com/library/dn435916.aspx). Questa funzionalità è disponibile a partire da SQL Server 2012 SP1 CU2 ed è consigliata per il backup su dischi dati associati. Quando si esegue il backup o il ripristino da e verso l'archiviazione di Azure, seguire le indicazioni fornite in [Procedure consigliate e risoluzione dei problemi per il backup di SQL Server nell'URL e Ripristino da backup archiviati nell'archiviazione di Azure](https://msdn.microsoft.com/library/jj919149.aspx). È inoltre possibile automatizzare i backup usando [Backup automatizzato per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-automated-backup.md).

	Prima di SQL Server 2012, usare lo [strumento di backup di SQL Server in Azure](https://www.microsoft.com/download/details.aspx?id=40740). Questo strumento consente di aumentare la velocità effettiva di backup usando più destinazioni di backup in striping.

- **File di dati di SQL Server in Azure**: questa nuova funzionalità ([file di dati di SQL Server in Azure](https://msdn.microsoft.com/library/dn385720.aspx)) è disponibile a partire da SQL Server 2014. L'esecuzione di SQL Server con file di dati in Azure dimostra le caratteristiche di prestazioni paragonabili a quelle dei dischi dati di Azure.

## Passaggi successivi

Se si desidera esplorare Archiviazione Premium e SQL Server in modo più approfondito, vedere l'articolo [Utilizzare Archiviazione Premium di Azure con SQL Server in macchine virtuali](virtual-machines-sql-server-use-premium-storage.md).

Per le procedure consigliate relative alla sicurezza, vedere [Considerazioni relative alla sicurezza per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-security-considerations.md).

Esaminare altri argomenti relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=August15_HO9-->