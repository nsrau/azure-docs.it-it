<properties 
    pageTitle="Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL | Microsoft Azure" 
    description="Avviare un failover pianificato o non pianificato per il database SQL di Azure usando Transact-SQL" 
    services="sql-database" 
    documentationCenter="" 
    authors="carlrabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="04/27/2016"
    ms.author="carlrab"/>

# Avviare un failover pianificato o non pianificato per il database SQL di Azure con Transact-SQL


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [Transact-SQL](sql-database-geo-replication-failover-transact-sql.md)


Questo articolo illustra come avviare il failover su un database SQL secondario con Transact-SQL. Per configurare la replica geografica, vedere [Configurare la replica geografica per il database SQL di Azure](sql-database-geo-replication-transact-sql.md).



Per avviare il failover, è necessario quanto segue:

- Un account di accesso con il ruolo DBManager nel database primario e con db\_ownership del database locale per il quale verrà eseguita la replica geografica, oltre al ruolo DBManager nei server partner in cui si configurerà la replica geografica.
- La versione più recente di SQL Server Management Studio: per ottenere la versione più recente di SQL Server Management Studio (SSMS), visitare la pagina di [download di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Per informazioni sull'uso di SQL Server Management Studio per gestire database e server logici di database SQL di Azure, vedere [Gestione di database SQL di Azure tramite SQL Server Management Studio](sql-database-manage-azure-ssms.md)



## Avviare un failover pianificato per alzare di livello un database secondario per diventare il nuovo database primario

È possibile usare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario a nuovo database primario in maniera pianificata, abbassando di livello il database primario esistente a database secondario. Questa istruzione viene eseguita sul database master nel server logico di database SQL di Azure in cui risiede il database secondario con replica geografica che viene alzato di livello. Questa funzionalità è progettata per il failover pianificato, ad esempio durante le esercitazioni sul ripristino di emergenza, e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, in modo che tutte le transazioni in sospeso siano scaricate nel server secondario e le nuove transazioni vengano bloccate.

2. Scambia i ruoli dei due database nella relazione di replica geografica.

Questa sequenza garantisce che i due database siano sincronizzati prima dello scambio di ruoli, in modo da evitare la perdita di dati. Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Se il database primario dispone di più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. Per altre informazioni, vedere [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Livelli di servizio](sql-database-service-tiers.md).


Usare la procedura seguente per avviare un failover pianificato.

1. In Management Studio connettersi al server logico di database SQL di Azure in cui risiede un database secondario con replica geografica.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare la seguente istruzione **ALTER DATABASE** per passare il database secondario al ruolo di database primario.

        ALTER DATABASE <MyDB> FAILOVER;

4. Fare clic su **Execute** per eseguire la query.

>[AZURE.NOTE] In rari casi, è possibile che l'operazione non possa essere completata e appaia bloccata. In questo caso l'utente può eseguire il comando di failover forzato e accettare la perdita dei dati.


## Avviare un failover non pianificato dal database primario al database secondario

È possibile usare l'istruzione **ALTER DATABASE** per alzare di livello un database secondario a nuovo database primario in maniera non pianificata, forzando l'abbassamento di livello del database primario esistente a database secondario in un momento in cui il database primario non è più disponibile. Questa istruzione viene eseguita sul database master nel server logico di database SQL di Azure in cui risiede il database secondario con replica geografica che viene alzato di livello.

Questa funzionalità è progettata per il ripristino di emergenza quando il ripristino della disponibilità del database è critica ed è accettabile una perdita di dati. Quando si richiama il failover forzato, il database secondario specificato diventa immediatamente il database primario e inizia ad accettare le transazioni di scrittura. Non appena il database primario originale può riconnettersi a questo nuovo database primario, viene eseguito un backup incrementale nel database primario originale e il database primario precedente viene convertito in un database secondario per il nuovo database primario. In seguito si tratterà semplicemente di una replica di sincronizzazione del nuovo database.

Poiché tuttavia il ripristino temporizzato non è supportato nei database secondari, se l'utente vuole recuperare i dati di cui è stato eseguito il commit nel database primario precedente e che non sono stati replicati nel nuovo database primario prima del failover forzato, dovrà rivolgersi al supporto tecnico per il ripristino dei dati persi.

Se il database primario dispone di più database secondari, il comando riconfigura automaticamente gli altri database secondari per la connessione al nuovo database primario.

Usare la procedura seguente per avviare un failover non pianificato.

1. In Management Studio connettersi al server logico di database SQL di Azure in cui risiede un database secondario con replica geografica.

2. Aprire la cartella Database, espandere la cartella **Database di sistema** fare clic con il pulsante destro del mouse su **master** e quindi scegliere **Nuova query**.

3. Usare la seguente istruzione **ALTER DATABASE** per passare il database secondario al ruolo di database primario.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Fare clic su **Execute** per eseguire la query.

>[AZURE.NOTE] Se il comando viene eseguito quando i database primario e secondario sono online, il database primario precedente diventerà il nuovo database secondario, senza alcuna sincronizzazione dei dati. Se il database primario sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.



## Risorse aggiuntive

- [Nuove funzionalità di replica geografica in evidenza](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Progettazione di applicazioni cloud per la continuità aziendale mediante la replica geografica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](https://azure.microsoft.com/services/sql-database/)
- [Esercitazioni di ripristino di emergenza](sql-database-disaster-recovery-drills.md)

<!---HONumber=AcomDC_0504_2016-->