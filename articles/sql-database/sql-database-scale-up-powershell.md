<properties 
    pageTitle="Modificare il livello di servizio e il livello delle prestazioni di un database SQL di Azure utilizzando PowerShell" 
    description="La modifica del livello del servizio e del livello delle prestazioni di un database SQL di Azure mostra come scalare il database SQL verso l’alto o verso il basso con PowerShell. Modificare il livello di prezzi di un database SQL di Azure con PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/23/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modificare il livello di servizio e il livello di prestazioni (livello di prezzo) di un database SQL con PowerShell


> [AZURE.SELECTOR]
- [Azure portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


In questo articolo viene illustrato come modificare il livello del servizio e il livello delle prestazioni del database SQL con PowerShell.

Utilizzare le informazioni contenute negli argomenti [Aggiornamento delle edizioni Web e Business del database SQL ai nuovi livelli di servizio](sql-database-upgrade-server-portal.md) e [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md) per determinare il livello di prestazioni e il livello di servizio appropriati per il database SQL di Azure.

> [AZURE.IMPORTANT] La modifica del livello di servizio e del livello delle prestazioni di un database SQL è un'operazione in linea. Ciò significa che il database rimane online e disponibile durante l'intera operazione senza tempi di inattività.

- Per effettuare il downgrade di un database, la dimensione di quest'ultimo deve essere inferiore alla dimensione massima consentita per il livello del servizio di destinazione. 
- Quando si aggiorna un database con la [replica geografica](sql-database-geo-replication-portal) abilitata, è necessario aggiornare i database secondari al livello di prestazioni desiderato prima di aggiornare il database primario.
- Quando si effettua il downgrade da un livello di servizio Premium, è necessario prima terminare tutte le relazioni di replica geografica. È possibile attenersi alla procedura descritta nell'argomento [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per arrestare il processo di replica tra il database primario e i database secondari attivi.
- Le offerte per il ripristino del servizio sono diverse per i vari livelli di servizio. Se si effettua il downgrade è possibile che la capacità di eseguire un ripristino temporizzato o di disporre di un periodo di mantenimento del backup inferiore vengano perse. Per ulteriori informazioni, vedere [Backup e ripristino del database SQL di Azure](sql-database-business-continuity.md).
- È possibile apportare fino a quattro modifiche di singoli database (livello di servizio o di prestazioni) nell'arco di un periodo di 24 ore.
- Le nuove proprietà del database non vengono applicate finché non sono state completate le modifiche.



**Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:**

- Una sottoscrizione di Azure. Se è necessaria una sottoscrizione ad Azure, fare clic su **ACCOUNT GRATUITO** nella parte superiore della pagina, quindi tornare all'articolo.
- un database SQL di Azure. Se non si dispone di un database SQL, crearne uno seguendo i passaggi indicati in questo articolo: [Creare il primo database SQL di Azure](sql-database-get-started.md).
- Azure PowerShell.


Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).



## Configurare le credenziali e selezionare la sottoscrizione

È innanzitutto necessario stabilire l'accesso all'account Azure, poi avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Login-AzureRmAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario l’ID sottoscrizione o il nome della sottoscrizione (**-SubscriptionName**) È possibile copiare l'ID sottoscrizione dalle informazioni visualizzate nel passaggio precedente o, se si dispone di più sottoscrizioni e si necessita di altre informazioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId




## Modificare il livello di servizio e il livello delle prestazioni del database SQL

Eseguire il cmdlet **Set-AzureRmSqlDatabase** e impostare **-RequestedServiceObjectiveName** sul livello di prestazioni del piano tariffario desiderato, ad esempio *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## Script di esempio di PowerShell per modificare il livello di servizio e il livello delle prestazioni del database SQL

    

    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Passaggi successivi

- [Scalare orizzontalmente e verticalmente](sql-database-elastic-scale-get-started.md)
- [Connettersi al database SQL con SSMS ed eseguire query](sql-database-connect-query-ssms.md)
- [Esportare un database SQL di Azure](sql-database-export-powershell.md)

## Risorse aggiuntive

- [Panoramica sulla continuità aziendale](sql-database-business-continuity.md)
- [Documentazione relativa al database SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlet del database SQL di Azure.](http://msdn.microsoft.com/library/mt574084.aspx)

<!---HONumber=AcomDC_0224_2016-->