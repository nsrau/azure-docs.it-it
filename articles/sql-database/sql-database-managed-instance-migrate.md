---
title: Eseguire la migrazione di un'istanza di SQL Server a Istanza gestita di database SQL di Azure | Microsoft Docs
description: Informazioni su come eseguire la migrazione di un'istanza di SQL Server a Istanza gestita di database SQL di Azure.
keywords: migrazione di database, migrazione di database sql server, strumenti di migrazione del database, eseguire la migrazione di database, eseguire la migrazione di database sql
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: fd8225b2c5c4f897bbf3d7e1b2ba7659f564409d
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migrazione di un'istanza di SQL Server a Istanza gestita di database SQL di Azure

Questo articolo illustra i metodi disponibili per eseguire la migrazione di un'istanza di SQL Server 2005 o versione successiva a Istanza gestita di database SQL di Azure (anteprima). 

> [!NOTE]
> Per eseguire la migrazione di un database singolo a un database singolo o un pool elastico, vedere l'articolo relativo alla [migrazione di un database di SQL Server al database SQL di Azure](sql-database-cloud-migrate.md).

La funzionalità Istanza gestita di database SQL è un'espansione del servizio Database SQL esistente e offre una terza opzione di distribuzione oltre ai database singoli e ai pool elastici.  È stata progettata per consentire il trasferimento in modalità lift-and-shift di database a una soluzione PaaS completamente gestita senza riprogettazione dell'applicazione. Istanza gestita di database SQL offre compatibilità elevata con il modello di programmazione di SQL Server in locale e offre supporto predefinito per la maggior parte delle funzionalità di SQL Server e degli strumenti e dei servizi associati.

A livello generale, il processo di migrazione delle applicazioni si presenta come illustrato nel diagramma seguente:

![Processo di migrazione](./media/sql-database-managed-instance-migration/migration-process.png)

## <a name="assess-managed-instance-compatibility"></a>Valutare la compatibilità di Istanza gestita

Per prima cosa, determinare se Istanza gestita è compatibile con i requisiti di database dell'applicazione. Il servizio Istanza gestita è progettato per consentire una facile migrazione in modalità lift-and-shift della maggior parte delle applicazioni che usano SQL Server in locale o in macchine virtuali. Talvolta, tuttavia, possono essere necessarie caratteristiche o funzionalità non ancora supportate e il costo di implementazione di una soluzione alternativa è troppo elevato. 

Per rilevare i potenziali problemi di compatibilità che influirebbero sulle funzionalità dei database nel database SQL di Azure, usare [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview). DMA non supporta ancora Istanza gestita come destinazione della migrazione, ma è consigliabile eseguire una valutazione rispetto al database SQL di Azure e confrontare attentamente l'elenco dei problemi di compatibilità e parità di funzionalità segnalati con la documentazione del prodotto. La maggior parte dei problemi che impediscono una migrazione al database SQL di Azure è stata rimossa con Istanza gestita. Nelle istanze gestite, ad esempio, sono disponibili funzionalità come le query tra database, le transazioni tra database nella stessa istanza, il server collegato per altre origini SQL, CLR, le tabelle temporanee globali, le viste a livello di istanza, Service Broker e così via. 

In alcuni casi, tuttavia, è necessario considerare un'opzione alternativa, come [SQL Server in macchine virtuali in Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Di seguito sono riportati alcuni esempi:

- Se è necessario l'accesso diretto al sistema operativo o al file system, ad esempio per installare agenti personalizzati o di terze parti nella stessa macchina virtuale con SQL Server.
- Se è presente una stretta dipendenza da funzionalità non ancora supportate, come FileStream/FileTable, PolyBase e le transazioni tra istanze.
- Se è assolutamente necessario mantenere una versione specifica di SQL Server (ad esempio, 2012).
- Se i requisiti di calcolo sono nettamente inferiori rispetto a quanto offerto da Istanza gestita in anteprima pubblica (ad esempio, un vCore) e il consolidamento di database non è un'opzione accettabile.

## <a name="choose-app-connectivity-option"></a>Scegliere un'opzione di connettività dell'app

Un'istanza gestita è interamente contenuta nella rete virtuale e offre quindi il massimo livello di isolamento e sicurezza dei dati. Il diagramma seguente illustra le diverse opzioni disponibili per distribuire varie topologie di applicazioni interamente in Azure o in un ambiente ibrido, indipendentemente dal fatto che si scelga un modello ospitato o di servizio completamente gestito per le applicazioni front-end.

