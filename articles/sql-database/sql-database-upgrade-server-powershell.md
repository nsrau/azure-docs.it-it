<properties 
	pageTitle="Eseguire l'aggiornamento a database SQL V12 di Azure tramite PowerShell | Microsoft Azure" 
	description="Viene spiegato come eseguire l'aggiornamento alla V12 del Database SQL di Azure, come eseguire l'aggiornamento di database Web e Business e come aggiornare un server V11 che sta migrando i database direttamente in un pool di database elastici con PowerShell." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="sstein"/>

# Eseguire l'aggiornamento a database SQL V12 di Azure tramite PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-upgrade-server-portal.md)
- [PowerShell](sql-database-upgrade-server-powershell.md)


Database SQL V12 è la versione più recente, pertanto, è consigliabile eseguire l'aggiornamento alla versione 12 del Database SQL. Il database SQL V12 presenta molti [vantaggi rispetto alla versione precedente](sql-database-v12-whats-new.md), tra cui:

- Miglioramento della compatibilità con SQL Server.
- Maggiori prestazioni Premium e nuovi livelli di prestazioni.
- [Pool di database elastici](sql-database-elastic-pool.md).

In questo articolo vengono fornite istruzioni per l'aggiornamento di database e server di database SQL V11 esistenti alla versione 12 del database SQL.

Durante il processo di aggiornamento alla versione 12 verranno aggiornati tutti i database Web e Business a un nuovo livello di servizio in modo che siano incluse le istruzioni per l'aggiornamento dei database Web e Business.

Inoltre, la migrazione a un [pool di database elastici](sql-database-elastic-pool.md) può essere più conveniente rispetto all'aggiornamento a livelli di prestazioni singoli (livelli di prezzi) per singoli database. I pool inoltre semplificano la gestione dei database perché è sufficiente gestire le impostazioni delle prestazioni per il pool anziché gestire separatamente i livelli di prestazione dei singoli database. Se si dispone di database in più server è consigliabile spostarli nello stesso server e usufruire del vantaggio di metterli in un pool.

È possibile migrare automaticamente e facilmente i database dai server della V11 direttamente nel pool di database elastici seguendo i passaggi in questo articolo.

Si noti che i database rimarranno online e continueranno a funzionare durante l'operazione di aggiornamento. Al momento della transizione effettiva al nuovo livello di prestazioni, le connessioni al database possono interrompersi temporaneamente per un periodo molto breve che in genere è di circa 90 secondi fino a un massimo di 5 minuti. Se un'applicazione ha una [gestione degli errori temporanei per le interruzioni di connessione](sql-database-connect-central-recommendations.md), è sufficiente impostare la protezione dalle connessioni interrotte alla fine dell'aggiornamento.

L'aggiornamento al database SQL V12 non può essere annullato. Dopo un aggiornamento, nel server non può essere ripristinata la versione 11.

Dopo l'aggiornamento alla V12, [le indicazioni per i livelli di servizio](sql-database-service-tier-advisor.md) e [le indicazioni per i pool elastici](sql-database-elastic-pool-portal.md#step-2-choose-a-pricing-tier) non saranno immediatamente disponibili fino a quando il servizio non disporrà di tempo per valutare i carichi di lavoro nel nuovo server. La cronologia delle indicazioni del server versione 11 non è applicabile ai server versione 12 e pertanto non viene conservata.

## Preparare l'aggiornamento

