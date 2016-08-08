<properties 
    pageTitle="Configurare la replica geografica attiva per il database SQL di Azure con PowerShell | Microsoft Azure" 
    description="Configurare la replica geografica attiva per il database SQL di Azure con PowerShell" 
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
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# Configurare la replica geografica per il database SQL di Azure con PowerShell

> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-replication-overview.md)
- [Portale di Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Questo articolo illustra come configurare la replica geografica attiva per il database SQL con PowerShell.

Per avviare il failover usando PowerShell, vedere [Avviare un failover pianificato o non pianificato per il database SQL di Azure con PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] La replica geografica attiva (database secondari accessibili in lettura) è ora disponibile per tutti i database in tutti i livelli di servizio. Nell'aprile 2017 il tipo di database secondario non leggibile verrà ritirato e i database non leggibili esistenti verranno aggiornati automaticamente a database secondari accessibili in lettura.



Per configurare la replica geografica attiva tramite PowerShell, sono necessari gli elementi seguenti:

- Una sottoscrizione di Azure.
- Un database SQL di Azure: il database primario che si vuole replicare.
- Azure PowerShell 1.0 o versione successiva È possibile scaricare e installare i moduli di Azure PowerShell dalla pagina [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.


	Login-AzureRmAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario il relativo ID. Questo può essere copiato dal passaggio precedente oppure, se si dispone di più sottoscrizioni e sono necessarie altre informazioni, è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Il cmdlet seguente usa l'ID sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureRmSubscription**, si ritornerà al prompt dei comandi di PowerShell.


## Aggiungere un database secondario


La procedura seguente crea un nuovo database secondario in una relazione di replica geografica.
  
Per abilitare un database secondario è necessario essere il proprietario o il comproprietario della sottoscrizione.

È possibile usare il cmdlet **New-AzureRmSqlDatabaseSecondary** per aggiungere un database secondario in un server partner a un database locale nel server a cui si è connessi, ovvero il database primario.

Il cmdlet sostituisce **Start-AzureSqlDatabaseCopy** con il parametro **–IsContinuous**. Genera un oggetto **AzureRmSqlDatabaseSecondary** che può essere usato da altri cmdlet per identificare chiaramente un collegamento di replica specifico. Il risultato di questo cmdlet verrà restituito dopo aver creato il database secondario e averne effettuato correttamente il seeding. A seconda delle dimensioni del database, l'operazione può richiedere da pochi minuti a diverse ore.

Il database replicato nel server secondario avrà lo stesso nome del database nel server primario e, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere accessibile o non accessibile in lettura e può essere un database singolo o un database elastico. Per altre informazioni, vedere [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) e [Livelli di servizio del database SQL](sql-database-service-tiers.md). Dopo aver creato ed effettuato il seeding del database secondario, inizierà la replica dei dati dal database primario al nuovo database secondario. I passaggi seguenti descrivono come eseguire questa attività con PowerShell per creare database secondari accessibili e non accessibili in lettura, sia con un database singolo che con un database elastico.

Se il database partner esiste già, ad esempio come risultato della terminazione di una precedente relazione di replica geografica, il comando non riuscirà.



### Aggiungere un database secondario non accessibile in lettura (database singolo)

Il comando seguente crea un database secondario non accessibile in lettura del database "mydb" nel server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Aggiungere un database secondario accessibile in lettura (database singolo)

Il comando seguente crea un database secondario accessibile in lettura del database "mydb" nel server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Aggiungere un database secondario non accessibile in lettura (database elastico)

Il comando seguente crea un database secondario non accessibile in lettura del database "mydb" nel pool di database elastici denominato "ElasticPool1" del server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Aggiungere un database secondario accessibile in lettura (database elastico)

Il comando seguente crea un database secondario accessibile in lettura del database "mydb" nel pool di database elastici denominato "ElasticPool1" del server "srv2" nel gruppo di risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Rimuovere un database secondario

È possibile usare il cmdlet **Remove-AzureRmSqlDatabaseSecondary** per terminare definitivamente la relazione di replica tra un database secondario e il relativo database primario. Dopo la terminazione della relazione, il database secondario diventa un database accessibile in lettura e scrittura. Se la connettività al database secondario viene interrotta, il comando riesce ma il database diventerà di nuovo accessibile in lettura e scrittura al ripristino della connettività. Per altre informazioni, vedere [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) e [Livelli di servizio del database SQL](sql-database-service-tiers.md).

Questo cmdlet sostituisce Stop-AzureSqlDatabaseCopy per la replica.

Questa rimozione equivale a una terminazione forzata che rimuove il collegamento di replica. Il database secondario diventa quindi un database autonomo che non viene completamente replicato prima della terminazione. Tutti i dati relativi al collegamento vengono puliti sia sui database primario e secondario precedenti, se e quando sono disponibili. Il risultato di questo cmdlet verrà restituito quando viene rimosso il collegamento di replica.


Per rimuovere il database secondario gli utenti devono aver accesso in scrittura sia database primario e secondario, secondo il controllo degli accessi in base al ruolo. Per altre informazioni, vedere Controllo degli accessi in base al ruolo.

Il codice seguente rimuove il collegamento di replica del database denominato "mydb" nel server "srv2" del gruppo di risorse "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## Monitorare la configurazione di replica geografica e l'integrità

Le attività di monitoraggio includono il controllo della configurazione della replica geografica e dell'integrità della replica dei dati.

È possibile usare [Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) per recuperare le informazioni sui collegamenti di replica visibili nella vista del catalogo sys.geo\_replication\_links.

Il comando seguente recupera lo stato del collegamento di replica tra il database primario "mydb” e il database secondario nel server "srv2” del gruppo di risorse "rg2”.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## Passaggi successivi

- Per altre informazioni sulla replica geografica attiva, vedere [Replica geografica attiva](sql-database-geo-replication-overview.md)
- Per la panoramica e gli scenari della continuità aziendale, vedere [Continuità aziendale del database SQL di Azure](sql-database-business-continuity.md)

<!---HONumber=AcomDC_0727_2016-->