![Topologie di distribuzione delle applicazioni](./media/sql-database-managed-instance-migration/application-deployment-topologies.png)

Tutte le opzioni selezionate consentono la connettività a un endpoint SQL solo tramite indirizzi IP privati e questo garantisce il livello ottimale di isolamento dei dati. <!--- For more information, see How to connect your application to Managed Instance.--->

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Eseguire la distribuzione in un'istanza gestita di dimensioni ottimali

Il servizio Istanza gestita è progettato appositamente per carichi di lavoro locali che si intende spostare nel cloud. Introduce un nuovo modello di acquisto che offre maggiore flessibilità nella selezione del livello appropriato di risorse per i carichi di lavoro. Nell'ambiente locale, si è probabilmente soliti dimensionare i carichi di lavoro usando core fisici. Il nuovo modello di acquisto di Istanza gestita è basato sui core virtuali, o "vCore", con I/O e spazio di archiviazione aggiuntivi disponibili separatamente. Il modello basato su vCore semplifica la comprensione dei requisiti di calcolo nel cloud rispetto alle risorse usate attualmente in locale. Questo nuovo modello consente di dimensionare correttamente l'ambiente di destinazione nel cloud.

È possibile selezionare le risorse di calcolo e di archiviazione in fase di distribuzione e quindi modificarle in seguito senza causare tempi di inattività per l'applicazione.

