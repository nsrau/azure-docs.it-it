<properties 
    pageTitle="Nuova configurazione del database SQL con PowerShell | Microsoft Azure" 
    description="Informazioni su come creare un nuovo database SQL con PowerShell. Le comuni attività di configurazione del database possono essere gestite tramite i cmdlet di PowerShell." 
    keywords="creazione di un nuovo database sql,configurazione del database"
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="05/09/2016"
    ms.author="sstein"/>

# Creare un nuovo database SQL ed eseguire comuni attività di configurazione del database con i cmdlet di PowerShell 


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Informazioni su come creare un nuovo database SQL con i cmdlet di PowerShell. Per la creazione di database elastici, vedere [Creare un nuovo pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md).


[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]

## Configurazione del database: creare un gruppo di risorse, il server e la regola firewall

È ora possibile accedere per eseguire i cmdlet con la sottoscrizione di Azure selezionata, pertanto il passaggio successivo consiste nello stabilire il gruppo di risorse che contiene il server in cui verrà creato il database. È possibile modificare il comando successivo per utilizzare qualsiasi percorso valido scelto. Eseguire **(Get-AzureRmLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** per ottenere un elenco di percorsi validi.

Usare il comando seguente per creare un nuovo gruppo di risorse:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Dopo aver creato un nuovo gruppo di risorse, sullo schermo verranno visualizzate informazioni tra cui **ProvisioningState: Succeeded**.


### Creare un server 

I database SQL vengono creati all'interno dei server di database SQL di Azure. Per creare un nuovo server, eseguire **New-AzureRmSqlServer**. Sostituire ServerName con il nome del server. Deve essere univoco per tutti i server SQL di Azure, altrimenti se il nome del server è già in uso verrà visualizzato un messaggio di errore. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. È possibile modificare il comando per usare qualsiasi percorso valido, ma è consigliabile scegliere la stessa posizione usata per il gruppo di risorse creato nel passaggio precedente.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.

Dopo aver creato il server, verranno visualizzati i relativi dettagli.

### Configurare una regola firewall del server per consentire l'accesso al server

Definire una regola firewall per accedere al server. Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il computer.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Dopo aver creato la regola firewall, verranno visualizzati i relativi dettagli.

Per consentire ad altri servizi di Azure di accedere al server, aggiungere una regola firewall e impostare StartIpAddress e EndIpAddress su 0.0.0.0. Si noti che ciò consente al traffico di Azure proveniente da qualsiasi sottoscrizione di Azure di accedere al server.

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md).


## Creare un nuovo database SQL

Ora si dispone di un gruppo di risorse, un server e una regola del firewall con una configurazione che consente di accedere al server.

Il comando seguente consente di creare un nuovo database SQL (vuoto) al livello di servizio Standard con un livello di prestazioni S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Dopo aver creato il database, verranno visualizzati i relativi dettagli.

## Creare un nuovo script di PowerShell per database SQL

    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    $ResourceGroupName = "resourcegroupname"
    $Location = "Japan West"
    
    $ServerName = "uniqueservername"
    
    $FirewallRuleName = "rule1"
    $FirewallStartIP = "192.168.0.0"
    $FirewallEndIp = "192.168.0.0"
    
    $DatabaseName = "database1"
    $DatabaseEdition = "Standard"
    $DatabasePerfomanceLevel = "S1"
    
    
    Add-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Passaggi successivi
Dopo aver creato un nuovo database SQL e aver eseguito le attività di configurazione del database di base, è possibile:

- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)


## Risorse aggiuntive

- [Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0518_2016-->