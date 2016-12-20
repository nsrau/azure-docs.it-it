---
title: Gestire e risolvere i problemi di Estensione database | Documentazione Microsoft
description: Scoprire come gestire e risolvere i problemi di Database Estensione.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 8a43c0fc-64d3-4042-8921-a36542aa8933
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 681ad472e53a17600b589d8354d9fdb5c9c3c574


---
# <a name="manage-and-troubleshoot-stretch-database"></a>Gestire e risolvere i problemi di Database Estensione
Per gestire e risolvere i problemi di Database Estensione, usare gli strumenti e i metodi descritti in questo argomento.

## <a name="manage-local-data"></a>Gestire i dati locali
### <a name="a-namelocalinfoaget-info-about-local-databases-and-tables-enabled-for-stretch-database"></a><a name="LocalInfo"></a>Ottenere informazioni sui database locali e le tabelle abilitate per Estensione database
Aprire le viste del catalogo **sys.databases** e **sys.tables** per visualizzare informazioni sulle tabelle e i database SQL Server con estensione abilitata. Per altre informazioni, vedere [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Per vedere la quantità di spazio che una tabella con estensione abilitata sta usando in SQL Server, eseguire l'istruzione seguente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## <a name="manage-data-migration"></a>Gestire la migrazione dei dati
### <a name="check-the-filter-function-applied-to-a-table"></a>Controllare la funzione di filtro applicato a una tabella
Aprire la vista catalogo **sys.remote\_data\_archive\_tables** e controllare il valore della colonna **filter\_predicate** per identificare la funzione che Estensione database sta usando per selezionare le righe da migrare. Se il valore è null, l'intera tabella è idonea alla migrazione. Per altre informazioni, vedere [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) e [Selezionare le righe di cui eseguire la migrazione tramite una funzione di filtro](sql-server-stretch-database-predicate-function.md).

### <a name="a-namemigrationacheck-the-status-of-data-migration"></a><a name="Migration"></a>Controllare lo stato della migrazione dei dati
Selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio per monitorare la migrazione dei dati in Monitoraggio Estensione database. Per altre informazioni, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Estensione database)](sql-server-stretch-database-monitor.md).

In alternativa, aprire la DMV **sys.dm\_db\_rda\_migration\_status** per visualizzare il numero di batch e righe di dati migrati.

### <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Risoluzione dei problemi di migrazione dei dati
Per avere suggerimenti sulla risoluzione dei problemi, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Estensione database)](sql-server-stretch-database-monitor.md).