![Dimensionamento dell'istanza gestita](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Per informazioni su come creare l'infrastruttura di rete virtuale e l'istanza gestita e ripristinare un database da un file di backup, vedere l'articolo su come [creare un'istanza gestita](sql-database-managed-instance-tutorial-portal.md).

> [!IMPORTANT]
> È importante che la rete virtuale e la subnet di destinazione siano sempre conformi ai [requisiti per la rete virtuale di Istanza gestita](sql-database-managed-instance-vnet-configuration.md#requirements). Qualsiasi incompatibilità può impedire la creazione di nuove istanze o l'uso di quelle già create.

## <a name="select-migration-method-and-migrate"></a>Selezionare il metodo ed eseguire la migrazione

Il servizio Istanza gestita è destinato a scenari utente che prevedono la migrazione di massa di database da implementazioni di database locali o IaaS. Questa opzione è ottimale quando è necessario trasferire in modalità lift-and-shift il back-end di applicazioni che usano regolarmente funzionalità a livello di istanza e/o tra database. In questo scenario, è possibile spostare un'intera istanza in un ambiente corrispondente in Azure senza dover ridefinire l'architettura delle applicazioni. 

Per spostare istanze di SQL, è necessario pianificare accuratamente quanto segue:

-   La migrazione di tutti i database da collocare (quelli in esecuzione nella stessa istanza)
-   La migrazione degli oggetti a livello di istanza da cui dipende l'applicazione, quali account di accesso, credenziali, operatori e processi di SQL Agent e trigger a livello di server. 

Istanza gestita è un servizio completamente gestito che consente di delegare alcune delle regolari attività di amministrazione di database alla piattaforma, perché sono predefinite. Di conseguenza, non è necessario eseguire la migrazione di alcuni dati a livello di istanza, come i processi di manutenzione per i backup regolari o la configurazione Always On, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

Istanza gestita supporta le opzioni di migrazione di database descritte di seguito, che attualmente sono gli unici metodi di migrazione supportati.

### <a name="azure-database-migration-service"></a>Servizio Migrazione del database di Azure

[Servizio Migrazione del database di Azure](../dms/dms-overview.md) è un servizio completamente gestito progettato per consentire migrazioni senza problemi da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi. Questo servizio semplifica le attività necessarie per spostare database di SQL Server e di terze parti in Azure. Le opzioni di distribuzione in anteprima pubblica includono il database SQL di Azure, Istanza gestita e SQL Server in una macchina virtuale di Azure. Servizio Migrazione del database è il metodo di migrazione consigliato per i carichi di lavoro aziendali. 

![Servizio Migrazione del database](./media/sql-database-managed-instance-migration/dms.png)

Per altre informazioni su questo scenario e sulla procedura di configurazione per Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a Istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Ripristino nativo da URL

Il ripristino di backup nativi (file con estensione bak) ottenuti da SQL Server in locale o [SQL Server in macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/) e disponibili in [Archiviazione di Azure](https://azure.microsoft.com/services/storage/) è una delle funzionalità principali di Istanza gestita di database SQL e consente una migrazione di database offline semplice e rapida. 

Il diagramma seguente illustra il processo a livello generale:

![Flusso della migrazione](./media/sql-database-managed-instance-migration/migration-flow.png)

La tabella seguente contiene altre informazioni sul metodo che è possibile usare a seconda della versione di SQL Server di origine eseguita:

|Passaggio|Versione e motore SQL|Metodo di backup/ripristino|
|---|---|---|
|Inserire il backup in Archiviazione di Azure|Precedente SQL 2012 SP1 CU2|Caricamento diretto del file con estensione bak in Archiviazione di Azure|
||2012 SP1 CU2 - 2016|Backup diretto con sintassi [con credenziali](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) deprecata|
||2016 e versioni successive|Backup diretto [con credenziali di firma di accesso condiviso](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Eseguire il ripristino da Archiviazione di Azure a Istanza gestita|[Ripristino da URL con credenziali di firma di accesso condiviso](sql-database-managed-instance-tutorial-portal.md#restore-the-wide-world-importers-database-from-a-backup-file)|

> [!IMPORTANT]
> Il ripristino di database di sistema non è supportato. Per eseguire la migrazione di oggetti a livello di istanza (archiviati in database master o msdb), è consigliabile inserirli in script ed eseguire gli script T-SQL nell'istanza di destinazione.

Per un'esercitazione completa con il ripristino del backup di un database in un'istanza gestita con credenziali di firma di accesso condiviso, vedere l'articolo su come [creare un'istanza gestita](sql-database-managed-instance-tutorial-portal.md).

### <a name="migrate-using-bacpac-file"></a>Eseguire la migrazione con un file BACPAC

È possibile eseguire l'importazione nel database SQL di Azure e in Istanza gestita da una copia del database originale, con i dati, creata in un file BACPAC. Vedere [Importare un file BACPAC in un nuovo database SQL di Azure](sql-database-import.md).

## <a name="monitor-applications"></a>Monitorare le applicazioni

Dopo la migrazione tenere traccia del comportamento e delle prestazioni dell'applicazione. In Istanza gestita, alcune modifiche sono abilitate solo dopo la [modifica del livello di compatibilità del database](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Nella maggior parte dei casi, nella migrazione di database al database SQL di Azure viene mantenuto il livello di compatibilità originale. Se il livello di compatibilità di un database utente prima della migrazione è 100 o un valore superiore, il livello rimane invariato dopo la migrazione. Se il livello di compatibilità di un database utente prima della migrazione è 90, nel database aggiornato viene impostato su 100, che è il livello di compatibilità minimo supportato in Istanza gestita. Il livello di compatibilità dei database di sistema è 140.

Per ridurre i rischi della migrazione, modificare il livello di compatibilità del database solo dopo il monitoraggio delle prestazioni. Come strumento ottimale per ottenere informazioni sulle prestazioni dei carichi di lavoro prima e dopo la modifica del livello di compatibilità del database, usare Query Store come illustrato in [Mantenere la stabilità delle prestazioni durante l'aggiornamento alla nuova versione di SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Dopo essere passati a una piattaforma completamente gestita, usufruire dei vantaggi offerti automaticamente come parte del servizio Database SQL. In Istanza gestita, ad esempio, non è necessario creare backup, perché vengono creati automaticamente dal servizio, né preoccuparsi della pianificazione, dell'esecuzione e della gestione dei backup. Istanza gestita offre la possibilità di eseguire il ripristino a qualsiasi momento specifico all'interno del periodo di conservazione con il [ripristino temporizzato](sql-database-recovery-using-backups.md#point-in-time-restore). Durante l'anteprima pubblica, il periodo di conservazione è fissato a sette giorni.
Non è inoltre necessario preoccuparsi della configurazione della disponibilità elevata, perché la [disponibilità elevata](sql-database-high-availability.md) è predefinita.

Per potenziare la sicurezza, valutare la possibilità di usare alcune delle funzionalità disponibili:
- Autenticazione di Azure Active Directory a livello di database
- Controllo e rilevamento delle minacce per monitorare le attività
- Controllo dell'accesso ai dati sensibili e con privilegi ([sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) e [maschera dati dinamica](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking))

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni in proposito, vedere l'articolo di [informazioni su Istanza gestita](sql-database-managed-instance.md).
- Per un'esercitazione con un ripristino da backup, vedere l'articolo su come [creare un'istanza gestita](sql-database-managed-instance-tutorial-portal.md).
- Per un'esercitazione sulla migrazione con Servizio Migrazione del database, vedere l'articolo su come [eseguire la migrazione di un database locale a Istanza gestita con Servizio Migrazione del database](../dms/tutorial-sql-server-to-managed-instance.md).  
