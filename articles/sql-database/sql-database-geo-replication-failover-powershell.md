<properties 
    pageTitle="Avviare un failover pianificato o non pianificato per il database SQL di Azure con PowerShell | Microsoft Azure" 
    description="Avviare un failover pianificato o non pianificato per il database SQL di Azure usando PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="07/19/2016"
    ms.author="sstein"/>

# Avviare un failover pianificato o non pianificato per il database SQL di Azure con PowerShell



> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Questo articolo illustra come avviare un failover pianificato o non pianificato per il database SQL con PowerShell. Per configurare la replica geografica, vedere [Configurare la replica geografica per il database SQL di Azure con PowerShell](sql-database-geo-replication-powershell.md).



## Avviare un failover pianificato

Usare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con il parametro **-Failover** per alzare di livello un database secondario a nuovo database primario, abbassando di livello il database primario esistente a database secondario. Questa funzionalità è progettata per il failover pianificato, ad esempio durante le esercitazioni sul ripristino di emergenza, e richiede che il database primario sia disponibile.

Il comando esegue il flusso di lavoro seguente:

1. Passa temporaneamente alla modalità di replica sincrona, in modo che tutte le transazioni in sospeso siano scaricate nel server secondario.

2. Scambia i ruoli dei due database nella relazione di replica geografica.

Questa sequenza garantisce che i due database siano sincronizzati prima dello scambio di ruoli, in modo da evitare la perdita di dati. Per un breve periodo, da 0 a 25 secondi, entrambi i database non sono disponibili mentre vengono scambiati i ruoli. Il completamento dell'intera operazione dovrebbe richiedere meno di un minuto in circostanze normali. Per altre informazioni, vedere [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).




Il risultato di questo cmdlet verrà restituito quando il processo di scambio da database secondario a primario sarà completato.

Il comando seguente cambia in primario i ruoli del database denominato "mydb” nel server "srv2” del gruppo di risorse "rg2”. Il database primario originale a cui è stato connesso "db2” diventerà secondario dopo la sincronizzazione completa dei due database.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] In rari casi, è possibile che l'operazione non possa essere completata e appaia bloccata. In questo caso l'utente può eseguire il comando di failover forzato, ovvero un failover non pianificato, e accettare la perdita dei dati.


## Avviare un failover non pianificato dal database primario al database secondario


È possibile usare il cmdlet **Set-AzureRmSqlDatabaseSecondary** con i parametri **–Failover** e **-AllowDataLoss** per alzare di livello un database secondario a nuovo database primario in maniera non pianificata, forzando l'abbassamento di livello del database primario esistente a database secondario in un momento in cui il database primario non è più disponibile.

Questa funzionalità è progettata per il ripristino di emergenza quando il ripristino della disponibilità del database è critica ed è accettabile una perdita di dati. Quando si richiama il failover forzato, il database secondario specificato diventa immediatamente il database primario e inizia ad accettare le transazioni di scrittura. Non appena il database primario originale può riconnettersi a questo nuovo database primario dopo l'operazione di failover forzato, viene eseguito un backup incrementale nel database primario originale e il database primario precedente viene convertito in un database secondario per il nuovo database primario. In seguito si tratterà semplicemente di una replica del nuovo database.

Poiché tuttavia il ripristino temporizzato non è supportato nei database secondari, se l'utente vuole recuperare i dati di cui è stato eseguito il commit nel database primario precedente e che non sono stati replicati nel nuovo database primario, dovrà rivolgersi al supporto tecnico per il ripristino di un database al backup del log noto.

> [AZURE.NOTE] Se il comando viene eseguito quando i database primario e secondario sono online, il database primario precedente diventerà il nuovo database secondario, senza alcuna sincronizzazione dei dati. Se il database primario sta eseguendo il commit di transazioni al momento dell'esecuzione del comando, può verificarsi la perdita di dati.


Se il database primario ha più database secondari, il comando riuscirà in parte. Il database secondario su cui è stato eseguito il comando diventerà il database primario. Il database primario precedente rimarrà tuttavia primario, ovvero i due database primari rimarranno in uno stato non coerente e connessi da un collegamento di replica sospeso. L'utente dovrà ripristinare manualmente questa configurazione usando un'API di "rimozione del database secondario" in uno di questi database primari.


Il comando seguente cambia i ruoli del database denominato "mydb” in primario quando il database primario non è disponibile. Il database primario originale a cui è stato connesso "mydb” diventerà secondario dopo essere ritornato online. A questo punto la sincronizzazione potrebbe causare una perdita di dati.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## Passaggi successivi   

- Per informazioni sul ripristino di emergenza tramite replica geografica attiva, incluse procedure preliminari e successive a un ripristino e un'esercitazione per il ripristino di emergenza, vedere [Ripristinare un database SQL di Azure o eseguire il failover in un database secondario](sql-database-disaster-recovery.md)
- Per un post di blog di Sasha Nosov sulla replica geografica attiva, vedere [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Nuove funzionalità di replica geografica in evidenza)
- Per informazioni sulla progettazione di applicazioni cloud per l'uso della replica geografica attiva, vedere [Progettare un'applicazione per il ripristino di emergenza cloud mediante la replica geografica attiva nel database SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- Per informazioni sull'uso della replica geografica attiva con pool di database elastici, vedere [Strategie di ripristino di emergenza per applicazioni che usano il pool elastico del database SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
- Per un quadro generale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0727_2016-->