## <a name="manage-remote-data"></a>Gestire i dati remoti
### <a name="a-nameremoteinfoaget-info-about-remote-databases-and-tables-used-by-stretch-database"></a><a name="RemoteInfo"></a>Informazioni su database e tabelle remoti usati da Estensione database
Aprire le viste del catalogo **sys.remote\_data\_archive\_databases** e **sys.remote\_data\_archive\_tables** per visualizzare informazioni sui database e le tabelle remoti in cui sono archiviati i dati migrati. Per altre informazioni, vedere [sys.remote_data_archive_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Per vedere la quantità di spazio che una tabella con estensione abilitata sta usando in Azure, eseguire l'istruzione seguente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### <a name="delete-migrated-data"></a>Eliminare i dati migrati
Se si vuole eliminare dati già migrati in Azure, seguire la procedura descritta in [sys.sp_rda_reconcile_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## <a name="manage-table-schema"></a>Gestire lo schema della tabella
### <a name="dont-change-the-schema-of-the-remote-table"></a>Non modificare lo schema della tabella remota
Non modificare lo schema di una tabella di Azure remota associata a una tabella di SQL Server configurata per Database Estensione. In particolare, non si deve modificare il nome o il tipo di dati di una colonna. La funzionalità Database Estensione ipotizza varie opzioni per lo schema della tabella remota in relazione allo schema della tabella di SQL Server. Se si modifica lo schema remoto, Database Estensione smette di funzionare per la tabella modificata.

### <a name="reconcile-table-columns"></a>Riconciliare le colonne della tabella
Se sono state eliminate accidentalmente colonne dalla tabella remota, eseguire **sp_rda_reconcile_columns** per aggiungere colonne alla tabella remota presenti nella tabella SQL Server con estensione abilitata ma non nella tabella remota. Per altre informazioni, vedere [sys.sp_rda_reconcile_columns](https://msdn.microsoft.com/library/mt707765.aspx).  

> [!IMPORTANT]
> Quando **sp_rda_reconcile_columns** ricrea colonne che sono state accidentalmente eliminate dalla tabella remota, non ripristina i dati contenuti in precedenza nelle colonne eliminate.
> 
> 

**sp_rda_reconcile_columns** non elimina dalla tabella remota le colonne che esistono nella tabella remota, ma non nella tabella SQL Server con estensione abilitata. Se sono presenti colonne nella tabella di Azure remota che non esistono più nella tabella SQL Server con estensione abilitata, queste colonne aggiuntive non impediscono a Estensione database di funzionare normalmente. Se lo si desidera, è possibile rimuovere manualmente le colonne in eccesso.  

## <a name="manage-performance-and-costs"></a>Gestire prestazioni e costi
### <a name="troubleshoot-query-performance"></a>Risoluzione dei problemi di prestazioni delle query
Sono previste prestazioni più lente per le query che includono tabelle con estensione abilitata rispetto al periodo antecedente all'abilitazione dell'estensione per le tabelle. Se le prestazioni delle query diminuiscono significativamente, esaminare i possibili problemi tra quelli riportati di seguito.

* Il server Azure si trova in un'area geografica diversa rispetto a SQL Server? Configurare il server Azure in modo che si trovi nella stessa area geografica di SQL Server per ridurre la latenza di rete.
* Possibile danneggiamento delle condizioni della rete. Per informazioni su problemi o interruzioni recenti, contattare l'amministratore di rete.

### <a name="increase-azure-performance-level-for-resource-intensive-operations-such-as-indexing"></a>Aumentare il livello di prestazioni di Azure per le operazioni a elevato utilizzo di risorse, ad esempio l'indicizzazione
Quando si compila, ricompila o riorganizza un indice su una tabella di grandi dimensioni configurata per Estensione database e si prevedono pesanti operazioni di query dei dati migrati in Azure durante questo intervallo, provare ad aumentare il livello di prestazioni del database di Azure remoto corrispondente per tutta la durata dell'operazione. Per altre informazioni sui livelli di prestazioni e i prezzi, vedere l'articolo sui [prezzi relativi a Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### <a name="you-cant-pause-the-sql-server-stretch-database-service-on-azure"></a>Non è possibile sospendere il servizio Estensione database di SQL Server in Azure
 Assicurarsi di selezionare il livello di prestazioni e di prezzo appropriati. Se si aumenta temporaneamente il livello di prestazioni per un'operazione a elevato utilizzo di risorse, ripristinare il livello precedente al termine dell'operazione. Per altre informazioni sui livelli di prestazioni e i prezzi, vedere l'articolo sui [prezzi relativi a Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).  

## <a name="change-the-scope-of-queries"></a>Modificare l'ambito delle query
 Le query sulle tabelle con estensione abilitata restituiscono dati sia locali sia remoti per impostazione predefinita. È possibile modificare l'ambito delle query per tutte le query da parte di tutti gli utenti o solo per una singola query da parte di un amministratore.  

### <a name="change-the-scope-of-queries-for-all-queries-by-all-users"></a>Modificare l'ambito delle query per tutte le query di tutti gli utenti
 Per modificare l'ambito di tutte le query di tutti gli utenti, eseguire la stored procedure **sys.sp_rda_set_query_mode**. È possibile ridurre l'ambito per rivolgere le query solo ai dati locali, disabilitare tutte le query o ripristinare l'impostazione predefinita. Per altre informazioni, vedere [sys.sp_rda_set_query_mode](https://msdn.microsoft.com/library/mt703715.aspx).  

### <a name="a-namequeryhintsachange-the-scope-of-queries-for-a-single-query-by-an-administrator"></a><a name="queryHints"></a>Modificare l'ambito delle query per una singola query di un amministratore
 Per modificare l'ambito di una singola query da parte di un membro del ruolo db_owner, aggiungere l'hint di query **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** all'istruzione SELECT. L'hint di query REMOTE_DATA_ARCHIVE_OVERRIDE può avere i seguenti valori.  

* **LOCAL_ONLY**. Query sui soli dati locali.  
* **REMOTE_ONLY**. Query sui soli dati remoti.  
* **STAGE_ONLY**. Query sui soli dati della tabella in cui Estensione database inserisce temporaneamente le righe idonee alla migrazione e conserva le righe migrate per il periodo specificato dopo la migrazione. Questo hint di query è l'unico modo per eseguire query sulla tabella di gestione temporanea.  

Ad esempio, la query seguente restituisce solo risultati locali.  

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="a-nameadminhintsamake-administrative-updates-and-deletes"></a><a name="adminHints"></a>Eseguire eliminazioni e aggiornamenti amministrativi
 Per impostazione predefinita, non è possibile eseguire UPDATE o DELETE sulle righe idonee alla migrazione o sulle righe già migrate in una tabella con estensione abilitata. Quando è necessario risolvere un problema, un membro del ruolo db_owner può eseguire un'operazione UPDATE o DELETE aggiungendo l'hint di query **WITH \( REMOTE_DATA_ARCHIVE_OVERRIDE = *value* \)** all'istruzione. L'hint di query REMOTE_DATA_ARCHIVE_OVERRIDE può avere i seguenti valori.  

* **LOCAL_ONLY**. Aggiornamento o eliminazione di soli dati locali.  
* **REMOTE_ONLY**. Aggiornamento o eliminazione di soli dati remoti.  
* **STAGE_ONLY**. Aggiornamento o eliminazione dei soli dati della tabella in cui Estensione database inserisce temporaneamente le righe idonee alla migrazione e conserva le righe migrate per il periodo specificato dopo la migrazione.  

## <a name="see-also"></a>Vedere anche
[Monitorare Database Estensione](sql-server-stretch-database-monitor.md)

[Eseguire il backup di database con estensione abilitata](sql-server-stretch-database-backup.md)

[Ripristinare database con estensione abilitata](sql-server-stretch-database-restore.md)




<!--HONumber=Nov16_HO3-->