- **Aggiornare tutti i database Web e Business**: vedere la sezione[Aggiornare tutti i database Web e Business](sql-database-v12-upgrade.md#upgrade-all-web-and-business-databases) o utilizzare [PowerShell per aggiornare database e server](sql-database-upgrade-server-powershell.md).
- **Rivedere e sospendere la replica geografica**: se il database SQL di Azure è configurato per la replica geografica è necessario documentare la configurazione attuale e [arrestare la replica geografica](sql-database-geo-replication-portal.md#remove-secondary-database). Al termine dell'aggiornamento, riconfigurare il database per la replica geografica.
- **Aprire queste porte se si dispone di client sulle macchine virtuali di Azure**: Se il programma client si connette a Database SQL V12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire gli intervalli di porta 11000-11999 e 14000-14999 nella macchina virtuale: Per ulteriori informazioni, vedere [porte per SQL Database V12](sql-database-develop-direct-route-ports-adonet-v12.md).


## Prerequisiti 

Per eseguire aggiornamento di un server alla versione 12 con PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a seconda della versione potrebbe essere necessario passarlo alla modalità di gestione delle risorse per accedere ai cmdlet di PowerShell di Gestione risorse di Azure.

Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## Configurare le credenziali e selezionare la sottoscrizione

Per eseguire i cmdlet PowerShell nella sottoscrizione di Azure, è necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureRmAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso

Per selezionare la sottoscrizione con cui lavorare, è necessario l’ID sottoscrizione (**-SubscriptionId**) o il nome della sottoscrizione (**-SubscriptionName**). È possibile copiarlo dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati.

Eseguire il cmdlet seguente con le informazioni della sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

I comandi seguenti verranno eseguiti con la sottoscrizione appena selezionata.

## Ottenere raccomandazioni

Per ottenere le indicazioni per l'aggiornamento del server eseguire il cmdlet seguente:

    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

Per altre informazioni, vedere [Creare un pool scalabile di database elastici per database SQL nel portale di Azure](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) e [Indicazioni sui livelli di prezzo del database SQL](sql-database-service-tier-advisor.md).



## Avviare l'aggiornamento

Per avviare l'aggiornamento del server di eseguire il cmdlet seguente:

    Start-AzureRmSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


Quando si esegue questo comando, inizia il processo di aggiornamento. È possibile personalizzare l'output dell’indicazione e fornire le indicazioni modificate a questo cmdlet.


## Aggiornare un server


    # Adding the account
    #
    Add-AzureRmAccount
    
    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureRmSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureRmSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## Mapping di aggiornamento personalizzato

Se le indicazioni non sono appropriate per il server e il caso aziendale, è possibile scegliere la modalità di aggiornamento dei database e è possibile eseguirne il mapping a database singoli o elastici.

I parametri ElasticPoolCollection e DatabaseCollection sono facoltativi:
    
    # Creating elastic pool mapping
    #
    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties 
    $elasticPool.DatabaseDtuMax = 100 
    $elasticPool.DatabaseDtuMin = 0 
    $elasticPool.Dtu = 800 
    $elasticPool.Edition = "Standard" 
    $elasticPool.DatabaseCollection = ("DB1", “DB2”, “DB3”, “DB4”) 
    $elasticPool.Name = "elasticpool_1" 
    $elasticPool.StorageMb = 800 
     
    # Creating single database mapping for 2 databases. DBMain1 mapped to S0 and DBMain2 mapped to S2
    #
    $databaseMap1 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap1.Name = "DBMain1" 
    $databaseMap1.TargetEdition = "Standard" 
    $databaseMap1.TargetServiceLevelObjective = "S0"
    
    $databaseMap2 = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties 
    $databaseMap2.Name = "DBMain2" 
    $databaseMap2.TargetEdition = "Standard" 
    $databaseMap2.TargetServiceLevelObjective = "S2"
     
    # Starting the upgrade
    #
    Start-AzureRmSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection @($databaseMap1, $databaseMap2) -ElasticPoolCollection @($elasticPool) 

    

## Monitorare i database dopo l'aggiornamento alla V12 di Database SQL


Dopo l’aggiornamento, si consiglia di monitorare il database in maniera attiva per assicurarsi che le applicazioni offrano le prestazioni desiderati e per ottimizzare l’utilizzo in base alle esigenze.

Oltre al monitoraggio dei singoli database, è possibile monitorare i pool di database elastici [tramite il portale](sql-database-elastic-pool-portal.md#monitor-and-manage-an-elastic-database-pool) o con [PowerShell](sql-database-elastic-pool-powershell.md#monitoring-elastic-databases-and-elastic-database-pools)


**Dati sul consumo delle risorse:** per i database Basic, Standard e Premium i dati sul consumo delle risorse sono disponibili tramite il DMV [sys.dm\_ db\_resource\_stats](http://msdn.microsoft.com/library/azure/dn800981.aspx) nel database utente. Questa DMV fornisce informazioni sul consumo delle risorse in tempo reale con una granularità di 15 secondi per l'ora precedente di funzionamento. Il consumo percentuale di DTU per un intervallo viene calcolato come consumo percentuale massimo delle dimensioni di CPU, IO e log. Ecco una query per calcolare il consumo percentuale medio di DTU nell'ultima ora:

    SELECT end_time
    	 , (SELECT Max(v)
             FROM (VALUES (avg_cpu_percent)
                         , (avg_data_io_percent)
                         , (avg_log_write_percent)
    	   ) AS value(v)) AS [avg_DTU_percent]
    FROM sys.dm_db_resource_stats
    ORDER BY end_time DESC;

Informazioni di monitoraggio aggiuntive:

- [Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](http://msdn.microsoft.com/library/azure/dn369873.aspx).
- [Considerazioni di prezzo e prestazioni per un pool di database elastici](sql-database-elastic-pool-guidance.md).
- [Monitoraggio del database SQL di Azure tramite le visualizzazioni di gestione dinamica](sql-database-monitoring-with-dmvs.md)



**Avvisi:** configurare gli avvisi nel portale di Azure per ricevere una notifica quando il consumo di DTU per un database aggiornato si avvicina a un livello elevato specifico. Nel portale di Azure si possono configurare avvisi di database per diverse metriche delle prestazioni, come DTU, CPU, IO e log. Individuare il database e selezionare **Regole di avviso** nel pannello **Impostazioni**.

Ad esempio, è possibile impostare un avviso di posta elettronica sulla percentuale DTU se il valore medio della percentuale DTU supera il 75% negli ultimi 5 minuti. Fare riferimento a [Ricevere notifiche di avviso](../azure-portal/insights-receive-alert-notifications.md) per altre informazioni sulla configurazione di notifiche di avviso.



## Passaggi successivi

- [Creare un pool di database elastici](sql-database-elastic-pool-portal.md) e aggiungere alcuni o tutti i database nel pool.
- [Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL](sql-database-scale-up.md).



## Informazioni correlate

- [Get-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRmSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

<!---HONumber=AcomDC_0224_2016-->