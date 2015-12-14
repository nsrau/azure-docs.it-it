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
	ms.date="12/01/2015" 
	ms.author="sstein"/>

# Gestire il database SQL di Azure con PowerShell


> [AZURE.SELECTOR]
- [Azure Portal](sql-database-manage-portal.md)
- [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

In questo argomento sono disponibili comandi PowerShell per eseguire molte attività del database SQL di Azure.


> [AZURE.IMPORTANT]Iniziando con la versione di anteprima di Azure PowerShell 1.0, il cmdlet Switch-AzureMode non è più disponibile e i cmdlet che sono stati nel modulo Azure ResourceManager sono stati rinominati. Gli esempi in questo articolo utilizzano nuove convenzioni di denominazione dell’Anteprima di PowerShell 1.0. Per informazioni dettagliate, vedere [Deprecazione di Switch-AzureMode, in Azure PowerShell](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).


Per eseguire i cmdlet di PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a causa della rimozione di Switch-AzureMode, scaricare e installare la versione più recente di Azure PowerShell eseguendo l’[installazione guidata della piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).



## Configurazione delle credenziali

Per eseguire i cmdlet PowerShell nella sottoscrizione di Azure, è necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure classico.

	Add-AzureAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso


## Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione con cui lavorare, è necessario l’ID sottoscrizione (**-SubscriptionId**) o il nome della sottoscrizione (**-SubscriptionName**). che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati.

Eseguire il cmdlet seguente con le informazioni della sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

I comandi seguenti verranno eseguiti con la sottoscrizione appena selezionata.

## Creare un gruppo di risorse

Creare il gruppo di risorse che conterrà il server. È possibile modificare il comando successivo per utilizzare qualunque percorso valido.

Per un elenco delle posizioni dei server dei database SQL di Azure validi, eseguire i cmdlet seguenti:

	$AzureSQLLocations = Get-AzureRMLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

Se si dispone già di un gruppo di risorse, è possibile proseguire per creare un server oppure è possibile modificare ed eseguire il comando seguente per creare un nuovo gruppo di risorse:

	New-AzureRMResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Creare un server 

Per creare un nuovo uso server V12, utilizzare il cmdlet [New-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603715.aspx). Sostituire server12 con il nome del server. Deve essere univoco per i server SQL di Azure, altrimenti se il nome del server è già in uso verrà visualizzato un messaggio di errore. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. Una volta creato il server, verranno visualizzati il prompt PowerShell e i dettagli del server. È possibile modificare il comando per utilizzare qualunque percorso valido.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.

## Creare una regola del firewall del server

Per creare una regola del firewall per accedere al server, utilizzare il comando [New-AzureRMSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860.aspx). Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il client.

Se il server deve consentire l'accesso ad altri servizi Azure, aggiungere lo switch **- AllowAllAzureIPs**, che aggiungerà una regola firewall speciale e consentirà l’accesso al server per tutto il traffico Azure.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Creazione di un database SQL

Per creare un database, utilizzare il comando [New-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx). È necessario un server per creare un database. Nell'esempio seguente viene creato un database SQL denominato TestDB12. Il database viene creato come database Standard S1.

	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Modifica del livello di prestazioni di un database SQL

È possibile scalare il database verso l'alto o verso il basso con il comando [Set-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx). L'esempio seguente scala verso l’alto un database SQL denominato TestDB12 dal livello di prestazioni corrente a un livello Standard S3.

	Set-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Eliminazione di un database SQL

È possibile eliminare un database SQL con il comando [Remove-AzureRMSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368.aspx). Nell'esempio seguente viene eliminato un database SQL denominato TestDB12.

	Remove-AzureRMSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Eliminazione di un server

È inoltre possibile eliminare un server con il comando [Remove-AzureRMSqlServer](https://msdn.microsoft.com/library/azure/mt603488.aspx). Nell'esempio seguente viene eliminato un server denominato server12.

	Remove-AzureRMSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Se si prevede di creare nuovamente queste risorse SQL di Azure o risorse simili, è possibile:

- Salvare il codice come file di script di PowerShell (*.ps1).
- Salvare il codice come runbook di Automazione di Azure nella sezione Automazione del portale di Azure classico. 

## Passaggi successivi

Combinare i comandi e automatizzare. Ad esempio, sostituire tutto ciò che si trova tra le virgolette, inclusi i < and > caratteri, con i valori per creare un server, la regola del firewall e il database:


    New-AzureRMResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureRMSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureRMSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureRMSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Informazioni correlate

- [Cmdlet del database SQL di Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx).

<!---HONumber=AcomDC_1203_2015-->