---
title: Eseguire la migrazione da SQL Server ad Azure SQL Istanza gestita
description: Informazioni su come eseguire la migrazione di un database da SQL Server al Istanza gestita SQL di Azure.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: ''
ms.date: 07/11/2019
ms.openlocfilehash: 2761b97e595f5e11b00e75cd778ee269b12bfcae
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917801"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>SQL Server la migrazione dell'istanza ad Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Questo articolo illustra i metodi per eseguire la migrazione di un'istanza di SQL Server 2005 o versione successiva in [Azure SQL istanza gestita](sql-managed-instance-paas-overview.md). Per informazioni sulla migrazione a un singolo database o a un pool elastico, vedere [Panoramica della migrazione: SQL Server al database SQL](../migration-guides/database/sql-server-to-sql-database-overview.md). Per informazioni sulla migrazione da altre piattaforme e linee guida per gli strumenti e le opzioni, vedere eseguire la migrazione [ad Azure SQL](../migration-guides/index.yml).

> [!NOTE]
> Se si vuole avviare rapidamente e provare Istanza gestita SQL di Azure, è possibile passare alla [Guida introduttiva](quickstart-content-reference-guide.md) invece che a questa pagina.

A livello generale, il processo di migrazione del database è simile a quello indicato di seguito:

![Processo di migrazione](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Valutare la compatibilità di SQL istanza gestita](#assess-sql-managed-instance-compatibility) in cui è necessario assicurarsi che non ci siano problemi di blocco che possono impedire le migrazioni.
  
  Questo passaggio include anche la creazione di una [baseline](#create-a-performance-baseline) per le prestazioni per determinare l'utilizzo delle risorse nell'istanza di SQL Server di origine. Questo passaggio è necessario se si desidera distribuire un'istanza gestita di dimensioni corrette e verificare che le prestazioni dopo la migrazione non siano interessate.
- [Scegliere Opzioni di connettività dell'app](connect-application-instance.md).
- [Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali in](#deploy-to-an-optimally-sized-managed-instance) cui si scelgono le caratteristiche tecniche (numero di Vcore, quantità di memoria) e il livello di prestazioni (business critical, per utilizzo generico) dell'istanza gestita.
- [Selezionare metodo di migrazione e migrare](#select-a-migration-method-and-migrate) in cui si esegue la migrazione dei database usando la migrazione offline (backup/ripristino nativo, importazione/esportazione di database) o migrazione in linea (servizio migrazione dati di Azure, replica transazionale).
- [Monitorare le applicazioni](#monitor-applications) per assicurarsi di avere le prestazioni previste.

> [!NOTE]
> Per eseguire la migrazione di un singolo database in un database singolo o in un pool elastico, vedere [eseguire la migrazione di un database di SQL Server al database SQL di Azure](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Valutazione della compatibilità di SQL Istanza gestita

Per prima cosa, determinare se SQL Istanza gestita è compatibile con i requisiti del database dell'applicazione. Il Istanza gestita SQL è progettato per offrire una migrazione semplificata e in modalità Lift-and-Shift per la maggior parte delle applicazioni esistenti che utilizzano SQL Server. Tuttavia, talvolta è possibile richiedere caratteristiche o funzionalità non ancora supportate e il costo di implementazione di una soluzione alternativa è troppo elevato.

Usare [Data Migration Assistant](/sql/dma/dma-overview) per rilevare i potenziali problemi di compatibilità che incidono sulla funzionalità del database nel database SQL di Azure. In caso di problemi di blocco segnalati, potrebbe essere necessario prendere in considerazione un'opzione alternativa, ad esempio [SQL Server in una VM di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Ecco alcuni esempi:

- Se è necessario l'accesso diretto al sistema operativo o file system, ad esempio per installare agenti di terze parti o personalizzati nella stessa macchina virtuale con SQL Server.
- Se si ha una dipendenza rigorosa da funzionalità che non sono ancora supportate, ad esempio FileStream/FileTable, polibase e transazioni tra istanze.
- Se è assolutamente necessario rimanere in una versione specifica di SQL Server (2012, ad esempio).
- Se i requisiti di calcolo sono molto inferiori rispetto alle offerte di istanze gestite (un vCore, ad esempio) e il consolidamento dei database non è un'opzione accettabile.

Se sono stati risolti tutti i blocchi di migrazione identificati e si continua la migrazione a SQL Istanza gestita, si noti che alcune delle modifiche potrebbero influire sulle prestazioni del carico di lavoro:

- Il modello di recupero con registrazione completa obbligatoria e la pianificazione regolare dei backup automatici potrebbero influito sulle prestazioni del carico di lavoro o sulle azioni di manutenzione/ETL se è stato usato periodicamente un modello con registrazione minima o con registrazione minima delle operazioni bulk oppure è stato interrotto
- Configurazioni a livello di server o di database diverse, ad esempio i flag di traccia o i livelli di compatibilità.
- Le nuove funzionalità di, ad esempio Transparent Database Encryption (Transparent Database Encryption) o i gruppi di failover automatico potrebbero avere un effetto sull'utilizzo di CPU e IO.

SQL Istanza gestita garantisce la disponibilità del 99,99% anche negli scenari critici, quindi il sovraccarico causato da queste funzionalità non può essere disabilitato. Per ulteriori informazioni, vedere [le cause principali che potrebbero causare diverse prestazioni in SQL Server e istanza gestita SQL di Azure](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-a-performance-baseline"></a>Creare una baseline per le prestazioni

Se è necessario confrontare le prestazioni del carico di lavoro in un'istanza gestita con il carico di lavoro originale in esecuzione in SQL Server, è necessario creare un riferimento per le prestazioni che verrà usato per il confronto.

La linea di base delle prestazioni è un set di parametri, ad esempio utilizzo medio/massimo CPU, latenza IO disco media/massima, velocità effettiva, IOPS, permanenza presunta pagine media/massima e dimensioni medie massime del tempdb. Si vuole avere parametri simili o ancora migliori dopo la migrazione, quindi è importante misurare e registrare i valori di base per questi parametri. Oltre ai parametri di sistema, è necessario selezionare un set di query rappresentative o le query più importanti nel carico di lavoro e misurare la durata minima/media/massima e l'utilizzo della CPU per le query selezionate. Questi valori consentono di confrontare le prestazioni del carico di lavoro in esecuzione nell'istanza gestita con i valori originali nell'istanza di SQL Server di origine.

Di seguito sono elencati alcuni parametri che è necessario misurare nell'istanza di SQL Server:

- [Monitorare l'utilizzo della CPU nell'istanza di SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registrare l'utilizzo medio e massimo della CPU.
- [Monitorare l'utilizzo della memoria nell'istanza di SQL Server](/sql/relational-databases/performance-monitor/monitor-memory-usage) e determinare la quantità di memoria utilizzata da componenti diversi, ad esempio pool di buffer, cache dei piani, pool di archivio colonne, [OLTP in memoria](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)e così via. Inoltre, è necessario trovare i valori medi e massimi del contatore delle prestazioni della memoria permanenza presunta delle pagine.
- Monitorare l'utilizzo di i/o su disco nell'istanza di SQL Server di origine utilizzando [sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) visualizzazione o [contatori delle prestazioni](/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorare il carico di lavoro e le prestazioni delle query o l'istanza di SQL Server esaminando viste a gestione dinamica o Query Store se si esegue la migrazione da una versione di SQL Server 2016 +. Identificare la durata media e l'utilizzo della CPU delle query più importanti nel carico di lavoro per confrontarle con le query in esecuzione nell'istanza gestita.

> [!Note]
> Se si riscontrano problemi con il carico di lavoro in SQL Server, ad esempio un utilizzo elevato della CPU, un numero eccessivo di richieste di memoria o problemi di parametrizzazione o tempdb, è consigliabile provare a risolverli nell'istanza del SQL Server di origine prima di eseguire la migrazione e la baseline. La migrazione di problemi noti a qualsiasi nuovo sistema può causare risultati imprevisti e invalidare qualsiasi confronto delle prestazioni.

Come risultato di questa attività, è necessario aver documentato i valori medi e massimi per l'utilizzo della CPU, della memoria e dell'i/o nel sistema di origine, nonché la durata media e massima e l'utilizzo della CPU delle query dominanti e più critiche nel carico di lavoro. Usare questi valori in un secondo momento per confrontare le prestazioni del carico di lavoro in un'istanza gestita con le prestazioni di base del carico di lavoro nell'istanza di SQL Server di origine.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali

Il Istanza gestita SQL è progettato per i carichi di lavoro locali che pianificano il passaggio al cloud. Introduce un [nuovo modello di acquisto](../database/service-tiers-vcore.md) che offre maggiore flessibilità nella selezione del livello appropriato di risorse per i carichi di lavoro. Nell'ambiente locale, si è probabilmente soliti dimensionare i carichi di lavoro usando core fisici o larghezza di banda di I/O. Il modello di acquisto dell'istanza gestita è basato sui core virtuali, o "vCore", con I/O e spazio di archiviazione aggiuntivi disponibili separatamente. Il modello basato su vCore semplifica la comprensione dei requisiti di calcolo nel cloud rispetto alle risorse usate attualmente in locale. Questo nuovo modello consente di dimensionare correttamente l'ambiente di destinazione nel cloud. Alcune linee guida generali che possono essere utili per scegliere il livello di servizio e le caratteristiche corretti sono descritte di seguito:

- In base all'utilizzo della CPU di base, è possibile effettuare il provisioning di un'istanza gestita che corrisponda al numero di core utilizzati in SQL Server, tenendo presente che è possibile che le caratteristiche della CPU debbano essere ridimensionate in modo da corrispondere alle [caratteristiche della macchina virtuale in cui è installata l'istanza gestita](resource-limits.md#hardware-generation-characteristics).
- In base all'utilizzo della memoria di base, scegliere [il livello di servizio con memoria corrispondente](resource-limits.md#hardware-generation-characteristics). La quantità di memoria non può essere scelta direttamente, quindi è necessario selezionare l'istanza gestita con la quantità di Vcore con memoria corrispondente (ad esempio, 5,1 GB/vCore in quinta generazione).
- In base alla latenza di i/o di base del sottosistema file, scegliere tra il per utilizzo generico (latenza maggiore di 5 ms) e i livelli di servizio business critical (latenza inferiore a 3 ms).
- In base alla velocità effettiva di base, pre-allocare le dimensioni dei file di dati o di log per ottenere le prestazioni di i/o previste.

È possibile scegliere risorse di calcolo e di archiviazione in fase di distribuzione e quindi modificarle successivamente senza introdurre tempi di inattività per l'applicazione usando il [portale di Azure](../database/scale-resources.md):

![Dimensionamento istanza gestita](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Per informazioni su come creare l'infrastruttura di rete virtuale e un'istanza gestita, vedere [Creare un'istanza gestita](instance-create-quickstart.md).

> [!IMPORTANT]
> È importante che la VNet e la subnet di destinazione siano conformi ai [requisiti VNet dell'istanza gestita](connectivity-architecture-overview.md#network-requirements). Qualsiasi incompatibilità può impedire la creazione di nuove istanze o l'uso di quelle già create. Altre informazioni sulle [creazione di nuove reti](virtual-network-subnet-create-arm-template.md) e [la configurazione di reti esistenti](vnet-existing-add-subnet.md).

## <a name="select-a-migration-method-and-migrate"></a>Selezionare un metodo di migrazione ed eseguire la migrazione

SQL Istanza gestita è destinato a scenari utente che richiedono la migrazione di database di massa dalle implementazioni di database locali o di macchine virtuali di Azure. Sono la scelta ottimale quando è necessario sollevare e spostare il back-end delle applicazioni che usano regolarmente le funzionalità a livello di istanza e/o tra database. In questo scenario, è possibile spostare un'intera istanza in un ambiente corrispondente in Azure senza dover ridefinire l'architettura delle applicazioni.

Per spostare istanze di SQL, è necessario pianificare accuratamente quanto segue:

- Migrazione di tutti i database che devono essere collocati (quelli in esecuzione nella stessa istanza).
- Migrazione di oggetti a livello di istanza da cui dipende l'applicazione, tra cui account di accesso, credenziali, operatori e processi di SQL Agent e trigger a livello di server.

SQL Istanza gestita è un servizio gestito che consente di delegare alcune delle normali attività DBA alla piattaforma come sono state compilate. Non è pertanto necessario eseguire la migrazione di alcuni dati a livello di istanza, ad esempio i processi di manutenzione per i backup regolari o la configurazione di Always On, in quanto la [disponibilità elevata](../database/high-availability-sla.md) è incorporata.

SQL Istanza gestita supporta le seguenti opzioni di migrazione del database (attualmente questi sono gli unici metodi di migrazione supportati):

- Servizio migrazione del database di Azure: migrazione con tempi di inattività quasi nulli.
- `RESTORE DATABASE FROM URL` nativo: usa backup nativi di SQL Server e comporta un tempo di inattività

### <a name="azure-database-migration-service"></a>Servizio Migrazione del database di Azure

Il [servizio migrazione del database di Azure](../../dms/dms-overview.md) è un servizio completamente gestito progettato per consentire migrazioni senza interruzioni da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare i database esistenti di terze parti e di SQL Server in Azure. Le opzioni di distribuzione disponibili in anteprima pubblica includono i database nel database SQL di Azure e SQL Server database in una macchina virtuale di Azure. Il servizio migrazione del database è il metodo consigliato per la migrazione dei carichi di lavoro aziendali.

Se si usa SQL Server Integration Services (SSIS) in SQL Server locale, il servizio migrazione del database non supporta ancora la migrazione del catalogo SSIS (SSISDB) in cui sono archiviati i pacchetti SSIS, ma è possibile effettuare il provisioning di Azure-SSIS Integration Runtime (IR) in Azure Data Factory, in modo da creare un nuovo SSISDB in un'istanza gestita in modo che sia possibile ridistribuirvi i pacchetti. Vedere [creare Azure-SSIS IR in Azure Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md).

Per altre informazioni su questo scenario e sulla procedura di configurazione per il servizio migrazione del database, vedere [eseguire la migrazione del database locale all'istanza gestita usando il servizio migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Ripristino nativo da URL

Il ripristino di backup nativi (file con estensione bak) ricavati da un'istanza di SQL Server, disponibile in [archiviazione di Azure](https://azure.microsoft.com/services/storage/), è una delle funzionalità principali di SQL istanza gestita che consente la migrazione di database offline rapida e semplice.

Il diagramma seguente offre una panoramica di alto livello del processo:

![Il diagramma mostra SQL Server con una freccia con etichetta BACKUP/upload nell'URL che passa ad archiviazione di Azure e una seconda freccia con etichetta Ripristina da URL che passa da archiviazione di Azure a una Istanza gestita di SQL.](./media/migrate-to-instance-from-sql-server/migration-flow.png)

La tabella seguente contiene altre informazioni sul metodo che è possibile usare a seconda della versione di SQL Server di origine eseguita:

|Passaggio|Versione e motore SQL|Metodo Backup/Restore|
|---|---|---|
|Inserire il backup in Archiviazione di Azure|Prima di 2012 SP1 CU2|Caricare il file con estensione bak direttamente in archiviazione di Azure|
||2012 SP1 CU2 - 2016|Backup diretto con sintassi [con credenziali](/sql/t-sql/statements/restore-statements-transact-sql) deprecata|
||2016 e versioni successive|Backup diretto [con credenziali di firma di accesso condiviso](/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Eseguire il ripristino da archiviazione di Azure a un'istanza gestita|[Ripristino da URL con credenziali di firma di accesso condiviso](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Quando si esegue la migrazione di un database protetto da [Transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) a un'istanza gestita usando l'opzione di ripristino nativo, è necessario eseguire la migrazione del certificato corrispondente dalla macchina virtuale locale o di Azure SQL Server prima del ripristino del database. Per i dettagli, vedere [Eseguire la migrazione del certificato TDE a un'istanza gestita](tde-certificate-migrate.md).
> - Il ripristino di database di sistema non è supportato. Per eseguire la migrazione degli oggetti a livello di istanza (archiviati nei database master o msdb), è consigliabile creare uno script per eseguire lo script e gli script T-SQL nell'istanza di destinazione.

Per una guida di avvio rapido che descrive come eseguire il ripristino del backup di un database in un'istanza gestita con credenziali di firma di accesso condiviso, vedere l'articolo su come [eseguire il ripristino da un backup in un'istanza gestita](restore-sample-database-quickstart.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Monitorare le applicazioni

Una volta completata la migrazione a un'istanza gestita, è necessario tenere traccia del comportamento e delle prestazioni dell'applicazione del carico di lavoro. Questo processo include le attività seguenti:

- [Confrontare le prestazioni del carico di lavoro in esecuzione nell'istanza gestita](#compare-performance-with-the-baseline) con la [baseline delle prestazioni creata nell'istanza di SQL Server di origine](#create-a-performance-baseline).
- [Monitora continuamente le prestazioni del tuo carico di lavoro](#monitor-performance) per identificare i potenziali problemi e i miglioramenti.

### <a name="compare-performance-with-the-baseline"></a>Confrontare le prestazioni con la linea di base

La prima attività che dovrebbe essere eseguita immediatamente dopo la migrazione è confrontare le prestazioni del carico di lavoro con le prestazioni di base del carico di lavoro. L'obiettivo di questa attività consiste nel verificare che le prestazioni del carico di lavoro nell'istanza gestita soddisfino le proprie esigenze.

La migrazione del database a un'istanza gestita mantiene le impostazioni del database e il livello di compatibilità originale nella maggior parte dei casi. Le impostazioni originali vengono mantenute laddove possibile per ridurre il rischio di riduzioni delle prestazioni rispetto all'istanza di SQL Server di origine. Se il livello di compatibilità di un database utente prima della migrazione è 100 o un valore superiore, il livello rimane invariato dopo la migrazione. Se il livello di compatibilità di un database utente è 90 prima della migrazione, nel database aggiornato il livello di compatibilità è impostato su 100, che corrisponde al livello di compatibilità supportato più basso in un'istanza gestita. Il livello di compatibilità dei database di sistema è 140. Poiché la migrazione a un'istanza gestita sta effettivamente eseguendo la migrazione alla versione più recente del motore di database di SQL Server, è necessario tenere presente che è necessario testare di nuovo le prestazioni del carico di lavoro per evitare problemi di prestazioni sorprendenti.

Come prerequisito, verificare di aver completato le attività seguenti:

- Allineare le impostazioni nell'istanza gestita con le impostazioni dell'istanza di SQL Server di origine esaminando varie istanze, database, impostazioni di tempdb e configurazioni. Assicurarsi di non aver modificato le impostazioni, ad esempio i livelli di compatibilità o la crittografia, prima di eseguire il primo confronto delle prestazioni o di accettare il rischio che alcune delle nuove funzionalità abilitate potrebbero influire su alcune query. Per ridurre i rischi della migrazione, modificare il livello di compatibilità del database solo dopo il monitoraggio delle prestazioni.
- Implementare le [linee guida sulle procedure consigliate di archiviazione per per utilizzo generico](https://techcommunity.microsoft.com), ad esempio pre-allocare le dimensioni dei file per ottenere prestazioni migliori.
- Informazioni sulle [differenze di ambiente principali che potrebbero causare le differenze di prestazioni tra un'istanza gestita e SQL Server](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)e identificare i rischi che potrebbero influire sulle prestazioni.
- Assicurarsi di rimanere abilitati Query Store e l'ottimizzazione automatica nell'istanza gestita. Queste funzionalità consentono di misurare le prestazioni dei carichi di lavoro e di correggere automaticamente i potenziali problemi di prestazioni. Informazioni su come usare Query Store come strumento ottimale per ottenere informazioni sulle prestazioni del carico di lavoro prima e dopo la modifica del livello di compatibilità del database, come illustrato in [garantire la stabilità delle prestazioni durante l'aggiornamento a una versione più recente di SQL Server](/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Una volta preparato l'ambiente che è paragonabile al più possibile con l'ambiente locale, è possibile avviare l'esecuzione del carico di lavoro e misurare le prestazioni. Il processo di misurazione deve includere gli stessi parametri misurati [durante la creazione delle prestazioni di base delle misure del carico di lavoro nell'istanza di SQL Server di origine](#create-a-performance-baseline).
Di conseguenza, è consigliabile confrontare i parametri delle prestazioni con la linea di base e identificare le differenze critiche.

> [!NOTE]
> In molti casi, non è possibile ottenere esattamente le prestazioni corrispondenti nell'istanza gestita e SQL Server. Istanza gestita SQL di Azure è un motore di database SQL Server, ma la configurazione dell'infrastruttura e della disponibilità elevata in un'istanza gestita può presentare alcune differenze. Ci si potrebbe aspettare che alcune query risultino più veloci mentre altre potrebbero risultare più lente. L'obiettivo del confronto consiste nel verificare che le prestazioni del carico di lavoro nell'istanza gestita soddisfino le prestazioni in SQL Server (in media) e identifichino le query critiche con le prestazioni che non corrispondono alle prestazioni originali.

Il risultato del confronto delle prestazioni potrebbe essere:

- Le prestazioni dei carichi di lavoro nell'istanza gestita sono allineate o migliori rispetto alle prestazioni del carico di lavoro in SQL Server. In questo caso, è stato confermato che la migrazione è stata completata correttamente.
- La maggior parte dei parametri delle prestazioni e delle query nel carico di lavoro funziona correttamente, con alcune eccezioni con prestazioni ridotte. In questo caso, è necessario identificare le differenze e la loro importanza. Se sono presenti alcune query importanti con prestazioni ridotte, è necessario verificare se i piani SQL sottostanti sono stati modificati o se le query raggiungono alcuni limiti di risorse. In questo caso la mitigazione può essere l'applicazione di alcuni suggerimenti sulle query critiche (ad esempio, il livello di compatibilità modificato e lo strumento di stima della cardinalità legacy), direttamente o utilizzando guide di piano, ricompilare o creare statistiche e indici che potrebbero influire sui piani.
- La maggior parte delle query è più lenta in un'istanza gestita rispetto all'istanza di SQL Server di origine. In questo caso, provare a identificare le cause principali della differenza, ad esempio il [raggiungimento di un limite di risorse](resource-limits.md#service-tier-characteristics) , ad esempio i limiti di i/o, il limite di memoria, il limite di frequenza dei log dell'istanza Se non sono previsti limiti per le risorse che possono causare la differenza, provare a modificare il livello di compatibilità del database o modificare le impostazioni del database, ad esempio la stima della cardinalità legacy e riavviare il test. Esaminare le indicazioni fornite dall'istanza gestita o dalle viste Query Store per identificare le query che hanno reimpostato le prestazioni.

> [!IMPORTANT]
> Azure SQL Istanza gestita dispone di una funzionalità integrata di correzione automatica dei piani abilitata per impostazione predefinita. Questa funzionalità garantisce che le query che hanno funzionato correttamente nell'Incolla non peggiorano in futuro. Verificare che questa funzionalità sia abilitata e che il carico di lavoro sia stato eseguito abbastanza a lungo con le impostazioni precedenti prima di modificare le nuove impostazioni, in modo da consentire all'istanza gestita di ottenere informazioni sulle prestazioni e sui piani di base.

Apportare la modifica dei parametri o aggiornare i livelli di servizio per convergere alla configurazione ottimale fino a quando non si ottengono le prestazioni del carico di lavoro adatte alle proprie esigenze.

### <a name="monitor-performance"></a>Monitorare le prestazioni

SQL Istanza gestita fornisce numerosi strumenti avanzati per il monitoraggio e la risoluzione dei problemi ed è consigliabile usarli per monitorare le prestazioni dell'istanza. Alcuni dei parametri che è necessario monitorare sono:

- Utilizzo della CPU nell'istanza di per determinare se il numero di vcore di cui è stato effettuato il provisioning è la corrispondenza corretta per il carico di lavoro.
- Permanenza presunta della pagina nell'istanza gestita per determinare [se è necessaria memoria aggiuntiva](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Statistiche come `INSTANCE_LOG_GOVERNOR` o che indicano se si verificano problemi di i/o di `PAGEIOLATCH` archiviazione, soprattutto nel livello di per utilizzo generico, in cui potrebbe essere necessario pre-allocare i file per ottenere migliori prestazioni di i/o.

## <a name="leverage-advanced-paas-features"></a>Sfruttare le funzionalità avanzate di PaaS

Quando si è in una piattaforma completamente gestita e si è verificato che le prestazioni del carico di lavoro corrispondono alle prestazioni del carico di lavoro SQL Server, utilizzare i vantaggi forniti automaticamente come parte del servizio.

Anche se non si apportano modifiche all'istanza gestita durante la migrazione, è probabile che si accendano alcune delle nuove funzionalità mentre si sta effettuando l'istanza per sfruttare i miglioramenti più recenti del motore di database. Alcune modifiche sono abilitate solo dopo la [modifica del livello di compatibilità del database](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

Non è ad esempio necessario creare backup nell'istanza gestita. il servizio esegue automaticamente i backup. né preoccuparsi della pianificazione, dell'esecuzione e della gestione dei backup. SQL Istanza gestita offre la possibilità di eseguire il ripristino in qualsiasi momento entro il periodo di conservazione utilizzando il [ripristino temporizzato (ripristino temporizzato)](../database/recovery-using-backups.md#point-in-time-restore). Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, in quanto la [disponibilità elevata](../database/high-availability-sla.md) è incorporata in.

Per rafforzare la sicurezza, è consigliabile usare [Azure Active Directory autenticazione](../database/security-overview.md), il [controllo](auditing-configure.md), il [rilevamento delle minacce](../database/azure-defender-for-sql.md), la [sicurezza a livello di riga](/sql/relational-databases/security/row-level-security)e la [maschera dati dinamica](/sql/relational-databases/security/dynamic-data-masking).

Oltre alle funzionalità avanzate di gestione e sicurezza, un'istanza gestita fornisce un set di strumenti avanzati che consentono di [monitorare e ottimizzare il carico di lavoro](../database/monitor-tune-overview.md). [Analisi SQL di Azure](../../azure-monitor/insights/azure-sql.md) consente di monitorare un ampio set di istanze gestite e centralizzare il monitoraggio di un numero elevato di istanze e database. L' [ottimizzazione automatica](/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) nelle istanze gestite monitora continuamente le prestazioni delle statistiche di esecuzione del piano SQL e corregge automaticamente i problemi di prestazioni identificati.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui Istanza gestita SQL di Azure, vedere [che cos'è istanza gestita SQL di Azure?](sql-managed-instance-paas-overview.md).
- Per un'esercitazione con un ripristino da backup, vedere [Creare un'istanza gestita](instance-create-quickstart.md).
- Per un'esercitazione sulla migrazione con il servizio migrazione del database, vedere [eseguire la migrazione del database locale in Azure SQL istanza gestita usando il servizio migrazione del database](../../dms/tutorial-sql-server-to-managed-instance.md).