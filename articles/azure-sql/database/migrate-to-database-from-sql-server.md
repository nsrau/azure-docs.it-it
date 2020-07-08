---
title: Migrazione di un database SQL Server al database SQL di Azure
description: Informazioni sulla migrazione di SQL Server database al database SQL di Azure.
keywords: migrazione di database, migrazione di database sql server, strumenti di migrazione del database, eseguire la migrazione di database, eseguire la migrazione di database sql
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 106337fb4756052ee682624290620093bf4a70b3
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86081945"
---
# <a name="sql-server-database-migration-to-azure-sql-database"></a>Migrazione di un database SQL Server al database SQL di Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo articolo illustra i metodi principali per eseguire la migrazione di un database SQL Server 2005 o versione successiva al database SQL di Azure. Per informazioni sulla migrazione ad Azure SQL Istanza gestita, vedere [eseguire la migrazione di un'istanza di SQL Server a SQL di azure istanza gestita](../managed-instance/migrate-to-instance-from-sql-server.md). Per informazioni sulla migrazione da altre piattaforme, vedere [Azure Database Migration Guide](https://datamigration.microsoft.com/) (Guida alla migrazione di database di Azure).

## <a name="migrate-to-a-single-database-or-a-pooled-database"></a>Eseguire la migrazione a un database singolo o in pool

Esistono due metodi principali per eseguire la migrazione di un database SQL Server 2005 o versione successiva al database SQL di Azure. Il primo metodo è più semplice, ma comporta tempi di inattività anche lunghi durante la migrazione. Il secondo metodo è più complesso, ma elimina quasi completamente i tempi di inattività durante la migrazione.

