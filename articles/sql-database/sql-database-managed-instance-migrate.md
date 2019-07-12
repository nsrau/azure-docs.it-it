---
title: Eseguire la migrazione di database dall'istanza di SQL Server al Database SQL di Azure - istanza gestita | Microsoft Docs
description: Informazioni su come eseguire la migrazione di un database dall'istanza di SQL Server al Database SQL di Azure - istanza gestita.
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
ms.openlocfilehash: 7cf54b79fac87905117e321574571890c59315e6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827070"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure

Questo articolo illustra i metodi disponibili per eseguire la migrazione di un'istanza di SQL Server 2005 o versione successiva a un'[istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Per informazioni sulla migrazione a un database singolo o a un pool elastico, vedere [Migrate to a single or pooled database](sql-database-cloud-migrate.md) (Eseguire la migrazione a un database singolo o in pool). Per informazioni sulla migrazione da altre piattaforme, vedere [Guida alla migrazione di database di Azure](https://datamigration.microsoft.com/).

> [!NOTE]
> Se si desidera iniziare rapidamente e provare a istanza gestita, si potrebbe voler passare a [Guida introduttiva](/sql-database-managed-instance-quickstart-guide.md) invece questa pagina. 

A livello generale, il processo di migrazione del database è simile a quello indicato di seguito:

![Processo di migrazione](./media/sql-database-managed-instance-migration/migration-process.png)

- [Valutare la compatibilità di istanza gestita](#assess-managed-instance-compatibility) in cui è necessario assicurarsi che vi siano problemi di blocco che potrebbero impedire le migrazioni.
  - Questo passaggio prevede anche la creazione di [baseline delle prestazioni](#create-performance-baseline) per determinare l'utilizzo di risorse nell'istanza di SQL Server di origine. Questo passaggio è necessario se si desidera o distribuire l'istanza gestita di dimensioni in modo corretto e verificare che non sono interessate le prestazioni dopo la migrazione.
- [Scegliere le opzioni di connettività delle app](sql-database-managed-instance-connect-app.md)
- [Distribuire in un'istanza gestita di dimensioni ottimali](#deploy-to-an-optimally-sized-managed-instance) in cui si sceglierà caratteristiche tecniche (numero di Vcore, quantità di memoria) e il livello di prestazioni (Business Critical, utilizzo generico) dell'istanza gestita.
- [Selezionare il metodo ed eseguire la migrazione](#select-migration-method-and-migrate) in cui si esegue la migrazione dei database tramite migrazione offline (backup e ripristino native, importe/esportazione di database) o la migrazione online (servizio migrazione del database, la replica transazionale).
- [Monitorare le applicazioni](#monitor-applications) per garantire che si hanno prestazioni previste.

> [!NOTE]
> Per eseguire la migrazione di un database singolo in un database singolo o un pool di database elastico, vedere l'articolo relativo alla [migrazione di un database di SQL Server nel database SQL di Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Valutare la compatibilità dell'istanza gestita

Per prima cosa, determinare se l'istanza gestita è compatibile con i requisiti di database dell'applicazione. L'opzione di distribuzione dell'istanza gestita è progettata per consentire una facile migrazione in modalità lift-and-shift della maggior parte delle applicazioni che usano SQL Server in locale o in macchine virtuali. Talvolta, tuttavia, possono essere necessarie caratteristiche o funzionalità non ancora supportate e il costo di implementazione di una soluzione alternativa è troppo elevato.

Per rilevare i potenziali problemi di compatibilità che influirebbero sulle funzionalità dei database nel database SQL di Azure, usare [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview). DMA non supporta ancora un'istanza gestita come destinazione della migrazione, ma è consigliabile eseguire una valutazione rispetto al database SQL di Azure e confrontare attentamente l'elenco dei problemi di compatibilità e parità di funzionalità segnalati con la documentazione del prodotto. Vedere [Funzionalità di database SQL di Azure](sql-database-features.md) per controllare se sono stati segnalati problemi di blocco non presenti nell'istanza gestita poiché la maggior parte dei problemi che impediscono la migrazione al database SQL di Azure è stata risolta con l'istanza gestita. Nelle istanze gestite, ad esempio, sono disponibili funzionalità come le query tra database, le transazioni tra database nella stessa istanza, il server collegato per altre origini SQL, CLR, le tabelle temporanee globali, le viste a livello di istanza, Service Broker e così via.

Se sono presenti problemi che causano un blocco che non sono stati risolti con l'opzione di distribuzione dell'istanza gestita, può essere necessario prendere in considerazione un'opzione alternativa, ad esempio [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Ecco alcuni esempi:

- Se è necessario l'accesso diretto al sistema operativo o al file system, ad esempio per installare agenti personalizzati o di terze parti nella stessa macchina virtuale con SQL Server.
- Se è presente una stretta dipendenza da funzionalità non ancora supportate, come FileStream/FileTable, PolyBase e le transazioni tra istanze.
- Se assolutamente necessario mantenere una versione specifica di SQL Server (2012, ad esempio).
- Se i requisiti di calcolo sono nettamente inferiori rispetto a quanto offerto dall'istanza gestita, ad esempio, un vCore, e il consolidamento di database non è un'opzione accettabile.

Se è stata risolta tutti identificati blocchi migrazione e continuare la migrazione a istanza gestita, si noti che alcune modifiche potrebbero influire sulle prestazioni del carico di lavoro:
- Modello di recupero con registrazione completa obbligatorio e pianificazione del backup automatico regolare potrebbe compromettere le prestazioni del carico di lavoro o operazioni di manutenzione/ETL periodicamente usato modello semplice/bulk-logged o arrestato i backup su richiesta.
- Configurazioni a livello server o database diverse, ad esempio i flag di traccia o livelli di compatibilità
- Nuove funzionalità che si sta utilizzando, ad esempio gruppi di failover automatico o Transparent Database Encryption (TDE) potrebbe compromettere l'utilizzo della CPU e i/o.

Gestita garanzia del 99,99% disponibilità dell'istanza anche negli scenari critici, quindi non può essere disabilitata l'overhead causato da queste funzionalità. Per altre informazioni, vedere [cause principali che potrebbero causare diversi di prestazioni in SQL Server e istanza gestita](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/).

### <a name="create-performance-baseline"></a>Creare una baseline delle prestazioni

Se si desidera confrontare le prestazioni del carico di lavoro sull'istanza gestita con il carico di lavoro originale in esecuzione in SQL Server, è necessario creare una baseline delle prestazioni che verrà utilizzata per il confronto. 

La baseline delle prestazioni è un set di parametri, ad esempio utilizzo della CPU medio/massimo, il disco di average/max latenza dei / o e velocità effettiva, numero di IOPS, la permanenza presunta della pagina average/max, Media delle dimensioni massime del database tempdb. Vuoi avere parametri simili o meglio ancora, dopo la migrazione, pertanto è importante misurare e registrare i valori della linea di base per questi parametri. Oltre ai parametri di sistema, è necessario selezionare un set di query più importanti o le query rappresentative di durata min/average/max carichi di lavoro e misure, utilizzo della CPU per le query selezionate. Questi valori consente di confrontare le prestazioni del carico di lavoro in esecuzione in istanza gestita con i valori originali nell'origine SQL Server.

Alcuni dei parametri che è necessario misurare nell'istanza di SQL Server sono: 
- [Monitorare l'utilizzo della CPU nell'istanza di SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) e la media di record e i picchi di utilizzo della CPU.
- [Monitoraggio dell'utilizzo di memoria nell'istanza di SQL Server](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) e determinare la quantità di memoria utilizzata da componenti diversi, ad esempio pool di buffer, cache, il pool di ColumnStore, dei piani [OLTP In memoria](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)e così via. Inoltre, si devono trovare i valori di picco e medi del contatore delle prestazioni memoria permanenza presunta della pagina.
- Monitoraggio dell'utilizzo dei / o disco in istanza SQL Server di origine mediante [DM io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) visualizzazione oppure [i contatori delle prestazioni](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Monitorare le prestazioni del carico di lavoro e query o l'istanza di SQL Server tramite l'esame delle viste a gestione dinamica o Query Store se si esegue la migrazione dalla versione di SQL Server 2016 e versioni successive. Identificare durata media e utilizzo della CPU delle query più importanti nel carico di lavoro per eseguire un confronto con le query in esecuzione in istanza gestita.

> [!Note]
> Se si nota un problema con il carico di lavoro in SQL Server, ad esempio utilizzo elevato della CPU, utilizzo della memoria costante, tempdb o parametrizzazione problemi, è consigliabile provare a risolverli nell'istanza di SQL Server di origine prima di portare la linea di base e la migrazione. La migrazione di problemi noti per qualsiasi nuova migh sistema provocare risultati imprevisti e invalidano qualsiasi confronto delle prestazioni.

Nell'ambito di questa attività deve aver documentato Media e valori di picco per CPU, memoria e utilizzo dei / o nel proprio sistema di origine, nonché la durata media e massima e utilizzo della CPU del dominante e le query più importanti nel carico di lavoro. È necessario usare questi valori in un secondo momento per confrontare le prestazioni del carico di lavoro sull'istanza gestita con le prestazioni di base del carico di lavoro in SQL Server di origine.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali

L'istanza gestita è progettata appositamente per carichi di lavoro locali che si intende spostare nel cloud. Introduce un [nuovo modello di acquisto](sql-database-service-tiers-vcore.md) che offre maggiore flessibilità nella selezione del livello appropriato di risorse per i carichi di lavoro. Nell'ambiente locale, si è probabilmente soliti dimensionare i carichi di lavoro usando core fisici o larghezza di banda di I/O. Il modello di acquisto dell'istanza gestita è basato sui core virtuali, o "vCore", con I/O e spazio di archiviazione aggiuntivi disponibili separatamente. Il modello basato su vCore semplifica la comprensione dei requisiti di calcolo nel cloud rispetto alle risorse usate attualmente in locale. Questo nuovo modello consente di dimensionare correttamente l'ambiente di destinazione nel cloud. Linee guida generali che possono aiutarti a scegliere il livello di servizio corretto e le caratteristiche sono descritti di seguito:
- In base la linea di base dell'utilizzo della CPU è possibile eseguire il provisioning di un'istanza gestita che corrisponde al numero di core che si usa in SQL Server, con presente tale caratteristiche della CPU potrebbe essere necessario aumentare in modo che corrisponda [caratteristiche della macchina virtuale in cui l'istanza gestita è installato](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
- L'utilizzo della memoria della linea di base in base sceglie [il livello di servizio che disponga di memoria corrisponda](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics). La quantità di memoria non può essere scelta direttamente ed è quindi necessario selezionare l'istanza gestita con la quantità di Vcore che disponga di memoria corrisponda (ad esempio 5.1 GB/vCore in Gen5). 
- Basato sulla linea di base dei / o la latenza del sottosistema di file scegliere tra generico (latenza maggiore di 5 ms) e i livelli di servizio Business Critical (latenza inferiore a 3 ms).
- Basato sulla velocità effettiva della linea di base pre-allocare le dimensioni dei dati o i file di log per ottenere prestazioni dei / o previste.

È possibile scegliere di calcolo e le risorse di archiviazione in fase di distribuzione di tempo e quindi modificarle in seguito senza causare tempi di inattività per l'applicazione utilizzando il [portale di Azure](sql-database-scale-resources.md):

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

Dopo aver completato la migrazione a istanza gestita, è necessario rilevare il comportamento dell'applicazione e le prestazioni del carico di lavoro. Questo processo include le attività seguenti:
- [Confrontare le prestazioni del carico di lavoro in esecuzione in istanza gestita](#compare-performance-with-the-baseline) con il [baseline delle prestazioni che è stato creato in SQL Server di origine](#create-performance-baseline).
- In modo continuativo [monitorare le prestazioni del carico di lavoro](#monitor-performance) per identificare potenziali problemi e analisi utilizzo software.

### <a name="compare-performance-with-the-baseline"></a>Confrontare le prestazioni con la linea di base

La prima attività che è necessario effettuare immediatamente dopo il successo della migrazione consiste nel confrontare le prestazioni del carico di lavoro con le prestazioni del carico di lavoro di base. L'obiettivo di questa attività consiste nel confermare che le prestazioni del carico di lavoro per istanza gestita soddisfano le proprie esigenze. 

Migrazione del database a istanza gestita mantiene le impostazioni del database e del relativo livello di compatibilità originale nella maggior parte dei casi. Le impostazioni originali vengono mantenute dove possibile per ridurre il rischio di alcune riduzioni delle prestazioni rispetto all'origine SQL Server. Se il livello di compatibilità di un database utente prima della migrazione è 100 o un valore superiore, il livello rimane invariato dopo la migrazione. Se il livello di compatibilità di un database utente prima della migrazione è 90, nel database aggiornato viene impostato su 100, che è il livello di compatibilità minimo supportato in un'istanza gestita. Il livello di compatibilità dei database di sistema è 140. Poiché la migrazione a istanza gestita è effettivamente la migrazione alla versione più recente del motore di Database di SQL Server, è necessario tenere presente che è necessario ripetere il test delle prestazioni del carico di lavoro per evitare problemi di prestazioni insoliti.

Come prerequisito, assicurarsi di aver completato le attività seguenti:
- Allineare le impostazioni sull'istanza gestita con le impostazioni dall'istanza di SQL Server di origine con l'analisi dei vari istanza, database, le impostazioni tempdb e le configurazioni. Assicurarsi che non è stato modificato le impostazioni come i livelli di compatibilità o la crittografia prima di eseguire il confronto delle prestazioni prima oppure accettare il rischio che alcune delle nuove funzionalità che è stato abilitato potrebbe influenzare alcune query. Per ridurre i rischi della migrazione, modificare il livello di compatibilità del database solo dopo il monitoraggio delle prestazioni.
- Implementare [migliore pratica linee guida di archiviazione per utilizzo generico](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) come pre-allocazione di dimensioni dei file per ottenere prestazioni migliori.
- Scopri le [differenze di ambiente che potrebbero causare le differenze di prestazioni tra istanza gestita e SQL Server principali]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) e identificare i rischi che potrebbero influire sulle prestazioni.
- Accertarsi di mantenere abilitato Query Store e l'ottimizzazione automatica per l'istanza gestita. Queste funzionalità consentono di misurare le prestazioni del carico di lavoro e risolvere automaticamente i potenziali problemi di prestazioni. Informazioni su come usare Query Store come strumento ottima per ottenere informazioni sulle prestazioni del carico di lavoro prima e dopo la modifica del livello di compatibilità del database, come spiegato in [mantenere la stabilità delle prestazioni durante l'aggiornamento alla versione più recente di SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Dopo aver preparato l'ambiente che può essere paragonata quanto più possibile con l'ambiente locale, è possibile avviare l'esecuzione del carico di lavoro e misurare le prestazioni. Processo di misurazione deve includere gli stessi parametri che è stata misurata [durante la creazione di prestazioni di base di misure del carico di lavoro in SQL Server di origine](#create-performance-baseline).
Di conseguenza, si deve confrontare i parametri delle prestazioni con la linea di base e identificare le differenze critiche.

> [!NOTE]
> In molti casi, non sarà in grado di ottenere prestazioni esattamente corrispondenti in istanza gestita e SQL Server. Istanza gestita di Azure è un motore di database di SQL Server, ma dell'infrastruttura e configurazione a disponibilità elevata in istanza gestita può introdurre alcune differenze. È possibile prevedere che alcune query sarebbe più veloci mentre un altro può risultare più lento. L'obiettivo di confronto è verificare che le prestazioni del carico di lavoro nell'istanza gestita di soddisfino le prestazioni in SQL Server (in Media) e identificare sono presenti tutte le query critiche con le prestazioni che non soddisfano le prestazioni originale.

Potrebbe essere il risultato del confronto delle prestazioni:
- Le prestazioni del carico di lavoro sull'istanza gestita è allineato o meglio che le prestazioni del carico di lavoro in SQL Server. In questo caso è confermato che la migrazione ha esito positivo.
- Maggioranza dei nodi dei parametri delle prestazioni e le query nel carico di lavoro lavoro tutto bene, con alcune eccezioni con influire negativamente sulle prestazioni. In questo caso, è necessario identificare le differenze e la loro importanza. Se sono presenti alcune query importanti con riduzione delle prestazioni, è necessario esaminare vengono modificati i piani SQL sottostanti o le query raggiungono alcuni limiti di risorse. Mitigazione in questo caso potrebbe consistere nell'applicare alcuni suggerimenti su query critiche (ad esempio Modifica livello di compatibilità, stima di cardinalità legacy) sia direttamente oppure usando le guide di piano rebuild o create le statistiche e indici che potrebbero interessare i piani. 
- La maggior parte delle query sono lenta in istanza gestita rispetto a SQL Server di origine. In questo caso provare a identificare le cause della differenza, ad esempio [raggiunge il limite di alcune risorse]( sql-database-managed-instance-resource-limits.md#instance-level-resource-limits) i limiti dei / o, limite di memoria, limite di velocità di log di istanza e così via. Se non sono previsti limiti di risorse che possono causare la differenza, provare a modificare il livello di compatibilità del database oppure modificare le impostazioni di database, ad esempio la stima di cardinalità legacy e avviare nuovamente il test. Esaminare i consigli forniti dalle visualizzazioni di Query Store o istanza gestita per identificare le query regredite delle prestazioni.

> [!IMPORTANT]
> Istanza gestita dispone di funzionalità di correzione automatica del piano predefiniti che è abilitato per impostazione predefinita. Questa funzionalità garantisce che le query che hanno avuto esito positivo in Incolla non comprometta in futuro. Assicurarsi che questa funzionalità è abilitata e che è stato eseguito il carico di lavoro abbastanza tempo con le impostazioni precedenti prima di impostare le nuove impostazioni per abilitare l'istanza gestita conoscere i piani e le prestazioni di riferimento.

Apportare la modifica dei parametri o aggiornare i livelli di servizio per la convergenza per la configurazione ottimale, fino a quando non si ottengono le prestazioni del carico di lavoro adatta alle proprie esigenze.

### <a name="monitor-performance"></a>Monitorare le prestazioni

Istanza gestita offre numerosi strumenti avanzati per il monitoraggio e risoluzione dei problemi e si devono utilizzarle per monitorare le prestazioni nell'istanza. Alcuni dei parametri che il dovrà monitorare sono:
- Utilizzo della CPU dell'istanza per determinare esegue il numerica di Vcore di cui è stato effettuato il provisioning è la corrispondenza corretta per il carico di lavoro.
- La permanenza presunta della pagina per istanza gestita per determinare [è necessario aggiungere memoria](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Statistiche di attesa `INSTANCE_LOG_GOVERNOR` o `PAGEIOLATCH` che indicherà sono problemi dei / o di archiviazione, soprattutto nel livello utilizzo generico in cui potrebbe essere necessario preallocare i file e ottenere migliorate prestazioni dei / o.

## <a name="leverage-advanced-paas-features"></a>Sfruttare le funzionalità avanzate di PaaS

Una volta che trovano in una piattaforma completamente gestita e aver verificato che le prestazioni del carico di lavoro delle corrispondenti si delle prestazioni del carico di lavoro di SQL Server, usufruire dei vantaggi offerti automaticamente come parte del servizio del Database SQL. 

Anche se non si apportano alcune modifiche in istanza gestita durante la migrazione, vi sono elevate probabilità che ci si affida in alcune delle nuove funzionalità quando si opera l'istanza per un usufruire dei miglioramenti apportati al motore di database più recente. Alcune modifiche sono abilitate solo dopo il [livello di compatibilità del database è stato modificato](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).


In un'istanza gestita, ad esempio, non è necessario creare backup, perché vengono creati automaticamente dal servizio, né preoccuparsi della pianificazione, dell'esecuzione e della gestione dei backup. Un'istanza gestita offre la possibilità di eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione con il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore). Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

Per potenziare la sicurezza, è consigliabile usare [Azure Active Directory Authentication](sql-database-security-overview.md), [controllo](sql-database-managed-instance-auditing.md), [rilevamento delle minacce](sql-database-advanced-data-security.md), [sicurezzaalivellodiriga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security), e [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) ).

Oltre a Gestione avanzata e funzionalità di sicurezza, istanza gestita offre un set di strumenti avanzati che possono aiutare a [monitorare e ottimizzare il carico di lavoro](sql-database-monitor-tune-overview.md). [Azure analitica SQL](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) consente di monitorare un ampio set di istanze gestite e centralizzare il monitoraggio di un numero elevato di istanze e database. [L'ottimizzazione automatica](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) nell'istanza gestita di monitoraggio continuo delle prestazioni Statistiche esecuzione piano SQL e risolvere automaticamente i problemi di prestazioni identificato.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle istanze gestite, vedere [Informazioni sull'istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione con un ripristino da backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sulla migrazione con Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a un'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  
