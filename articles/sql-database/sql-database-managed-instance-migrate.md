---
title: Eseguire la migrazione da SQL Server sql Server all'istanza gestitaMigrate from SQL Server to managed instance
description: Informazioni su come eseguire la migrazione di un database dall'istanza di SQL Server al database SQL di Azure - Istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208935"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure

Questo articolo illustra i metodi disponibili per eseguire la migrazione di un'istanza di SQL Server 2005 o versione successiva a un'[istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Per informazioni sulla migrazione a un database singolo o a un pool elastico, vedere [Migrate to a single or pooled database](sql-database-cloud-migrate.md) (Eseguire la migrazione a un database singolo o in pool). Per informazioni sulla migrazione da altre piattaforme, vedere [Azure Database Migration Guide](https://datamigration.microsoft.com/) (Guida alla migrazione di database di Azure).

> [!NOTE]
> Se si desidera avviare e provare rapidamente l'istanza gestita, è possibile passare alla [Guida rapida](sql-database-managed-instance-quickstart-guide.md) anziché a questa pagina. 

A livello generale, il processo di migrazione del database è simile a quello indicato di seguito:

![Processo di migrazione](./media/sql-database-managed-instance-migration/migration-process.png)

- [Valutare la compatibilità delle istanze gestite](#assess-managed-instance-compatibility) in cui è necessario assicurarsi che non vi siano problemi di blocco che possono impedire le migrazioni.
  - Questo passaggio include anche la creazione della linea di [base delle prestazioni](#create-performance-baseline) per determinare l'utilizzo delle risorse nell'istanza di SQL Server di origine. Questo passaggio è necessario se si desidera distribuire un'istanza gestita di dimensioni corrette e verificare che le prestazioni dopo la migrazione non siano interessate.
- [Scegliere le opzioni di connettività delle app](sql-database-managed-instance-connect-app.md)
- [Eseguire la distribuzione in un'istanza gestita](#deploy-to-an-optimally-sized-managed-instance) di dimensioni ottimali in cui è possibile scegliere le caratteristiche tecniche (numero di vCore, quantità di memoria) e il livello di prestazioni (Business Critical, General Purpose) dell'istanza gestita.
- [Selezionare il metodo](#select-migration-method-and-migrate) di migrazione ed eseguire la migrazione nel punto in cui si esegue la migrazione dei database utilizzando la migrazione offline (backup/ripristino nativo, importazione/esportazione del database/esportazione) o la migrazione online (Servizio migrazione dati, Replica transazionale).
- [Monitorare le applicazioni](#monitor-applications) per assicurarsi che le prestazioni previste siano le prestazioni previste.

> [!NOTE]
> Per eseguire la migrazione di un database singolo in un database singolo o un pool di database elastico, vedere l'articolo relativo alla [migrazione di un database di SQL Server nel database SQL di Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Valutare la compatibilità dell'istanza gestita

Per prima cosa, determinare se l'istanza gestita è compatibile con i requisiti di database dell'applicazione. L'opzione di distribuzione dell'istanza gestita è progettata per consentire una facile migrazione in modalità lift-and-shift della maggior parte delle applicazioni che usano SQL Server in locale o in macchine virtuali. Talvolta, tuttavia, possono essere necessarie caratteristiche o funzionalità non ancora supportate e il costo di implementazione di una soluzione alternativa è troppo elevato.

Per rilevare i potenziali problemi di compatibilità che influirebbero sulle funzionalità dei database nel database SQL di Azure, usare [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview). DMA non supporta ancora un'istanza gestita come destinazione della migrazione, ma è consigliabile eseguire una valutazione rispetto al database SQL di Azure e confrontare attentamente l'elenco dei problemi di compatibilità e parità di funzionalità segnalati con la documentazione del prodotto. Vedere [Funzionalità di database SQL di Azure](sql-database-features.md) per controllare se sono stati segnalati problemi di blocco non presenti nell'istanza gestita poiché la maggior parte dei problemi che impediscono la migrazione al database SQL di Azure è stata risolta con l'istanza gestita. Nelle istanze gestite, ad esempio, sono disponibili funzionalità come le query tra database, le transazioni tra database nella stessa istanza, il server collegato per altre origini SQL, CLR, le tabelle temporanee globali, le viste a livello di istanza, Service Broker e così via.

Se sono presenti problemi che causano un blocco che non sono stati risolti con l'opzione di distribuzione dell'istanza gestita, può essere necessario prendere in considerazione un'opzione alternativa, ad esempio [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Di seguito sono riportati alcuni esempi:

- Se è necessario l'accesso diretto al sistema operativo o al file system, ad esempio per installare agenti personalizzati o di terze parti nella stessa macchina virtuale con SQL Server.
- Se è presente una stretta dipendenza da funzionalità non ancora supportate, come FileStream/FileTable, PolyBase e le transazioni tra istanze.
- Se assolutamente necessario rimanere in una versione specifica di SQL Server (2012, per esempio).
- Se i requisiti di calcolo sono nettamente inferiori rispetto a quanto offerto dall'istanza gestita, ad esempio, un vCore, e il consolidamento di database non è un'opzione accettabile.

Se sono stati risolti tutti i blocchi di migrazione identificati e si continua la migrazione all'istanza gestita, tenere presente che alcune delle modifiche potrebbero influire sulle prestazioni del carico di lavoro:
- Il modello di recupero con registrazione completa obbligatorio e la normale pianificazione del backup automatico potrebbero influire sulle prestazioni del carico di lavoro o sulle azioni di manutenzione/ETL se sono stati utilizzati periodicamente modelli con registrazione minima/bulk o sono stati interrotti i backup su richiesta.
- Configurazioni a livello di server o di database diverse, ad esempio flag di traccia o livelli di compatibilità
- Le nuove funzionalità in uso, ad esempio i gruppi TDE (Transparent Database Encryption) o di failover automatico, potrebbero influire sull'utilizzo della CPU e dell'I/O.

L'istanza gestita garantisce la disponibilità al 99,99% anche negli scenari critici, pertanto l'overhead causato da queste funzionalità non può essere disabilitato. Per ulteriori informazioni, vedere [le cause principali che potrebbero causare prestazioni diverse in SQL Server e nell'istanza gestita](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Creare una linea di base delle prestazioni

Se è necessario confrontare le prestazioni del carico di lavoro nell'istanza gestita con il carico di lavoro originale in esecuzione in SQL Server, è necessario creare una linea di base delle prestazioni che verrà utilizzata per il confronto. 

La linea di base delle prestazioni è un set di parametri, ad esempio l'utilizzo medio/massimo della CPU, la latenza media/massima delle operazioni di I/O al secondo, la velocità effettiva, le operazioni di I/O al secondo, la speranza di vita media/massima della pagina, la dimensione massima media del tempdb. Si desidera avere parametri simili o anche migliori dopo la migrazione, pertanto è importante misurare e registrare i valori di base per questi parametri. Oltre ai parametri di sistema, è necessario selezionare un set di query rappresentative o le query più importanti nel carico di lavoro e misurare la durata min/media/massima, l'utilizzo della CPU per le query selezionate. Questi valori consentono di confrontare le prestazioni del carico di lavoro in esecuzione nell'istanza gestita con i valori originali nell'istanza di origine.

Alcuni dei parametri che è necessario misurare nell'istanza di SQL Server sono: 
- [Monitorare l'utilizzo della CPU nell'istanza](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) di SQL Server e registrare l'utilizzo medio e massimo della CPU.
- [Monitorare l'utilizzo](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) della memoria nell'istanza di SQL Server e determinare la quantità di memoria utilizzata da diversi componenti, ad esempio pool di buffer, cache dei piani, pool di archivi di colonne, [OLTP in memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)e così via. Inoltre, è necessario trovare i valori medi e massimi del contatore delle prestazioni della memoria Aspettativa di vita della pagina.
- Monitorare l'utilizzo delle operazioni di I/O del disco nell'istanza di SQL Server di origine utilizzando la visualizzazione [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) o [i contatori delle prestazioni.](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)
- Monitorare il carico di lavoro e le prestazioni delle query o l'istanza di SQL Server esaminando le viste a gestione dinamica o l'archivio query se si esegue la migrazione dalla versione di SQL Server 2016. Identificare la durata media e l'utilizzo della CPU delle query più importanti nel carico di lavoro per confrontarle con le query in esecuzione nell'istanza gestita.

> [!Note]
> Se si nota noto qualsiasi problema con il carico di lavoro in SQL Server, ad esempio un utilizzo elevato della CPU, pressione costante della memoria, tempdb o problemi di parametrizzazione, è consigliabile provare a risolverli nell'istanza di SQL Server di origine prima di prendere la linea di base e la migrazione. La migrazione di problemi noti a qualsiasi nuovo sistema causano risultati imprevisti e invalidano qualsiasi confronto delle prestazioni.

Come risultato di questa attività è necessario avere documentato i valori medi e massimi per l'utilizzo di CPU, memoria e I/O nel sistema di origine, nonché la durata media e massima e l'utilizzo della CPU delle query dominanti e più critiche nel carico di lavoro. È consigliabile usare questi valori in un secondo momento per confrontare le prestazioni del carico di lavoro nell'istanza gestita con le prestazioni di base del carico di lavoro nell'istanza di origine di SQL ServerSQL Server.You should use these values later to compare performance of your workload on Managed Instance with the baseline performance of the workload on the source SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali

L'istanza gestita è progettata appositamente per carichi di lavoro locali che si intende spostare nel cloud. Introduce un [nuovo modello di acquisto](sql-database-service-tiers-vcore.md) che offre maggiore flessibilità nella selezione del livello appropriato di risorse per i carichi di lavoro. Nell'ambiente locale, si è probabilmente soliti dimensionare i carichi di lavoro usando core fisici o larghezza di banda di I/O. Il modello di acquisto dell'istanza gestita è basato sui core virtuali, o "vCore", con I/O e spazio di archiviazione aggiuntivi disponibili separatamente. Il modello basato su vCore semplifica la comprensione dei requisiti di calcolo nel cloud rispetto alle risorse usate attualmente in locale. Questo nuovo modello consente di dimensionare correttamente l'ambiente di destinazione nel cloud. Alcune linee guida generali che potrebbero aiutarti a scegliere il livello di servizio e le caratteristiche più semplici sono descritte di seguito:Some general guidelines that might help you to choose the right service tier and characteristics are described here:
- In base all'utilizzo della CPU di base è possibile eseguire il provisioning di un'istanza gestita che corrisponde al numero di core in uso in SQL Server, tenendo presente che potrebbe essere necessario ridimensionare le caratteristiche della CPU in modo che corrispondano alle [caratteristiche della macchina virtuale in cui è installata l'istanza gestita.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)
- In base all'utilizzo della memoria di base, scegliere [il livello di servizio con memoria corrispondente.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics) La quantità di memoria non può essere scelta direttamente, quindi è necessario selezionare l'istanza gestita con la quantità di vCore con memoria corrispondente (ad esempio 5,1 GB/vCore in Gen5). 
- In base alla latenza di base di I/O del file subsistema, scegliere tra Scopi generali (latenza maggiore di 5 ms) e livelli di servizio Business Critical (latenza inferiore a 3 ms).
- In base alla velocità effettiva di base preallocare le dimensioni dei file di dati o di log per ottenere le prestazioni di I/O previste.

È possibile scegliere le risorse di elaborazione e archiviazione in fase di distribuzione e quindi modificarle in un secondo momento senza introdurre tempi di inattività per l'applicazione tramite il portale di [Azure:You](sql-database-scale-resources.md)can choose compute and storage resources at deployment time and then changes afterward using downtime for your application using the Azure portal:

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

Dopo aver completato la migrazione all'istanza gestita, è necessario tenere traccia del comportamento dell'applicazione e delle prestazioni del carico di lavoro. Questo processo include le seguenti attività:
- [Confrontare le prestazioni del carico di lavoro in esecuzione nell'istanza gestita](#compare-performance-with-the-baseline) con la [linea di base delle prestazioni creata nell'istanza di origine di SQL Server.](#create-performance-baseline)
- Monitora continuamente [le prestazioni del carico di lavoro](#monitor-performance) per identificare potenziali problemi e miglioramenti.

### <a name="compare-performance-with-the-baseline"></a>Confrontare le prestazioni con la linea di base

La prima attività che è necessario eseguire immediatamente dopo la corretta migrazione consiste nel confrontare le prestazioni del carico di lavoro con le prestazioni di base del carico di lavoro. L'obiettivo di questa attività è verificare che le prestazioni del carico di lavoro nell'istanza gestita soddisfino le proprie esigenze. 

La migrazione del database all'istanza gestita mantiene le impostazioni del database e il livello di compatibilità originale nella maggior parte dei casi. Le impostazioni originali vengono mantenute ove possibile per ridurre il rischio di alcune riduzioni delle prestazioni rispetto a SQL Server di origine. Se il livello di compatibilità di un database utente prima della migrazione è 100 o un valore superiore, il livello rimane invariato dopo la migrazione. Se il livello di compatibilità di un database utente prima della migrazione è 90, nel database aggiornato viene impostato su 100, che è il livello di compatibilità minimo supportato in un'istanza gestita. Il livello di compatibilità dei database di sistema è 140. Poiché la migrazione all'istanza gestita esegue effettivamente la migrazione alla versione più recente del Motore di database di SQL ServerSQL Server Database Engine, è necessario tenere presente che è necessario testare nuovamente le prestazioni del carico di lavoro per evitare problemi di prestazioni sorprendenti.

Come prerequisito, assicurarsi di aver completato le attività seguenti:
- Allineare le impostazioni nell'istanza gestita con le impostazioni dell'istanza di SQL Server di origine analizzando varie impostazioni di istanza, database, temdb e configurazioni. Assicurarsi di non aver modificato impostazioni quali i livelli di compatibilità o la crittografia prima di eseguire il primo confronto delle prestazioni oppure accettare il rischio che alcune delle nuove funzionalità abilitate possano influire su alcune query. Per ridurre i rischi della migrazione, modificare il livello di compatibilità del database solo dopo il monitoraggio delle prestazioni.
- Implementare [le linee guida sulle procedure consigliate di archiviazione per scopi generali,](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) ad esempio la preallocazione delle dimensioni dei file per ottenere prestazioni migliori.
- Informazioni sulle principali differenze di [ambiente che potrebbero causare le differenze di prestazioni tra l'istanza gestita e SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) e identificare i rischi che potrebbero influire sulle prestazioni.
- Assicurarsi di mantenere abilitato l'archivio query e l'ottimizzazione automatica nell'istanza gestita. Queste funzionalità consentono di misurare le prestazioni del carico di lavoro e correggere automaticamente i potenziali problemi di prestazioni. Informazioni su come usare Query Store come strumento ottimale per ottenere informazioni sulle prestazioni del carico di lavoro prima e dopo la modifica del livello di compatibilità del database, come illustrato in [Mantenere la stabilità delle prestazioni durante l'aggiornamento alla versione più recente](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)di SQL Server.
Dopo aver preparato l'ambiente che è paragonabile il più possibile con l'ambiente locale, è possibile avviare l'esecuzione del carico di lavoro e misurare le prestazioni. Il processo di misurazione deve includere gli stessi parametri misurati durante la [creazione delle prestazioni di base delle misure del carico di lavoro nell'origine SQL Server.](#create-performance-baseline)
Di conseguenza, è necessario confrontare i parametri delle prestazioni con la linea di base e identificare le differenze critiche.

> [!NOTE]
> In many cases, you would not be able to get exactly matching performance on Managed Instance and SQL Server. L'istanza gestita è un motore di database di SQL Server, ma l'infrastruttura e la configurazione a disponibilità elevata nell'istanza gestita possono introdurre alcune differenze. Si potrebbe prevedere che alcune query sarebbero più veloci, mentre alcune altre potrebbero essere più lente. L'obiettivo del confronto consiste nel verificare che le prestazioni del carico di lavoro nell'istanza gestita corrispondano a quelle in SQL Server (in media) e identificare l'esistono query critiche con le prestazioni che non corrispondono alle prestazioni originali.

Il risultato del confronto delle prestazioni potrebbe essere:
- Workload performance on Managed Instance is aligned or better that the workload performance on SQL Server. In questo caso è stata confermata correttamente la corretta migrazione.
- La maggior parte dei parametri delle prestazioni e delle query nel carico di lavoro funzionano correttamente, con alcune eccezioni con prestazioni ridotte. In questo caso, è necessario identificare le differenze e la loro importanza. Se sono presenti alcune query importanti con prestazioni ridotte, è necessario esaminare i piani SQL sottostanti modificati o le query raggiungono alcuni limiti di risorse. L'attenuazione in questo caso potrebbe consistere nell'applicare alcuni suggerimenti alle query critiche (ad esempio il livello di compatibilità modificato, lo stimatore di cardinalità legacy) direttamente o utilizzando le guide di piano, ricostruire o creare statistiche e indici che potrebbero influire sui piani. 
- La maggior parte delle query è più lenta nell'istanza gestita rispetto all'istanza di origine. In questo caso provare a identificare le cause principali della differenza, ad esempio [il raggiungimento]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) di un limite di risorse come limiti di I/O, limite di memoria, limite di frequenza del log dell'istanza e così via. Se non sono presenti limiti di risorse che possono causare la differenza, provare a modificare il livello di compatibilità del database o modificare le impostazioni del database, ad esempio la stima della cardinalità legacy e riavviare il test. Esaminare i suggerimenti forniti dalle visualizzazioni Istanza gestita o Archivio query per identificare le query che hanno regredito le prestazioni.

> [!IMPORTANT]
> L'istanza gestita dispone di una funzionalità di correzione automatica del piano incorporata abilitata per impostazione predefinita. Questa funzionalità garantisce che le query che hanno funzionato correttamente nel incolla non si rigradino in futuro. Assicurarsi che questa funzionalità sia abilitata e che il carico di lavoro sia stato eseguito abbastanza a lungo con le impostazioni precedenti prima di modificare le nuove impostazioni per abilitare l'istanza gestita per ottenere informazioni sulle prestazioni e i piani di base.

Apportare la modifica dei parametri o aggiornare i livelli di servizio per convergere alla configurazione ottimale fino a ottenere le prestazioni del carico di lavoro che si adattaalle alle proprie esigenze.

### <a name="monitor-performance"></a>Monitorare le prestazioni

L'istanza gestita fornisce molti strumenti avanzati per il monitoraggio e la risoluzione dei problemi ed è consigliabile utilizzarli per monitorare le prestazioni dell'istanza. Alcuni dei parametri che è necessario monitorare sono:
- L'utilizzo della CPU nell'istanza per determinare il numero di vCore di cui è stato eseguito il provisioning è la corrispondenza corretta per il carico di lavoro.
- L'aspettativa di vita della pagina nell'istanza gestita per determinare [la memoria aggiuntiva.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)
- Statistiche di `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` attesa come o che vi dirà di avere problemi di I/O di archiviazione, in particolare sul livello di scopo generale in cui potrebbe essere necessario pre-allocare i file per ottenere migliori prestazioni di I/O.

## <a name="leverage-advanced-paas-features"></a>Sfrutta le funzionalità avanzate di PaaS

Dopo aver utilizzato una piattaforma completamente gestita e aver verificato che le prestazioni del carico di lavoro corrispondano alle prestazioni del carico di lavoro di SQL ServerSQL Server , adotta i vantaggi forniti automaticamente come parte del servizio di database SQL. 

Anche se non si apportano alcune modifiche nell'istanza gestita durante la migrazione, è possibile attivare alcune delle nuove funzionalità durante il funzionamento dell'istanza per sfruttare i miglioramenti più recenti del motore di database. Alcune modifiche vengono abilitate solo dopo la modifica del livello di compatibilità del [database.](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)


In un'istanza gestita, ad esempio, non è necessario creare backup, perché vengono creati automaticamente dal servizio, né preoccuparsi della pianificazione, dell'esecuzione e della gestione dei backup. Un'istanza gestita offre la possibilità di eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione con il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore). Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

Per rafforzare la sicurezza, prendere in considerazione l'utilizzo [dell'autenticazione](sql-database-security-overview.md)di Azure Active Directory , [controllo](sql-database-managed-instance-auditing.md), [rilevamento delle minacce](sql-database-advanced-data-security.md), sicurezza a livello di [riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)e [mascheramento dinamico dei dati](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) .

Oltre alle funzionalità avanzate di gestione e sicurezza, Managed Instance offre un set di strumenti avanzati che consentono di [monitorare e ottimizzare il carico di lavoro.](sql-database-monitor-tune-overview.md) [L'analisi SQL](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) di Azure consente di monitorare un set di istanze gestite di grandi dimensioni e di centralizzare il monitoraggio di un numero elevato di istanze e database. [L'ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) nell'istanza gestita monitora continuamente le prestazioni delle statistiche di esecuzione del piano SQL e risolve automaticamente i problemi di prestazioni identificati.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle istanze gestite, vedere [Informazioni sull'istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione con un ripristino da backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sulla migrazione con Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a un'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  
