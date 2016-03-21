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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Gestire e risolvere i problemi di Database Estensione

Per gestire e risolvere i problemi di Database Estensione, usare gli strumenti e i metodi descritti in questo argomento.

## <a name="LocalInfo"></a>Ottenere informazioni sui database locali e le tabelle abilitate per Database Estensione
Aprire le viste del catalogo **sys.databases** e **sys.tables** per visualizzare informazioni sui database e le tabelle SQL Server con abilitazione dell'estensione. Per altre informazioni, vedere [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

## <a name="RemoteInfo"></a>Ottenere informazioni su database e tabelle remoti usati da Database Estensione
Aprire le viste del catalogo **sys.remote\_data\_archive\_databases** e **sys.remote\_data\_archive\_tables** per visualizzare informazioni su database e tabelle remoti in cui sono stati archiviati i dati migrati. Per altre informazioni, vedere [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Controllare il predicato del filtro applicato a una tabella
Aprire la vista del catalogo **sys.remote\_data\_archive\_tables** e controllare il valore della colonna **filter\_predicate**. Se il valore è null, l'intera tabella è idonea alla migrazione. Per altre informazioni, vedere [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="Migration"></a>Controllare lo stato della migrazione dei dati
Selezionare **Attività | Estensione | Monitoraggio** per un database in SQL Server Management Studio per monitorare la migrazione dei dati in Monitoraggio Database Estensione. Per altre informazioni, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Database Estensione)](sql-server-stretch-database-monitor.md).

In alternativa, aprire la DMV **sys.dm\_db\_rda\_migration\_status** per visualizzare il numero di batch e righe di dati migrati.

## Aumentare il livello di prestazioni di Azure per le operazioni a elevato utilizzo di risorse, ad esempio l'indicizzazione
Quando si compila, ricompila o riorganizza un indice su una tabella di grandi dimensioni configurata per Database Estensione, provare ad aumentare il livello di prestazioni del database remoto corrispondente per tutta la durata dell'operazione.

## Non modificare lo schema della tabella remota
Non modificare lo schema di una tabella di Azure remota associata a una tabella di SQL Server configurata per Database Estensione. In particolare, non si deve modificare il nome o il tipo di dati di una colonna. La funzionalità Database Estensione ipotizza varie opzioni per lo schema della tabella remota in relazione allo schema della tabella di SQL Server. Se si modifica lo schema remoto, Database Estensione smette di funzionare per la tabella modificata.

## <a name="Firewall"></a>Risoluzione dei problemi di migrazione dei dati
Per avere suggerimenti sulla risoluzione dei problemi, vedere l'articolo relativo a [monitoraggio e risoluzione dei problemi di migrazione dei dati (Database Estensione)](sql-server-stretch-database-monitor.md).

## Risoluzione dei problemi di prestazioni delle query
**Le query che includono la tabella con estensione abilitata sono lente.** Sono previste prestazioni più lente per le query che includono tabelle con estensione abilitata rispetto al periodo antecedente all'abilitazione dell'estensione per le tabelle. Se le prestazioni delle query diminuiscono significativamente, esaminare i possibili problemi tra quelli riportati di seguito.

-   Il server Azure si trova in un'area geografica diversa rispetto a SQL Server? Configurare il server Azure in modo che si trovi nella stessa area geografica di SQL Server per ridurre la latenza di rete.

-   Possibile danneggiamento delle condizioni della rete. Per informazioni su problemi o interruzioni recenti, contattare l'amministratore di rete.

## Vedere anche
[Monitorare Database Estensione](sql-server-stretch-database-monitor.md) [Eseguire backup e ripristino di database con estensione abilitata](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0309_2016-->