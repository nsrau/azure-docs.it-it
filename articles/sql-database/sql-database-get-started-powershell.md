<properties 
    pageTitle="Creare un database SQL di Azure tramite PowerShell" 
    description="Creare un database SQL di Azure tramite PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="10/20/2015"
    ms.author="sstein"/>

# Creare un database SQL tramite PowerShell

**Database singolo**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)


## Panoramica

Questo articolo illustra come creare un database SQL tramite PowerShell.

> [AZURE.IMPORTANT]Iniziando con la versione di anteprima di Azure PowerShell 1.0, il cmdlet Switch-AzureMode non è più disponibile e i cmdlet che sono stati nel modulo Azure ResourceManager sono stati rinominati. Gli esempi in questo articolo utilizzano nuove convenzioni di denominazione dell’Anteprima di PowerShell 1.0. Per informazioni dettagliate, vedere [Deprecazione di Switch-AzureMode, in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Per eseguire i cmdlet di PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a causa della rimozione di Switch-AzureMode, scaricare e installare la versione più recente di Azure PowerShell eseguendo l’[installazione guidata della piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

- Se è necessaria una sottoscrizione ad Azure, fare semplicemente clic su **VERSIONE DI PROVA GRATUITA** nella parte superiore della pagina, quindi tornare e proseguire fino alla fine di questo articolo.


## Configurare le credenziali e selezionare la sottoscrizione

Ora che si esegue il modulo di Gestione risorse di Azure, è possibile accedere a tutti i cmdlet necessari per creare un database SQL.

È necessario innanzitutto stabilire l'accesso all'account Azure, quindi eseguire il cmdlet seguente e verrà visualizzata una schermata di accesso per l'immissione delle credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureRMAccount

Dopo aver effettuato l'accesso, sullo schermo verranno visualizzate informazioni tra cui l'ID usato per l'accesso con le sottoscrizioni di Azure per le quali si dispone dell'accesso.


### Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione, è necessario il relativo ID. È possibile copiarlo dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureRMSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati. Una volta acquisita la sottoscrizione, eseguire il cmdlet seguente:

	Select-AzureRMSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Dopo aver eseguito correttamente il cmdlet **Select-AzureRMSubscription**, verrà nuovamente visualizzato il prompt dei comandi di PowerShell. Se si dispone di più di una sottoscrizione, è possibile eseguire il cmdlet **Get-AzureRMSubscription** e verificare che nella sottoscrizione che si desidera usare sia visualizzato **IsCurrent : True**.

## Creare un gruppo di risorse, il server e la regola firewall

È ora possibile accedere per eseguire i cmdlet con la sottoscrizione di Azure selezionata, pertanto il passaggio successivo consiste nello stabilire il gruppo di risorse che contiene il server in cui verrà creato il database. È possibile modificare il comando successivo per utilizzare qualsiasi percorso valido scelto. Eseguire **(Get-AzureLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** per ottenere un elenco di percorsi validi.

Usare il comando seguente per creare un nuovo gruppo di risorse:

	New-AzureRMResourceGroup -Name "resourcegroupsqlgsps" -Location "West US"

Dopo aver creato un nuovo gruppo di risorse, sullo schermo verranno visualizzate informazioni tra cui **ProvisioningState: Succeeded**.


### Creare un server 

I database SQL vengono creati all'interno dei server di database SQL di Azure. Per creare un nuovo server, eseguire **New AzureRMSqlServer**. Sostituire ServerName con il nome del server. Deve essere univoco per tutti i server SQL di Azure, altrimenti se il nome del server è già in uso verrà visualizzato un messaggio di errore. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. È possibile modificare il comando per usare qualsiasi percorso valido, ma è consigliabile scegliere la stessa posizione usata per il gruppo di risorse creato nel passaggio precedente.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.

Dopo aver creato il server, verranno visualizzati i relativi dettagli.

### Configurare una regola firewall del server per consentire l'accesso al server

Definire una regola firewall per accedere al server. Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il computer.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Dopo aver creato la regola firewall, verranno visualizzati i relativi dettagli.

Per consentire ad altri servizi di Azure di accedere al server, aggiungere una regola firewall e impostare StartIpAddress e EndIpAddress su 0.0.0.0. Si noti che ciò consente al traffico di Azure proveniente da qualsiasi sottoscrizione di Azure di accedere al server.

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](sql-database-firewall-configure.md).


## Creazione di un database SQL

Ora si dispone di un gruppo di risorse, un server e una regola del firewall con una configurazione che consente di accedere al server.

Il comando seguente consente di creare un nuovo database SQL (vuoto) al livello di servizio Standard con un livello di prestazioni S1:


	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Dopo aver creato il database, verranno visualizzati i relativi dettagli.

## Creare uno script di PowerShell per database SQL

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
    
    
    Add-AzureRMAccount
    Select-AzureRMSubscription -SubscriptionId $SubscriptionId
    
    $ResourceGroup = New-AzureRMResourceGroup -Name $ResourceGroupName -Location $Location
    
    $Server = New-AzureRMSqlServer -ResourceGroupName $ResourceGroupName -ServerName $ServerName -Location $Location -ServerVersion "12.0"
    
    $FirewallRule = New-AzureRMSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $ServerName -FirewallRuleName $FirewallRuleName -StartIpAddress $FirewallStartIP -EndIpAddress $FirewallEndIp
    
    $SqlDatabase = New-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -Edition $DatabaseEdition -RequestedServiceObjectiveName $DatabasePerfomanceLevel
    
    $SqlDatabase
    


## Passaggi successivi

- [Connettersi con SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)


## Risorse aggiuntive

- [Database SQL di Azure](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Nov15_HO2-->