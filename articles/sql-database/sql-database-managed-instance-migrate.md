---
title: Eseguire la migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure.
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
ms.date: 02/11/2019
ms.openlocfilehash: 1460b595e8887fc932d5be335ae51b07a000b9fb
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098358"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrazione di un'istanza di SQL Server a un'istanza gestita di database SQL di Azure

Questo articolo illustra i metodi disponibili per eseguire la migrazione di un'istanza di SQL Server 2005 o versione successiva a un'[istanza gestita di database SQL di Azure](sql-database-managed-instance.md). Per informazioni sulla migrazione a un database singolo o a un pool elastico, vedere [Migrate to a single or pooled database](sql-database-cloud-migrate.md) (Eseguire la migrazione a un database singolo o in pool). Per informazioni sulla migrazione da altre piattaforme, vedere [Azure Database Migration Guide](https://datamigration.microsoft.com/) (Guida alla migrazione di database di Azure).

A livello generale, il processo di migrazione del database è simile a quello indicato di seguito:

![Processo di migrazione](./media/sql-database-managed-instance-migration/migration-process.png)

- [Valutare la compatibilità dell'istanza gestita](#assess-managed-instance-compatibility)
- [Scegliere un'opzione di connettività dell'app](sql-database-managed-instance-connect-app.md)
- [Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali](#deploy-to-an-optimally-sized-managed-instance)
- [Selezionare il metodo ed eseguire la migrazione](#select-migration-method-and-migrate)
- [Monitorare le applicazioni](#monitor-applications)

> [!NOTE]
> Per eseguire la migrazione di un database singolo in un database singolo o un pool di database elastico, vedere l'articolo relativo alla [migrazione di un database di SQL Server nel database SQL di Azure](sql-database-single-database-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Valutare la compatibilità dell'istanza gestita

Per prima cosa, determinare se l'istanza gestita è compatibile con i requisiti di database dell'applicazione. L'opzione di distribuzione dell'istanza gestita è progettata per consentire una facile migrazione in modalità lift-and-shift della maggior parte delle applicazioni che usano SQL Server in locale o in macchine virtuali. Talvolta, tuttavia, possono essere necessarie caratteristiche o funzionalità non ancora supportate e il costo di implementazione di una soluzione alternativa è troppo elevato.

Per rilevare i potenziali problemi di compatibilità che influirebbero sulle funzionalità dei database nel database SQL di Azure, usare [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview). DMA non supporta ancora un'istanza gestita come destinazione della migrazione, ma è consigliabile eseguire una valutazione rispetto al database SQL di Azure e confrontare attentamente l'elenco dei problemi di compatibilità e parità di funzionalità segnalati con la documentazione del prodotto. Vedere [Funzionalità di database SQL di Azure](sql-database-features.md) per controllare se sono stati segnalati problemi di blocco non presenti nell'istanza gestita poiché la maggior parte dei problemi che impediscono la migrazione al database SQL di Azure è stata risolta con l'istanza gestita. Nelle istanze gestite, ad esempio, sono disponibili funzionalità come le query tra database, le transazioni tra database nella stessa istanza, il server collegato per altre origini SQL, CLR, le tabelle temporanee globali, le viste a livello di istanza, Service Broker e così via.

Se sono presenti problemi che causano un blocco che non sono stati risolti con l'opzione di distribuzione dell'istanza gestita, può essere necessario prendere in considerazione un'opzione alternativa, ad esempio [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Di seguito sono riportati alcuni esempi:

- Se è necessario l'accesso diretto al sistema operativo o al file system, ad esempio per installare agenti personalizzati o di terze parti nella stessa macchina virtuale con SQL Server.
- Se è presente una stretta dipendenza da funzionalità non ancora supportate, come FileStream/FileTable, PolyBase e le transazioni tra istanze.
- Se è assolutamente necessario mantenere una versione specifica di SQL Server (ad esempio, 2012).
- Se i requisiti di calcolo sono nettamente inferiori rispetto a quanto offerto dall'istanza gestita, ad esempio, un vCore, e il consolidamento di database non è un'opzione accettabile.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali

L'istanza gestita è progettata appositamente per carichi di lavoro locali che si intende spostare nel cloud. Introduce un [nuovo modello di acquisto](sql-database-service-tiers-vcore.md) che offre maggiore flessibilità nella selezione del livello appropriato di risorse per i carichi di lavoro. Nell'ambiente locale, si è probabilmente soliti dimensionare i carichi di lavoro usando core fisici o larghezza di banda di I/O. Il modello di acquisto dell'istanza gestita è basato sui core virtuali, o "vCore", con I/O e spazio di archiviazione aggiuntivi disponibili separatamente. Il modello basato su vCore semplifica la comprensione dei requisiti di calcolo nel cloud rispetto alle risorse usate attualmente in locale. Questo nuovo modello consente di dimensionare correttamente l'ambiente di destinazione nel cloud.

È possibile selezionare le risorse di calcolo e di archiviazione in fase di distribuzione e quindi modificarle in seguito senza causare tempi di inattività per l'applicazione usando il [portale di Azure](sql-database-scale-resources.md):

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

Dopo la migrazione tenere traccia del comportamento e delle prestazioni dell'applicazione. Nell'istanza gestita alcune modifiche sono abilitate solo dopo la [modifica del livello di compatibilità del database](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Nella maggior parte dei casi, nella migrazione di database al database SQL di Azure viene mantenuto il livello di compatibilità originale. Se il livello di compatibilità di un database utente prima della migrazione è 100 o un valore superiore, il livello rimane invariato dopo la migrazione. Se il livello di compatibilità di un database utente prima della migrazione è 90, nel database aggiornato viene impostato su 100, che è il livello di compatibilità minimo supportato in un'istanza gestita. Il livello di compatibilità dei database di sistema è 140.

Per ridurre i rischi della migrazione, modificare il livello di compatibilità del database solo dopo il monitoraggio delle prestazioni. Come strumento ottimale per ottenere informazioni sulle prestazioni dei carichi di lavoro prima e dopo la modifica del livello di compatibilità del database, usare Query Store come illustrato in [Mantenere la stabilità delle prestazioni durante l'aggiornamento alla nuova versione di SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Dopo essere passati a una piattaforma completamente gestita, usufruire dei vantaggi offerti automaticamente come parte del servizio Database SQL. In un'istanza gestita, ad esempio, non è necessario creare backup, perché vengono creati automaticamente dal servizio, né preoccuparsi della pianificazione, dell'esecuzione e della gestione dei backup. Un'istanza gestita offre la possibilità di eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione con il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore). Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

Per potenziare la sicurezza, valutare la possibilità di usare alcune delle funzionalità disponibili:

- Autenticazione di Azure Active Directory a livello di database
- Per proteggere l'istanza, usare le [funzionalità di sicurezza avanzate](sql-database-security-overview.md), ad esempio [controllo](sql-database-managed-instance-auditing.md), [rilevamento delle minacce](sql-database-advanced-data-security.md), [sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) e [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulle istanze gestite, vedere [Informazioni sull'istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione con un ripristino da backup, vedere [Creare un'istanza gestita](sql-database-managed-instance-get-started.md).
- Per un'esercitazione sulla migrazione con Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a un'istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  
