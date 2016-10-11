<properties
    pageTitle="Nuova configurazione del database SQL con PowerShell | Microsoft Azure"
    description="Informazioni su come creare un database SQL con PowerShell. Le comuni attività di configurazione del database possono essere gestite tramite i cmdlet di PowerShell."
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
    ms.date="08/19/2016"
    ms.author="sstein"/>  

# Creare un database SQL ed eseguire comuni attività di configurazione del database con i cmdlet di PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Informazioni su come creare un database SQL usando i cmdlet di PowerShell. Per la creazione di database elastici, vedere [Creare un nuovo pool di database elastici con PowerShell](sql-database-elastic-pool-create-powershell.md).


[AZURE.INCLUDE [Avviare la sessione di PowerShell](../../includes/sql-database-powershell.md)]

## Configurazione del database: creare un gruppo di risorse, il server e la regola firewall

Dopo avere ottenuto l'accesso per eseguire i cmdlet con la sottoscrizione di Azure selezionata, il passaggio successivo consiste nello stabilire il gruppo di risorse che contiene il server in cui verrà creato il database. È possibile modificare il comando successivo per utilizzare qualsiasi percorso valido scelto. Eseguire **(Get-AzureRmLocation | Where-Object { $\_.Providers -eq "Microsoft.Sql" }).Location** per ottenere un elenco di località valide.

Usare il comando seguente per creare un gruppo di risorse:

	New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### Creare un server

I database SQL vengono creati all'interno dei server di database SQL di Azure. Per creare un server, eseguire **New-AzureRmSqlServer**. Il nome del server deve essere univoco in tutti i server di database SQL di Azure. Se il nome del server è già in uso, viene visualizzato un errore. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. È possibile modificare il comando per usare qualsiasi località valida, ma è consigliabile scegliere la stessa usata per il gruppo di risorse creato nel passaggio precedente.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Quando si esegue questo comando, vengono richiesti il nome utente e la password. Non immettere le credenziali di Azure. Immettere invece il nome utente e la password da creare come amministratore del server. Lo script alla fine di questo articolo illustra come impostare le credenziali del server nel codice.

Dopo aver creato il server, verranno visualizzati i relativi dettagli.

### Configurare una regola firewall del server per consentire l'accesso al server

Per accedere al server, è necessario definire una regola firewall. Eseguire il comando seguente sostituendo gli indirizzi IP iniziale e finale con i valori validi per il computer locale.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Dopo aver creato la regola firewall, verranno visualizzati i relativi dettagli.

Per consentire ad altri servizi di Azure di accedere al server, aggiungere una regola del firewall e impostare StartIpAddress e EndIpAddress su 0.0.0.0. Questa regola consente al traffico di Azure proveniente da qualsiasi sottoscrizione di Azure di accedere al server.

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md).


## Creazione di un database SQL

Ora si dispone di un gruppo di risorse, un server e una regola del firewall con una configurazione che consente di accedere al server.

Il comando seguente consente di creare un database SQL (vuoto) al livello di servizio Standard con un livello di prestazioni S1:


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Dopo aver creato il database, verranno visualizzati i relativi dettagli.

## Creare uno script di PowerShell per database SQL

Lo script di PowerShell seguente crea un database SQL e tutte le risorse dipendenti. Sostituire tutti gli elementi `{variables}` con i valori specifici della sottoscrizione e delle risorse. Rimuovere **{}** quando si impostano i valori.

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## Passaggi successivi
Dopo aver creato un database SQL e aver eseguito le attività di configurazione del database di base, è possibile:

- [Gestire il database SQL con PowerShell](sql-database-manage-powershell.md)
- [Connettersi al database SQL con SQL Server Management Studio ed eseguire una query T-SQL di esempio](sql-database-connect-query-ssms.md)


## Risorse aggiuntive

- [Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1005_2016-->