---
title: Migrazione di un database SQL Server al database SQL di Azure | Microsoft Docs
description: "Informazioni sulla migrazione del database SQL Server al database SQL di Azure nel cloud. Usare gli strumenti di migrazione del database per verificare la compatibilità prima della migrazione del database."
keywords: migrazione di database, migrazione di database sql server, strumenti di migrazione del database, eseguire la migrazione di database, eseguire la migrazione di database sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 9cf09000-87fc-4589-8543-a89175151bc2
ms.service: sql-database
ms.custom: load & move data
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/08/2017
ms.author: carlrab
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 90c78007368c2679e1c5afdb9369869adde77f0d
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="sql-server-database-migration-to-sql-database-in-the-cloud"></a>Migrazione di un database SQL Server al database SQL nel cloud
Questo articolo illustra i due metodi principali per eseguire la migrazione di un database SQL Server 2005 o versione successiva a un database SQL di Azure. Il primo metodo è più semplice, ma comporta tempi di inattività anche lunghi durante la migrazione. Il secondo metodo è più complesso, ma elimina quasi completamente i tempi di inattività durante la migrazione.

In entrambi i casi, è necessario assicurarsi che il database di origine sia compatibile con database SQL di Azure, tramite [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595). Con la versione 12 del database SQL si sta raggiungendo la [parità di funzionalità](sql-database-features.md) con SQL Server, a eccezione dei problemi legati alle operazioni a livello di server e tra database. I database e le applicazioni basati su [funzionalità non supportate o supportate parzialmente](sql-database-transact-sql-information.md) devono essere [riprogettati per risolvere tali incompatibilità](sql-database-cloud-migrate.md#resolving-database-migration-compatibility-issues) prima della migrazione del database SQL Server.

> [!NOTE]
> Per eseguire la migrazione di database diversi dai database di SQL Server, inclusi Microsoft Access, Sybase, MySQL Oracle e DB2 nel database SQL di Azure, vedere il post di blog su [SQL Server Migration Assistant](https://blogs.msdn.microsoft.com/datamigration/2016/12/22/released-sql-server-migration-assistant-ssma-v7-2/).
> 

## <a name="method-1-migration-with-downtime-during-the-migration"></a>Metodo 1: Migrazione con tempi di inattività durante la migrazione

 Usare questo metodo se si è disposti a tollerare tempi di inattività o se si esegue una migrazione di prova di un database di produzione per una migrazione successiva. Per un'esercitazione, vedere [Eseguire la migrazione di un database SQL Server](sql-database-migrate-your-sql-server-database.md).

L'elenco seguente illustra un flusso di lavoro generico per la migrazione di un database SQL Server con questo metodo.

  ![Diagramma di migrazione di VSSSDT](./media/sql-database-cloud-migrate/azure-sql-migration-sql-db.png)

1. Valutare la compatibilità del database usando la versione più recente di [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).
2. Preparare eventuali correzioni necessarie come script Transact-SQL.
3. Creare una copia del database di origine coerente a livello di transazione e assicurarsi che non vengano apportate ulteriori modifiche al database di origine. In alternativa, è possibile applicare manualmente le eventuali modifiche al termine della migrazione. Per disattivare un database sono disponibili vari metodi, dalla disabilitazione della connettività client alla creazione di uno [snapshot del database](https://msdn.microsoft.com/library/ms175876.aspx).
4. Distribuire gli script Transact-SQL per applicare le correzioni alla copia del database.
5. [Esportare](sql-database-export.md) la copia del database in un file BACPAC in un'unità locale.
6. [Importare](sql-database-import.md) il file BACPAC come nuovo database SQL di Azure usando uno dei tanti strumenti di importazione BACPAC disponibili. Lo strumento consigliato è SQLPackage.exe, che permette di ottenere prestazioni ottimali.

### <a name="optimizing-data-transfer-performance-during-migration"></a>Ottimizzazione delle prestazioni di trasferimento dei dati durante la migrazione 

L'elenco seguente contiene indicazioni che permettono di ottimizzare le prestazioni durante il processo di importazione.

* Scegliere il livello di prestazioni e di servizio più elevato consentito dal budget per ottimizzare le prestazioni di trasferimento. Per risparmiare, è possibile ridurre le prestazioni al termine della migrazione. 
* Ridurre al minimo la distanza tra il file BACPAC e il data center di destinazione.
* Disabilitare le statistiche automatiche durante la migrazione.
* Partizionare tabelle e indici.
* Eliminare le viste indicizzate e ricrearle al termine della migrazione.
* Rimuovere i dati cronologici raramente sottoposti a query in un altro database ed eseguire la migrazione di tali dati in un database SQL di Azure separato. Sarà quindi possibile eseguire query sui dati cronologici usando [query elastiche](sql-database-elastic-query-overview.md).

### <a name="optimize-performance-after-the-migration-completes"></a>Ottimizzare le prestazioni al termine della migrazione

[Aggiornare le statistiche](https://msdn.microsoft.com/library/ms187348.aspx) con un'analisi completa dopo aver completato la migrazione.

## <a name="method-2-use-transactional-replication"></a>Metodo 2: Usare la replica transazionale

Quando non è possibile rimuovere il database SQL Server dalla produzione durante la migrazione, è possibile usare la replica transazionale di SQL Server come soluzione di migrazione. Per poter usare questo metodo, il database di origine deve soddisfare i [requisiti per la replica transazionale](https://msdn.microsoft.com/library/mt589530.aspx) ed essere compatibile con il database SQL di Azure. Per informazioni sulla replica di SQL con AlwaysOn, vedere [Configurare la replica per i gruppi di disponibilità AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server).

Per usare questa soluzione, è necessario configurare il database SQL di Azure come sottoscrittore dell'istanza di SQL Server di cui si vuole eseguire la migrazione. Il server di distribuzione della replica transazionale sincronizza i dati dal database da sincronizzare, ovvero il server di pubblicazione, mentre continua l'esecuzione di transazioni. 

Con la replica transazionale, tutte le modifiche ai dati o allo schema vengono visualizzate nel database SQL di Azure. Quando la sincronizzazione è completata e si è pronti a eseguire la migrazione, è sufficiente modificare la stringa di connessione delle applicazioni in modo che puntino al database SQL di Azure. Al termine dello svuotamento delle eventuali modifiche rimaste nel database di origine da parte della replica transazionale, quando tutte le applicazioni puntano al database di Azure è possibile disinstallare la replica transazionale. Il database SQL di Azure è ora il sistema di produzione.

 ![Diagramma di SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

> [!TIP]
> È anche possibile usare la replica transazionale per eseguire la migrazione di un subset del database di origine. La pubblicazione di cui si esegue la replica nel database SQL di Azure può essere limitata a un subset delle tabelle nel database replicato. Per ogni tabella replicata, è possibile limitare i dati a un subset di righe e/o di colonne.
>

### <a name="migration-to-sql-database-using-transaction-replication-workflow"></a>Migrazione al database SQL tramite il flusso di lavoro della replica transazionale

> [!IMPORTANT]
> Usare sempre la versione più aggiornata di SQL Server Management Studio per restare sincronizzati con gli aggiornamenti di Microsoft Azure e del database SQL. Le versioni precedenti di SQL Server Management Studio non sono in grado di impostare il database SQL come sottoscrittore. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 

1. Configurare la distribuzione
   -  [Usando SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_1)
   -  [Usando Transact-SQL](https://msdn.microsoft.com/library/ms151192.aspx#Anchor_2)
2. Creare la pubblicazione
   -  [Usando SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_1)
   -  [Usando Transact-SQL](https://msdn.microsoft.com/library/ms151160.aspx#Anchor_2)
3. Creare la sottoscrizione
   -  [Usando SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_0)
   -  [Usando Transact-SQL](https://msdn.microsoft.com/library/ms152566.aspx#Anchor_1)

### <a name="some-tips-and-differences-for-migrating-to-sql-database"></a>Suggerimenti e differenze per la migrazione al database SQL

1. Usare un server di distribuzione locale. 
   - Questo influisce sulle prestazioni del server. 
   - Se l'impatto sulle prestazioni non è accettabile, è possibile usare un altro server, facendo però aumentare la complessità delle operazioni di gestione e amministrazione.
2. Quando si seleziona una cartella snapshot, assicurarsi che le dimensioni della cartella selezionata siano sufficienti a contenere un BCP di ogni tabella da replicare. 
3. La creazione di snapshot consente di bloccare le tabelle associate fino al completamento, è quindi consigliabile pianificare lo snapshot in modo appropriato. 
4. Nel database SQL di Azure sono supportate solo le sottoscrizioni push. È possibile aggiungere sottoscrittori unicamente dal database di origine.

## <a name="resolving-database-migration-compatibility-issues"></a>Risoluzione dei problemi di compatibilità della migrazione di database
Esiste una vasta gamma di problemi di compatibilità che possono verificarsi, a seconda della versione del server SQL del database di origine e della complessità del database verso cui si esegue la migrazione. Le versioni precedenti di SQL Server presentano più problemi di compatibilità. Oltre a una ricerca mirata su Internet tramite i propri motori di ricerca preferiti, si consiglia di usare le risorse seguenti:

* [Funzionalità del database di SQL Server non supportate nel database SQL di Microsoft Azure](sql-database-transact-sql-information.md)
* [Funzionalità del motore di database sospese in SQL Server 2016](https://msdn.microsoft.com/library/ms144262%28v=sql.130%29)
* [Funzionalità del motore di database sospese in SQL Server 2014](https://msdn.microsoft.com/library/ms144262%28v=sql.120%29)
* [Funzionalità del motore di database sospese in SQL Server 2012](https://msdn.microsoft.com/library/ms144262%28v=sql.110%29)
* [Funzionalità del motore di database sospese in SQL Server 2008 R2](https://msdn.microsoft.com/library/ms144262%28v=sql.105%29)
* [Funzionalità del motore di database sospese in SQL Server 2005](https://msdn.microsoft.com/library/ms144262%28v=sql.90%29)

Oltre a ricerche su Internet e all'uso di queste risorse, usare il [forum della community MSDN SQL Server](https://social.msdn.microsoft.com/Forums/sqlserver/home?category=sqlserver) o [StackOverflow](http://stackoverflow.com/).

## <a name="next-steps"></a>Passaggi successivi
* Usare lo script nel blog degli ingegneri di Azure SQL EMEA per [monitorare l'utilizzo di tempdb durante la migrazione](https://blogs.msdn.microsoft.com/azuresqlemea/2016/12/28/lesson-learned-10-monitoring-tempdb-usage/).
* Usare lo script nel blog degli ingegneri di Azure SQL EMEA per [monitorare lo spazio del log delle transazioni del database durante la migrazione](https://blogs.msdn.microsoft.com/azuresqlemea/2016/10/31/lesson-learned-7-monitoring-the-transaction-log-space-of-my-database/0).
* Per informazioni sull'uso di file BACPAC per la migrazione, vedere l'articolo [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrazione da SQL Server al database SQL di Azure con file BACPAC) del blog del Customer Advisory Team di SQL Server.
* Per informazioni sull'uso dell'ora UTC dopo la migrazione, vedere [Modifying the default time zone for your local time zone](https://blogs.msdn.microsoft.com/azuresqlemea/2016/07/27/lesson-learned-4-modifying-the-default-time-zone-for-your-local-time-zone/) (Sostituire il fuso orario predefinito con il fuso orario locale).
* Per informazioni su come modificare la lingua predefinita di un database dopo la migrazione, vedere [How to change the default language of Azure SQL Database](https://blogs.msdn.microsoft.com/azuresqlemea/2017/01/13/lesson-learned-16-how-to-change-the-default-language-of-azure-sql-database/) (Come modificare la lingua predefinita del database SQL di Azure).