In entrambi i casi, è necessario assicurarsi che il database di origine sia compatibile con database SQL di Azure, tramite [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Il database SQL sta raggiungendo la [parità di funzionalità](features-comparison.md) con SQL Server, ad eccezione dei problemi relativi alle operazioni a livello di server e tra database. I database e le applicazioni basati su [funzionalità non supportate o supportate parzialmente](transact-sql-tsql-differences-sql-server.md) devono essere [riprogettati per risolvere tali incompatibilità](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues) prima della migrazione del database SQL Server.

> [!NOTE]
> Per eseguire la migrazione di database diversi dai database di SQL Server, inclusi Microsoft Access, Sybase, MySQL Oracle e DB2 nel database SQL di Azure, vedere il post di blog su [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2017/09/29/release-sql-server-migration-assistant-ssma-v7-6/).

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Metodo 1: Migrazione con tempi di inattività durante la migrazione

 Utilizzare questo metodo per eseguire la migrazione a un database singolo o in pool se è possibile concedere tempi di inattività o se si esegue una migrazione di prova di un database di produzione per una migrazione successiva. Per un'esercitazione, vedere [Eseguire la migrazione di un database SQL Server](../../dms/tutorial-sql-server-to-azure-sql.md).

L'elenco seguente illustra un flusso di lavoro generico per eseguire la migrazione di un database SQL Server singolo o in pool usando questo metodo. Per la migrazione a SQL Istanza gestita, vedere la pagina relativa [alla migrazione a sql istanza gestita](../managed-instance/migrate-to-instance-from-sql-server.md).

  ![Diagramma di migrazione di VSSSDT](./media/migrate-to-database-from-sql-server/azure-sql-migration-sql-db.png)

1. [Valutare](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) la compatibilità del database usando la versione più recente di [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Preparare eventuali correzioni necessarie come script Transact-SQL.
3. Creare una copia coerente a livello di transazione del database di origine di cui si esegue la migrazione o interrompere le nuove transazioni nel database di origine mentre è in corso la migrazione. Per questa seconda opzione è possibile disabilitare la connettività client o creare uno [snapshot del database](https://msdn.microsoft.com/library/ms175876.aspx). Dopo la migrazione, è possibile usare la replica transazionale per aggiornare i database migrati con le modifiche apportate dopo il punto limite definito per la migrazione. Vedere [Usare la replica transazionale](migrate-to-database-from-sql-server.md#method-2-use-transactional-replication).  
4. Distribuire gli script Transact-SQL per applicare le correzioni alla copia del database.
5. [Eseguire la migrazione](https://docs.microsoft.com/sql/dma/dma-migrateonpremsql) della copia del database in un nuovo database nel database SQL di Azure usando il Data Migration Assistant.

> [!NOTE]
> Anziché Data Migration Assistant, è possibile usare un file BACPAC. Vedere [importare un file BACPAC in un nuovo database nel database SQL di Azure](database-import.md).

### <a name="optimizing-data-transfer-performance-during-migration"></a>Ottimizzazione delle prestazioni di trasferimento dei dati durante la migrazione

L'elenco seguente contiene indicazioni che permettono di ottimizzare le prestazioni durante il processo di importazione.

- Scegliere il livello di servizio e le dimensioni di calcolo più elevati consentiti dal budget per ottimizzare le prestazioni di trasferimento. Per risparmiare, è possibile ridurre le prestazioni al termine della migrazione.
- Ridurre al minimo la distanza tra il file BACPAC e il data center di destinazione.
- Disabilitare le statistiche di autostatistica durante la migrazione
- Partizionare tabelle e indici
- Eliminare le viste indicizzate e ricrearle al termine della migrazione.
- Rimuovere i dati cronologici raramente sottoposti a query in un altro database ed eseguire la migrazione dei dati cronologici in un database separato nel database SQL di Azure. Sarà quindi possibile eseguire query sui dati cronologici usando [query elastiche](elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Ottimizzare le prestazioni al termine della migrazione

[Aggiornare le statistiche](https://docs.microsoft.com/sql/t-sql/statements/update-statistics-transact-sql) con un'analisi completa dopo aver completato la migrazione.

## <a name="method-2-use-transactional-replication"></a>Metodo 2: Usare la replica transazionale

Quando non è possibile rimuovere il database SQL Server dalla produzione durante la migrazione, è possibile usare SQL Server la replica transazionale come soluzione di migrazione. Per poter usare questo metodo, il database di origine deve soddisfare i [requisiti per la replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx) ed essere compatibile con il database SQL di Azure. Per informazioni sulla replica di SQL con AlwaysOn, vedere [Configurare la replica per i gruppi di disponibilità AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Per usare questa soluzione, è necessario configurare il database nel database SQL di Azure come Sottoscrittore per l'istanza di SQL Server di cui si vuole eseguire la migrazione. Il server di distribuzione della replica transazionale sincronizza i dati dal database da sincronizzare, ovvero il server di pubblicazione, mentre continua l'esecuzione di transazioni.

Con la replica transazionale, tutte le modifiche apportate ai dati o allo schema vengono visualizzate nel database nel database SQL di Azure. Una volta completata la sincronizzazione e si è pronti per eseguire la migrazione, modificare la stringa di connessione delle applicazioni in modo che punti al database. Al termine dello svuotamento delle eventuali modifiche rimaste nel database di origine da parte della replica transazionale, quando tutte le applicazioni puntano al database di Azure è possibile disinstallare la replica transazionale. Il database nel database SQL di Azure è ora il sistema di produzione.

 ![Diagramma di SeedCloudTR](./media/migrate-to-database-from-sql-server/SeedCloudTR.png)

> [!TIP]
> È anche possibile usare la replica transazionale per eseguire la migrazione di un subset del database di origine. La pubblicazione di cui si esegue la replica nel database SQL di Azure può essere limitata a un subset delle tabelle nel database replicato. Per ogni tabella replicata, è possibile limitare i dati a un subset di righe e/o di colonne.

## <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrazione al database SQL tramite il flusso di lavoro della replica transazionale

> [!IMPORTANT]
> Usare la versione più recente di SQL Server Management Studio per restare sincronizzati con gli aggiornamenti di Azure e del database SQL. Le versioni precedenti di SQL Server Management Studio non sono in grado di impostare il database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Configurare la distribuzione
   - [Usando SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Utilizzo di Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)

2. Creare la pubblicazione
   - [Usando SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/configure-publishing-and-distribution/)
   - [Utilizzo di Transact-SQL](/sql/relational-databases/replication/configure-publishing-and-distribution/)
3. Creazione di sottoscrizioni
   - [Usando SQL Server Management Studio (SSMS)](/sql/relational-databases/replication/create-a-push-subscription/)
   - [Utilizzo di Transact-SQL](/sql/relational-databases/replication/create-a-push-subscription/)

Suggerimenti e differenze per la migrazione al database SQL

- Usare un server di distribuzione locale.
  - Questo influisce sulle prestazioni del server.
  - Se l'impatto sulle prestazioni non è accettabile, è possibile usare un altro server, facendo però aumentare la complessità delle operazioni di gestione e amministrazione.
- Quando si seleziona una cartella snapshot, assicurarsi che le dimensioni della cartella selezionata siano sufficienti a contenere un BCP di ogni tabella da replicare.
- La creazione di snapshot blocca le tabelle associate fino al completamento, quindi pianifica lo snapshot in modo appropriato.
- Nel database SQL di Azure sono supportate solo le sottoscrizioni push. È possibile aggiungere sottoscrittori unicamente dal database di origine.

## <a name="resolving-database-migration-compatibility-issues"></a>Risoluzione dei problemi di compatibilità della migrazione di database

È possibile riscontrare un'ampia gamma di problemi di compatibilità, a seconda della versione di SQL Server nel database di origine e della complessità del database di cui si sta eseguendo la migrazione. Le versioni precedenti di SQL Server presentano più problemi di compatibilità. Oltre a una ricerca mirata su Internet tramite i propri motori di ricerca preferiti, si consiglia di usare le risorse seguenti:

- [Funzionalità del database di SQL Server non supportate nel database SQL di Azure](transact-sql-tsql-differences-sql-server.md)
- [Funzionalità del motore di database non più usate in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
- [Funzionalità del Motore di database non più utilizzate in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
- [Funzionalità di motore di database sospese in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
- [Funzionalità del motore di database sospese in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
- [Funzionalità del motore di database sospese in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oltre a cercare in Internet e a usare queste risorse, usare la [pagina Domande e risposte di Microsoft&per il database SQL di Azure](https://docs.microsoft.com/answers/topics/azure-sql-database.html) o [StackOverflow](https://stackoverflow.com/).

> [!IMPORTANT]
> Istanza gestita SQL di Azure consente di eseguire la migrazione di un'istanza di SQL Server esistente e dei relativi database con un minimo di problemi di compatibilità. Vedere [che cos'è un'istanza gestita](../managed-instance/sql-managed-instance-paas-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Usare lo script nel blog degli ingegneri di Azure SQL EMEA per [monitorare l'utilizzo di tempdb durante la migrazione](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
- Usare lo script nel blog degli ingegneri di Azure SQL EMEA per [monitorare lo spazio del log delle transazioni del database durante la migrazione](https://docs.microsoft.com/archive/blogs/azuresqlemea/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database).
- Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
- Per informazioni sull'uso dell'ora UTC dopo la migrazione, vedere [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Sostituire il fuso orario predefinito con il fuso orario locale).
- Per informazioni su come modificare la lingua predefinita di un database dopo la migrazione, vedere [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Come modificare la lingua predefinita del database SQL di Azure).
 