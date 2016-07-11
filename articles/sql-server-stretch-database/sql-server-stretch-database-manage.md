<properties
	pageTitle="Gestire e risolvere i problemi di Database Estensione | Microsoft Azure"
	description="Scoprire come gestire e risolvere i problemi di Database Estensione."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/27/2016"
	ms.author="douglasl"/>

# Gestire e risolvere i problemi di Database Estensione

Per gestire e risolvere i problemi di Database Estensione, usare gli strumenti e i metodi descritti in questo argomento.

## Gestire i dati locali

### <a name="LocalInfo"></a>Ottenere informazioni sui database locali e le tabelle abilitate per Estensione database
Aprire le viste del catalogo **sys.databases** e **sys.tables** per visualizzare informazioni sui database e le tabelle SQL Server con estensione abilitata. Per altre informazioni, vedere [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Per vedere la quantità di spazio che una tabella con estensione abilitata sta usando in SQL Server, eseguire l'istruzione seguente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'LOCAL_ONLY';
GO
 ```
## Gestire la migrazione dei dati

### Controllare la funzione di filtro applicato a una tabella
Aprire la vista catalogo **sys.remote\_data\_archive\_tables** e controllare il valore della colonna **filter\_predicate** per identificare la funzione che Estensione database sta usando per selezionare le righe da migrare. Se il valore è null, l'intera tabella è idonea alla migrazione. Per altre informazioni, vedere [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx) e [Usare una funzione di filtro per selezionare righe di cui eseguire la migrazione](sql-server-stretch-database-predicate-function.md).

### <a name="Migration"></a>Controllare lo stato della migrazione dei dati
Selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio per monitorare la migrazione dei dati in Monitoraggio Estensione database. Per altre informazioni, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Estensione database)](sql-server-stretch-database-monitor.md).

In alternativa, aprire la DMV **sys.dm\_db\_rda\_migration\_status** per visualizzare il numero di batch e righe di dati migrati.

### <a name="Firewall"></a>Risoluzione dei problemi di migrazione dei dati
Per avere suggerimenti sulla risoluzione dei problemi, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Estensione database)](sql-server-stretch-database-monitor.md).

## Gestire i dati remoti

### <a name="RemoteInfo"></a>Informazioni su database e tabelle remoti usati da Estensione database
Aprire le viste del catalogo **sys.remote\_data\_archive\_databases** e **sys.remote\_data\_archive\_tables** per visualizzare informazioni sui database e le tabelle remoti in cui sono stati archiviati i dati migrati. Per altre informazioni, vedere [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

Per vedere la quantità di spazio che una tabella con estensione abilitata sta usando in Azure, eseguire l'istruzione seguente.

 ```tsql
USE <Stretch-enabled database name>;
GO
EXEC sp_spaceused '<Stretch-enabled table name>', 'true', 'REMOTE_ONLY';
GO
 ```

### Eliminare i dati migrati  
Se si vuole eliminare dati già stati migrati in Azure, seguire la procedura descritta in [sys.sp\_rda\_reconcile\_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## Gestire lo schema della tabella

### Non modificare lo schema della tabella remota
Non modificare lo schema di una tabella di Azure remota associata a una tabella di SQL Server configurata per Database Estensione. In particolare, non si deve modificare il nome o il tipo di dati di una colonna. La funzionalità Database Estensione ipotizza varie opzioni per lo schema della tabella remota in relazione allo schema della tabella di SQL Server. Se si modifica lo schema remoto, Database Estensione smette di funzionare per la tabella modificata.

### Riconciliare le colonne della tabella  
Se sono state eliminate colonne accidentalmente nella tabella remota, eseguire **sp\_rda\_reconcile\_columns** per aggiungere colonne alla tabella remota presente nella tabella SQL Server con estensione abilitata ma non nella tabella remota. Per altre informazioni, vedere [sys.sp\_rda\_reconcile\_columns](https://msdn.microsoft.com/library/mt707765.aspx).

  > [!IMPORTANTE] Quando **sp\_rda\_reconcile\_columns** ricrea colonne che sono state eliminate per errore dalla tabella remota, non ripristina i dati contenuti in precedenza nelle colonne eliminate.

**sp\_rda\_reconcile\_columns** non elimina dalla tabella remota le colonne che esistono nella tabella remota ma non nella tabella SQL Server con estensione abilitata. Se sono presenti colonne nella tabella di Azure remota che non esistono più nella tabella SQL Server con estensione abilitata, queste colonne aggiuntive non impediscono a Estensione database di funzionare normalmente. Se lo si desidera, è possibile rimuovere manualmente le colonne in eccesso.

## Gestire prestazioni e costi  

### Risoluzione dei problemi di prestazioni delle query
Sono previste prestazioni più lente per le query che includono tabelle con estensione abilitata rispetto al periodo antecedente all'abilitazione dell'estensione per le tabelle. Se le prestazioni delle query diminuiscono significativamente, esaminare i possibili problemi tra quelli riportati di seguito.

-   Il server Azure si trova in un'area geografica diversa rispetto a SQL Server? Configurare il server Azure in modo che si trovi nella stessa area geografica di SQL Server per ridurre la latenza di rete.

-   Possibile danneggiamento delle condizioni della rete. Per informazioni su problemi o interruzioni recenti, contattare l'amministratore di rete.

### Aumentare il livello di prestazioni di Azure per le operazioni a elevato utilizzo di risorse, ad esempio l'indicizzazione
Quando si compila, ricompila o riorganizza un indice su una tabella di grandi dimensioni configurata per Estensione database e si prevedono pesanti operazioni di query dei dati migrati in Azure durante questo intervallo, provare ad aumentare il livello di prestazioni del database di Azure remoto corrispondente per tutta la durata dell'operazione. Per altre informazioni sui livelli di prestazioni e i prezzi, vedere l'articolo sui [prezzi relativi a Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Non è possibile sospendere il servizio Estensione database di SQL Server in Azure  
 Assicurarsi di selezionare il livello di prestazioni e di prezzo appropriati. Se si aumenta il livello di prestazioni temporaneamente per un'operazione che richiede molte risorse, ripristinare il livello precedente al termine dell'operazione. Per altre informazioni sui livelli di prestazioni e i prezzi, vedere l'articolo sui [prezzi relativi a Estensione database di SQL Server](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Modificare l'ambito delle query  
 Le query sulle tabelle con estensione abilitata restituiscono dati sia locali sia remoti per impostazione predefinita. È possibile modificare l'ambito delle query per tutte le query da parte di tutti gli utenti o solo per una singola query da parte di un amministratore.

### Modificare l'ambito delle query per tutte le query di tutti gli utenti  
 Per modificare l'ambito di tutte le query di tutti gli utenti, eseguire la stored procedure **sys.sp\_rda\_set\_query\_mode**. È possibile ridurre l'ambito per rivolgere le query solo ai dati locali, disabilitare tutte le query o ripristinare l'impostazione predefinita. Per altre informazioni, vedere [sys.sp\_rda\_set\_query\_mode](https://msdn.microsoft.com/library/mt703715.aspx).

### <a name="queryHints"></a>Modificare l'ambito delle query per una singola query di un amministratore  
 Per modificare l'ambito di una singola query da parte di un membro del ruolo db\_owner, aggiungere l'hint di query **WITH (REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *valore* )** all'istruzione SELECT. L'hint di query REMOTE\_DATA\_ARCHIVE\_OVERRIDE può avere i seguenti valori.
 -   **LOCAL\_ONLY**. Query sui soli dati locali.

 -   **REMOTE\_ONLY**. Query sui soli dati remoti.

 -   **STAGE\_ONLY**. Query sui soli dati della tabella in cui Estensione database inserisce temporaneamente le righe idonee alla migrazione e conserva le righe migrate per il periodo specificato dopo la migrazione. Questo hint di query è l'unico modo per eseguire query sulla tabella di gestione temporanea.

Ad esempio, la query seguente restituisce solo risultati locali.

 ```tsql  
 USE <Stretch-enabled database name>;
 GO
 SELECT * FROM <Stretch_enabled table name> WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ... ;
 GO
```  

## <a name="adminHints"></a>Eseguire eliminazioni e aggiornamenti amministrativi  
 Per impostazione predefinita non è possibile eseguire UPDATE o DELETE sulle righe idonee alla migrazione o le righe già migrate in una tabella con estensione abilitata. Quando è necessario risolvere un problema, un membro del ruolo db\_owner può eseguire un'operazione UPDATE o DELETE aggiungendo l'hint di query **WITH (REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *valore* )** all'istruzione. L'hint di query REMOTE\_DATA\_ARCHIVE\_OVERRIDE può avere i seguenti valori.
 -   **LOCAL\_ONLY**. Aggiornamento o eliminazione di soli dati locali.

 -   **REMOTE\_ONLY**. Aggiornamento o eliminazione di soli dati remoti.

 -   **STAGE\_ONLY**. Aggiornamento o eliminazione dei soli dati della tabella in cui Estensione database inserisce temporaneamente le righe idonee alla migrazione e conserva le righe migrate per il periodo specificato dopo la migrazione.

## Vedere anche

[Monitorare Database Estensione](sql-server-stretch-database-monitor.md)

[Eseguire il backup di database con estensione abilitata](sql-server-stretch-database-backup.md)

[Ripristinare database con estensione abilitata](sql-server-stretch-database-restore.md)

<!---HONumber=AcomDC_0629_2016-->