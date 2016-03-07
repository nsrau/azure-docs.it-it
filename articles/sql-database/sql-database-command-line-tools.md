<properties 
	pageTitle="Gestire il database SQL di Azure con PowerShell" 
	description="Gestione del database SQL di Azure con PowerShell" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="monicar"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/23/2016" 
	ms.author="sstein"/>

# Gestire il database SQL di Azure con PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

In questo argomento sono disponibili comandi PowerShell per eseguire molte attività del database SQL di Azure.


Per eseguire i cmdlet di PowerShell, è necessario che Azure PowerShell sia installato e in esecuzione. Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).



## Configurazione delle credenziali

Per eseguire i cmdlet PowerShell nella sottoscrizione di Azure, è necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure classico.

	Login-AzureRmAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso


## Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione con cui lavorare, è necessario l’ID sottoscrizione (**-SubscriptionId**) o il nome della sottoscrizione (**-SubscriptionName**). che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati.

Eseguire il cmdlet seguente con le informazioni della sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

I comandi seguenti verranno eseguiti con la sottoscrizione appena selezionata.

## Creare un gruppo di risorse

Creare il gruppo di risorse che conterrà il server. È possibile modificare il comando successivo per utilizzare qualunque percorso valido.

Per un elenco delle posizioni dei server dei database SQL di Azure validi, eseguire i cmdlet seguenti:

	$AzureSQLLocations = (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Sql'}).Locations

Se si dispone già di un gruppo di risorse, è possibile proseguire per creare un server oppure è possibile modificare ed eseguire il comando seguente per creare un nuovo gruppo di risorse:

	New-AzureRmResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Creare un server 

Per creare un nuovo server V12, usare il cmdlet [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Sostituire server12 con il nome del server. Deve essere univoco per i server SQL di Azure, altrimenti se il nome del server è già in uso verrà visualizzato un messaggio di errore. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. Una volta creato il server, verranno visualizzati il prompt PowerShell e i dettagli del server. È possibile modificare il comando per utilizzare qualunque percorso valido.

	New-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.

## Creare una regola del firewall del server

Per creare una regola del firewall per accedere al server, usare il comando [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il client.

Se il server deve consentire l'accesso ad altri servizi Azure, aggiungere lo switch **- AllowAllAzureIPs**, che aggiungerà una regola firewall speciale e consentirà l’accesso al server per tutto il traffico Azure.

	New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Creazione di un database SQL

Per creare un database, usare il comando [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). È necessario un server per creare un database. Nell'esempio seguente viene creato un database SQL denominato TestDB12. Il database viene creato come database Standard S1.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Modifica del livello di prestazioni di un database SQL

È possibile scalare il database verso l'alto o verso il basso con il comando [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). L'esempio seguente scala verso l’alto un database SQL denominato TestDB12 dal livello di prestazioni corrente a un livello Standard S3.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Eliminazione di un database SQL

È possibile eliminare un database SQL con il comando [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). Nell'esempio seguente viene eliminato un database SQL denominato TestDB12.

	Remove-AzureRmSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Eliminazione di un server

È inoltre possibile eliminare un server con il comando [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). Nell'esempio seguente viene eliminato un server denominato server12.

	Remove-AzureRmSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Se si prevede di creare nuovamente queste risorse SQL di Azure o risorse simili, è possibile:

- Salvare il codice come file di script di PowerShell (*.ps1).
- Salvare il codice come runbook di Automazione di Azure nella sezione Automazione del portale di Azure classico. 

## Passaggi successivi

Combinare i comandi e automatizzare. Ad esempio, sostituire tutto ciò che si trova tra le virgolette, inclusi i caratteri < and >, con i propri valori per creare un server, una regola del firewall e un database:


    New-AzureRmResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRmSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRmSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Informazioni correlate

- [Cmdlet del database SQL di Azure.](https://msdn.microsoft.com/library/azure/mt574084.aspx)

<!---HONumber=AcomDC_0224_2016-->