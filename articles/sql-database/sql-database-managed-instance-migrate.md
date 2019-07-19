---
title: Eseguire la migrazione del database da un'istanza di SQL Server a un'istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione di un database da un'istanza di SQL Server a un'istanza gestita di database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
manager: craigg
ms.date: 11/07/2019
ms.openlocfilehash: 0fa65454702c67d4b0baeedc7f412ccec402ea46
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302299"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure

Questo articolo illustra i metodi disponibili per eseguire la migrazione di un'istanza di SQL Server 2005 o versione successiva a un'[istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Per informazioni sulla migrazione a un database singolo o a un pool elastico, vedere [Migrate to a single or pooled database](sql-database-cloud-migrate.md) (Eseguire la migrazione a un database singolo o in pool). Per informazioni sulla migrazione da altre piattaforme, vedere [Guida alla migrazione di database di Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Se si desidera avviare rapidamente e provare Istanza gestita, è possibile passare alla [Guida](sql-database-managed-instance-quickstart-guide.md) introduttiva invece di questa pagina. 

A livello generale, il processo di migrazione del database è simile a quello indicato di seguito:

![Processo di migrazione](./media/sql-database-managed-instance-migration/migration-process.png)

- [Valutare la compatibilità delle istanze gestite](#assess-managed-instance-compatibility) in cui è necessario assicurarsi che non ci siano problemi di blocco che possono impedire le migrazioni.
  - Questo passaggio include anche la creazione di baseline per le [prestazioni](#create-performance-baseline) per determinare l'utilizzo delle risorse nell'istanza di SQL Server di origine. Questo passaggio è necessario se si vuole distribuire o distribuire correttamente Istanza gestita e verificare che le prestazioni dopo la migrazione non siano interessate.
- [Scegliere le opzioni di connettività dell'app](sql-database-managed-instance-connect-app.md)
- [Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali in](#deploy-to-an-optimally-sized-managed-instance) cui si scelgono caratteristiche tecniche (numero di Vcore, quantità di memoria) e livello di prestazioni (business critical, per utilizzo generico) del istanza gestita.
- [Selezionare il metodo di migrazione e migrare](#select-migration-method-and-migrate) il percorso in cui si esegue la migrazione dei database tramite migrazione offline (backup/ripristino nativo, importazione/esportazione di database) o migrazione in linea (servizio migrazione dati, replica transazionale).
- [Monitorare le applicazioni](#monitor-applications) per assicurarsi di avere le prestazioni previste.

> [!NOTE]
> Per eseguire la migrazione di un database singolo in un database singolo o un pool di database elastico, vedere l'articolo relativo alla [migrazione di un database di SQL Server nel database SQL di Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Valutare la compatibilità dell'istanza gestita

Per prima cosa, determinare se l'istanza gestita è compatibile con i requisiti di database dell'applicazione. L'opzione di distribuzione dell'istanza gestita è progettata per consentire una facile migrazione in modalità lift-and-shift della maggior parte delle applicazioni che usano SQL Server in locale o in macchine virtuali. Talvolta, tuttavia, possono essere necessarie caratteristiche o funzionalità non ancora supportate e il costo di implementazione di una soluzione alternativa è troppo elevato.

Per rilevare i potenziali problemi di compatibilità che influirebbero sulle funzionalità dei database nel database SQL di Azure, usare [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview). DMA non supporta ancora un'istanza gestita come destinazione della migrazione, ma è consigliabile eseguire una valutazione rispetto al database SQL di Azure e confrontare attentamente l'elenco dei problemi di compatibilità e parità di funzionalità segnalati con la documentazione del prodotto. Vedere [Funzionalità di database SQL di Azure](sql-database-features.md) per controllare se sono stati segnalati problemi di blocco non presenti nell'istanza gestita poiché la maggior parte dei problemi che impediscono la migrazione al database SQL di Azure è stata risolta con l'istanza gestita. Nelle istanze gestite, ad esempio, sono disponibili funzionalità come le query tra database, le transazioni tra database nella stessa istanza, il server collegato per altre origini SQL, CLR, le tabelle temporanee globali, le viste a livello di istanza, Service Broker e così via.

Se sono presenti problemi che causano un blocco che non sono stati risolti con l'opzione di distribuzione dell'istanza gestita, può essere necessario prendere in considerazione un'opzione alternativa, ad esempio [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Ecco alcuni esempi:

- Se è necessario l'accesso diretto al sistema operativo o al file system, ad esempio per installare agenti personalizzati o di terze parti nella stessa macchina virtuale con SQL Server.
- Se è presente una stretta dipendenza da funzionalità non ancora supportate, come FileStream/FileTable, PolyBase e le transazioni tra istanze.
- Se è assolutamente necessario rimanere a una versione specifica di SQL Server (2012, ad esempio).
- Se i requisiti di calcolo sono nettamente inferiori rispetto a quanto offerto dall'istanza gestita, ad esempio, un vCore, e il consolidamento di database non è un'opzione accettabile.

Se sono stati risolti tutti i blocchi di migrazione identificati e si continua la migrazione a Istanza gestita, si noti che alcune delle modifiche potrebbero influire sulle prestazioni del carico di lavoro:
- Il modello di recupero con registrazione completa obbligatoria e la pianificazione regolare dei backup automatici potrebbero influito sulle prestazioni del carico di lavoro o sulle azioni di manutenzione/ETL se è stato usato periodicamente un modello con registrazione minima o con registrazione minima delle operazioni bulk oppure è stato interrotto
- Configurazioni a livello di server o di database diverse, ad esempio i flag di traccia o i livelli di compatibilità
- Le nuove funzionalità di, ad esempio Transparent Database Encryption (Transparent Database Encryption) o i gruppi di failover automatico potrebbero avere un effetto sull'utilizzo di CPU e IO.

Istanza gestita garantire la disponibilità del 99,99% anche negli scenari critici, di conseguenza l'overhead causato da queste funzionalità non può essere disabilitato. Per ulteriori informazioni, vedere [le cause principali che potrebbero causare diverse prestazioni in SQL Server e istanza gestita](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Crea linea di base prestazioni

Se è necessario confrontare le prestazioni del carico di lavoro in Istanza gestita con il carico di lavoro originale in esecuzione su SQL Server, è necessario creare un riferimento per le prestazioni che verrà usato per il confronto. 

La linea di base delle prestazioni è un set di parametri, ad esempio utilizzo medio/massimo CPU, latenza IO disco media/massima, velocità effettiva, IOPS, permanenza media/massima permanenza della pagina, dimensioni medie massime del tempdb. Si vuole avere parametri simili o ancora migliori dopo la migrazione, quindi è importante misurare e registrare i valori di base per questi parametri. Oltre ai parametri di sistema, è necessario selezionare un set di query rappresentative o le query più importanti nel carico di lavoro e misurare la durata minima/media/massima, l'utilizzo della CPU per le query selezionate. Questi valori consentono di confrontare le prestazioni del carico di lavoro in esecuzione in Istanza gestita ai valori originali nell'SQL Server di origine.

Di seguito sono elencati alcuni parametri che è necessario misurare nell'istanza di SQL Server: 
- [Monitorare l'utilizzo della CPU nell'istanza di SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e registrare l'utilizzo medio e massimo della CPU.
- [Monitorare l'utilizzo della memoria nell'istanza di SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) e determinare la quantità di memoria utilizzata da componenti diversi, ad esempio pool di buffer, cache dei piani, pool di archivio colonne, [OLTP in memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)e così via. Inoltre, è necessario trovare i valori medi e massimi del contatore delle prestazioni della memoria presunta delle pagine.
- Monitorare l'utilizzo di i/o su disco nell'istanza di SQL Server di origine utilizzando i [contatori delle prestazioni](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)o della vista [sys. dm _io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) .
- Monitorare il carico di lavoro e le prestazioni delle query o l'istanza di SQL Server esaminando viste a gestione dinamica o Query Store se si esegue la migrazione da SQL Server versione 2016 +. Identificare la durata media e l'utilizzo della CPU delle query più importanti nel carico di lavoro per confrontarle con le query in esecuzione nel Istanza gestita.

> [!Note]
> Se si riscontrano problemi con il carico di lavoro in SQL Server, ad esempio un utilizzo elevato della CPU, un numero costante di richieste di memoria, tempdb o parametrizzazione, è consigliabile provare a risolverli nell'istanza del SQL Server di origine prima di eseguire la migrazione e la baseline. La migrazione di informazioni sui problemi di qualsiasi nuovo migh di sistema causa risultati imprevisti e invalida qualsiasi confronto delle prestazioni.

Come risultato di questa attività, è necessario aver documentato i valori medi e massimi per l'utilizzo della CPU, della memoria e dell'i/o nel sistema di origine, nonché la durata media e massima e l'utilizzo della CPU delle query dominanti e più critiche nel carico di lavoro. Usare questi valori in un secondo momento per confrontare le prestazioni del carico di lavoro in Istanza gestita con le prestazioni di base del carico di lavoro nel SQL Server di origine.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali

L'istanza gestita è progettata appositamente per carichi di lavoro locali che si intende spostare nel cloud. Introduce un [nuovo modello di acquisto](sql-database-service-tiers-vcore.md) che offre maggiore flessibilità nella selezione del livello appropriato di risorse per i carichi di lavoro. Nell'ambiente locale, si è probabilmente soliti dimensionare i carichi di lavoro usando core fisici o larghezza di banda di I/O. Il modello di acquisto dell'istanza gestita è basato sui core virtuali, o "vCore", con I/O e spazio di archiviazione aggiuntivi disponibili separatamente. Il modello basato su vCore semplifica la comprensione dei requisiti di calcolo nel cloud rispetto alle risorse usate attualmente in locale. Questo nuovo modello consente di dimensionare correttamente l'ambiente di destinazione nel cloud. Alcune linee guida generali che possono essere utili per scegliere il livello di servizio e le caratteristiche corretti sono descritte di seguito:
- In base all'utilizzo della CPU di base, è possibile effettuare il provisioning di un Istanza gestita corrispondente al numero di core usati in SQL Server, tenendo presente che è possibile che le caratteristiche della CPU debbano essere ridimensionate in modo da corrispondere alle [caratteristiche della macchina virtuale in cui è installato istanza gestita ](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- In base all'utilizzo della memoria di base, scegliere [il livello di servizio con memoria corrispondente](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). La quantità di memoria non può essere scelta direttamente, quindi è necessario selezionare la Istanza gestita con la quantità di Vcore con memoria corrispondente (ad esempio, 5,1 GB/vCore in quinta generazione). 
- In base alla latenza di i/o di base del sottosistema di file, scegliere tra per utilizzo generico (latenza maggiore di 5 ms) e business critical livelli di servizio (latenza inferiore a 3 ms).
- In base alla velocità effettiva di base, pre-allocare le dimensioni dei file di dati o di log per ottenere le prestazioni di i/o previste.

È possibile scegliere risorse di calcolo e di archiviazione in fase di distribuzione e quindi modificarle successivamente senza introdurre tempi di inattività per l'applicazione usando il [portale di Azure](sql-database-scale-resources.md):

![Dimensionamento dell'istanza gestita](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Per informazioni su come creare l'infrastruttura di rete virtuale e un'istanza gestita, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).

> [!IMPORTANT]
> È importante che la rete virtuale e la subnet di destinazione siano sempre conformi ai [requisiti per la rete virtuale dell'istanza gestita](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Qualsiasi incompatibilità può impedire la creazione di nuove istanze o l'uso di quelle già create. Altre informazioni sulle [creazione di nuove reti](sql-database-managed-instance-create-vnet-subnet.md) e [la configurazione di reti esistenti](sql-database-managed-instance-configure-vnet-subnet.md).

## <a name="select-migration-method-and-migrate"></a>Selezionare il metodo ed eseguire la migrazione

L'opzione di distribuzione dell'istanza gestita è destinata a scenari utente che richiedono la migrazione di massa di database da implementazioni di database locali o IaaS. Questa opzione è ottimale quando è necessario trasferire in modalità lift-and-shift il back-end di applicazioni che usano regolarmente funzionalità a livello di istanza e/o tra database. In questo scenario, è possibile spostare un'intera istanza in un ambiente corrispondente in Azure senza dover ridefinire l'architettura delle applicazioni.

Per spostare istanze di SQL, è necessario pianificare accuratamente quanto segue:

- La migrazione di tutti i database da collocare (quelli in esecuzione nella stessa istanza)
- La migrazione degli oggetti a livello di istanza da cui dipende l'applicazione, quali account di accesso, credenziali, operatori e processi di SQL Agent e trigger a livello di server.

L'istanza gestita è un servizio gestito che consente di delegare alcune delle regolari attività di amministrazione di database alla piattaforma perché sono predefinite. Di conseguenza, non è necessario eseguire la migrazione di alcuni dati a livello di istanza, come i processi di manutenzione per i backup regolari o la configurazione Always On, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

L'istanza gestita supporta le opzioni di migrazione di database descritte di seguito, che attualmente sono gli unici metodi di migrazione supportati.

- Servizio Migrazione del database di Azure: migrazione con tempo di inattività prossimo a zero
- `RESTORE DATABASE FROM URL` nativo: usa backup nativi di SQL Server e comporta un tempo di inattività

### <a name="azure-database-migration-service"></a>Servizio Migrazione del database di Azure

[Servizio Migrazione del database di Azure](../dms/dms-overview.md) è un servizio completamente gestito progettato per consentire migrazioni senza problemi da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti in Azure. Le opzioni di distribuzione in anteprima pubblica includono i database in Database SQL di Azure e i database SQL Server in una macchina virtuale di Azure. Servizio Migrazione del database è il metodo di migrazione consigliato per i carichi di lavoro aziendali.

Se si usa SQL Server Integration Services (SSIS) in SQL Server in locale, il Servizio Migrazione del database non supporta ancora la migrazione del catalogo SSIS (SSISDB) in cui sono archiviati i pacchetti SSIS, ma è possibile eseguire il provisioning del runtime di integrazione Azure-SSIS in Azure Data Factory, che creerà un nuovo SSISDB in un'istanza gestita. Sarà quindi possibile ridistribuire i pacchetti su di esso. Vedere [Creare il runtime di integrazione SSIS di Azure in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Per altre informazioni su questo scenario e sulla procedura di configurazione per Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a un'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Ripristino nativo da URL

Il ripristino di backup nativi (file con estensione bak) ottenuti da SQL Server in locale o [SQL Server in macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/) e disponibili in [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) è una delle funzionalità principali dell'opzione di distribuzione dell'istanza gestita e consente una migrazione di database offline semplice e rapida.

Il diagramma seguente offre una panoramica di alto livello del processo:

![Flusso della migrazione](./media/sql-database-managed-instance-migration/migration-flow.png)

La tabella seguente contiene altre informazioni sul metodo che è possibile usare a seconda della versione di SQL Server di origine eseguita:

|Passaggio|Versione e motore SQL|Metodo di backup/ripristino|
|---|---|---|
|Inserire il backup in Archiviazione di Azure|Precedente SQL 2012 SP1 CU2|Caricamento diretto del file con estensione bak in Archiviazione di Azure|
||2012 SP1 CU2 - 2016|Backup diretto con sintassi [con credenziali](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) deprecata|
||2016 e versioni successive|Backup diretto [con credenziali di firma di accesso condiviso](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Eseguire il ripristino da Archiviazione di Azure in un'istanza gestita|[Ripristino da URL con credenziali di firma di accesso condiviso](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - In caso di migrazione di un database protetto tramite [Transparent Data Encryption](transparent-data-encryption-azure-sql.md) a un'istanza gestita con l'opzione del ripristino nativo, prima di ripristinare il database è necessario eseguire la migrazione del certificato corrispondente dall'istanza locale o IaaS di SQL Server. Per i dettagli, vedere [Migrazione del certificato TDE a un'istanza gestita](sql-database-managed-instance-migrate-tde-certificate.md)
> - Il ripristino di database di sistema non è supportato. Per eseguire la migrazione di oggetti a livello di istanza (archiviati in database master o msdb), è consigliabile inserirli in script ed eseguire gli script T-SQL nell'istanza di destinazione.

Per una guida di avvio rapido che descrive come eseguire il ripristino del backup di un database in un'istanza gestita con credenziali di firma di accesso condiviso, vedere l'articolo su come [eseguire il ripristino da un backup in un'istanza gestita](sql-database-managed-instance-get-started-restore.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Monitorare le applicazioni

Una volta completata la migrazione a Istanza gestita, è necessario tenere traccia del comportamento e delle prestazioni dell'applicazione del carico di lavoro. Questo processo include le attività seguenti:
- [Confrontare le prestazioni del carico di lavoro in esecuzione nel istanza gestita](#compare-performance-with-the-baseline) con la [baseline delle prestazioni creata nell'SQL Server di origine](#create-performance-baseline).
- [Monitora continuamente le prestazioni del tuo carico di lavoro](#monitor-performance) per identificare i potenziali problemi e i miglioramenti.

### <a name="compare-performance-with-the-baseline"></a>Confrontare le prestazioni con la linea di base

La prima attività che dovrebbe essere eseguita immediatamente dopo la migrazione è confrontare le prestazioni del carico di lavoro con le prestazioni di base del carico di lavoro. L'obiettivo di questa attività consiste nel verificare che le prestazioni del carico di lavoro nel Istanza gestita soddisfino le proprie esigenze. 

La migrazione del database a Istanza gestita mantiene le impostazioni del database e il livello di compatibilità originale nella maggior parte dei casi. Le impostazioni originali vengono mantenute laddove possibile per ridurre il rischio di riduzioni delle prestazioni rispetto all'SQL Server di origine. Se il livello di compatibilità di un database utente prima della migrazione è 100 o un valore superiore, il livello rimane invariato dopo la migrazione. Se il livello di compatibilità di un database utente prima della migrazione è 90, nel database aggiornato viene impostato su 100, che è il livello di compatibilità minimo supportato in un'istanza gestita. Il livello di compatibilità dei database di sistema è 140. Poiché la migrazione a Istanza gestita sta effettivamente migrando alla versione più recente di SQL Server motore di database, è necessario tenere presente che è necessario testare di nuovo le prestazioni del carico di lavoro per evitare problemi di prestazioni sorprendenti.

Come prerequisito, verificare di aver completato le attività seguenti:
- Allineare le impostazioni in Istanza gestita con le impostazioni dell'istanza SQL Server di origine esaminando varie istanze, database, impostazioni di database tempdb e configurazioni. Assicurarsi di non aver modificato le impostazioni, ad esempio i livelli di compatibilità o la crittografia, prima di eseguire il primo confronto delle prestazioni o di accettare il rischio che alcune delle nuove funzionalità abilitate potrebbero influire su alcune query. Per ridurre i rischi della migrazione, modificare il livello di compatibilità del database solo dopo il monitoraggio delle prestazioni.
- Implementare le [linee guida per le procedure consigliate di archiviazione per per utilizzo generico](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) come pre-allocare le dimensioni dei file per ottenere prestazioni migliori.
- Informazioni sulle [differenze di ambiente principali che potrebbero causare le differenze di prestazioni tra istanza gestita e SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) e identificare i rischi che potrebbero influire sulle prestazioni.
- Assicurarsi di rimanere abilitati Query Store e l'ottimizzazione automatica nel Istanza gestita. Queste funzionalità consentono di misurare le prestazioni dei carichi di lavoro e di correggere automaticamente i potenziali problemi di prestazioni. Informazioni su come usare Query Store come strumento ottimale per ottenere informazioni sulle prestazioni del carico di lavoro prima e dopo la modifica del livello di compatibilità del database, come illustrato in [garantire la stabilità delle prestazioni durante l'aggiornamento alla versione più recente del SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Una volta preparato l'ambiente che è paragonabile al più possibile con l'ambiente locale, è possibile avviare l'esecuzione del carico di lavoro e misurare le prestazioni. Il processo di misurazione deve includere gli stessi parametri misurati [mentre si creano le prestazioni di base delle misure del carico di lavoro nel SQL Server di origine](#create-performance-baseline).
Di conseguenza, è consigliabile confrontare i parametri delle prestazioni con la linea di base e identificare le differenze critiche.

> [!NOTE]
> In molti casi, non è possibile ottenere esattamente le prestazioni corrispondenti in Istanza gestita e SQL Server. Istanza gestita è un motore di database SQL Server, ma la configurazione di infrastruttura e disponibilità elevata in Istanza gestita può presentare una certa differenza. Ci si potrebbe aspettare che alcune query risultino più veloci mentre altre potrebbero risultare più lente. L'obiettivo del confronto consiste nel verificare che le prestazioni del carico di lavoro in Istanza gestita corrispondano alle prestazioni in SQL Server (in media) e identificare siano presenti query critiche con le prestazioni che non corrispondono alle prestazioni originali.

Il risultato del confronto delle prestazioni potrebbe essere:
- Le prestazioni dei carichi di lavoro su Istanza gestita sono allineate o migliori rispetto alle prestazioni del carico di lavoro in SQL Server In questo caso è stato confermato che la migrazione è stata completata correttamente.
- La maggior parte dei parametri delle prestazioni e delle query nel carico di lavoro funziona correttamente, con alcune eccezioni con prestazioni ridotte. In questo caso, è necessario identificare le differenze e la loro importanza. Se sono presenti alcune query importanti con prestazioni ridotte, è necessario esaminare i piani SQL sottostanti modificati o le query che raggiungono alcuni limiti di risorse. In questo caso la mitigazione può essere l'applicazione di alcuni suggerimenti sulle query critiche, ad esempio il livello di compatibilità modificato, lo strumento di stima della cardinalità legacy, direttamente o utilizzando guide di piano, ricompilare o creare statistiche e indici che potrebbero influire sui piani. 
- La maggior parte delle query è più lenta in Istanza gestita rispetto al SQL Server di origine. In questo caso, provare a identificare le cause principali della differenza, ad esempio il raggiungimento di [un limite di risorse]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) , ad esempio i limiti di i/o, il limite di memoria, il limite di frequenza dei log delle Se non sono previsti limiti per le risorse che possono causare la differenza, provare a modificare il livello di compatibilità del database o modificare le impostazioni del database, ad esempio la stima della cardinalità legacy e riavviare il test. Esaminare le raccomandazioni fornite dalle viste Istanza gestita o Query Store per identificare le query che hanno reimpostato le prestazioni.

> [!IMPORTANT]
> Istanza gestita dispone di funzionalità predefinite di correzione automatica dei piani abilitata per impostazione predefinita. Questa funzionalità garantisce che le query che hanno funzionato correttamente nell'Incolla non peggiorano in futuro. Verificare che questa funzionalità sia abilitata e che il carico di lavoro sia stato eseguito abbastanza a lungo con le impostazioni precedenti prima di modificare le nuove impostazioni per consentire Istanza gestita di ottenere informazioni sulle prestazioni e sui piani di base.

Apportare la modifica dei parametri o aggiornare i livelli di servizio per convergere alla configurazione ottimale fino a quando non si ottengono le prestazioni del carico di lavoro adatte alle proprie esigenze.

### <a name="monitor-performance"></a>Monitorare le prestazioni

Istanza gestita offre numerosi strumenti avanzati per il monitoraggio e la risoluzione dei problemi ed è consigliabile usarli per monitorare le prestazioni dell'istanza. Alcuni dei parametri che devono essere monitorati sono:
- Utilizzo della CPU nell'istanza di per determinare il numero di vcore di cui è stato effettuato il provisioning è la corrispondenza giusta per il carico di lavoro.
- Permanenza presunta delle pagine nel Istanza gestita per determinare [se è necessaria memoria aggiuntiva](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Le statistiche di `INSTANCE_LOG_GOVERNOR` attesa `PAGEIOLATCH` come o che indicano se si verificano problemi di i/o di archiviazione, soprattutto nel livello per utilizzo generico in cui potrebbe essere necessario pre-allocare i file per ottenere migliori prestazioni di i/o.

## <a name="leverage-advanced-paas-features"></a>Sfruttare le funzionalità avanzate di PaaS

Quando si è in una piattaforma completamente gestita e si è verificato che le prestazioni del carico di lavoro corrispondono a SQL Server prestazioni del carico di lavoro, è possibile sfruttare i vantaggi offerti automaticamente come parte del servizio di database SQL. 

Anche se non si apportano modifiche all'istanza gestita durante la migrazione, è probabile che si accendano alcune delle nuove funzionalità mentre si sta operando l'istanza per sfruttare i vantaggi offerti dai miglioramenti più recenti del motore di database. Alcune modifiche sono abilitate solo dopo la [modifica del livello di compatibilità del database](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


In un'istanza gestita, ad esempio, non è necessario creare backup, perché vengono creati automaticamente dal servizio, né preoccuparsi della pianificazione, dell'esecuzione e della gestione dei backup. Un'istanza gestita offre la possibilità di eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione con il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore). Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

Per rafforzare la sicurezza, è consigliabile usare [Azure Active Directory autenticazione](sql-database-security-overview.md), il [controllo](sql-database-managed-instance-auditing.md), il [rilevamento delle minacce](sql-database-advanced-data-security.md), la [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)e la [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) .

Oltre alle funzionalità avanzate di gestione e sicurezza, Istanza gestita fornisce un set di strumenti avanzati che consentono di [monitorare e ottimizzare il carico di lavoro](sql-database-monitor-tune-overview.md). [Analisi SQL di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) consente di monitorare un ampio set di istanze gestite e centralizzare il monitoraggio di un numero elevato di istanze e database. L' [ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in istanza gestita monitorare continuamente le prestazioni delle statistiche di esecuzione del piano SQL e correggere automaticamente i problemi di prestazioni identificati.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle istanze gestite, vedere [Informazioni sull'istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione con un ripristino da backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sulla migrazione con Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a un'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  
