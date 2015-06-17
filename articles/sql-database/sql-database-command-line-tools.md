<properties 
	pageTitle="Gestire le risorse del database SQL di Azure con PowerShell" 
	description="Gestione del database SQL di Azure con la riga di comando" 
	services="sql-database" 
	documentationCenter="" 
	authors="TigerMint" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="vinsonyu"/>

# Gestire le risorse del database SQL di Azure con PowerShell


Questo argomento illustra l'uso di uno script di PowerShell per creare un server logico per il database SQL di Azure, un database e una regola del firewall.

## Passaggio 1: Installare Azure SDK

Se non è ancora stato installato, seguire le istruzioni incluse nell'argomento [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare Azure PowerShell nel computer locale. Quindi, aprire un prompt dei comandi di Azure PowerShell.


## Passaggio 2: Configurare gli script di PowerShell
Questo script di PowerShell crea un server, un database e una regola del firewall per il server.


1. Copiare il seguente blocco di cmdlet di PowerShell nell'editor di testo.
		
		
		Add-AzureAccount #Only needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "<Service Admin Login>" -AdministratorLoginPassword "<ServerLoginPassword>" -Location "<Location>" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "<Database1>" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "<FirewallRuleName>" -StartIpAddress "<IP4StartRange>" -EndIpAddress "<IP4EndRange>" -verbose

2. Sostituire tutti gli elementi all'interno di < > con i valori desiderati. Per un elenco dei percorsi di server di database SQL di Azure validi è possibile eseguire i cmdlet seguenti dal prompt dei comandi di Azure PowerShell.

		Switch-AzureMode -Name AzureResourceManager
		$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
		$AzureSQLLocations.Locations

##Passaggio 3: Eseguire lo script di PowerShell

Esaminare i cmdlet di Azure PowerShell raccolti.

Copiare i cmdlet di PowerShell configurati nel passaggio 2 e incollarli nel prompt dei comandi di Azure PowerShell. Per effetto di questa operazione, i cmdlet verranno eseguiti come una serie di comandi di PowerShell e creeranno il server SQL di Azure, il database e la regola del firewall per il server.

Se si prevede di creare nuovamente queste risorse SQL di Azure o risorse simili, è possibile:

- Salvare il codice come file di script di PowerShell (*.ps1).
- Salvare il codice come runbook di Automazione di Azure nella sezione Automazione del portale di gestione di Azure. 

##Esempi

Questo script di PowerShell creerà le risorse negli Stati Uniti occidentali.

		Add-AzureAccount #Needed if you have not been authenicated yet. For Azure Automation, you will need to set up a Service Principal.
		Switch-AzureMode -Name AzureServiceManagement
		$AzureServer = New-AzureSqlDatabaseServer -AdministratorLogin "admin" -AdministratorLoginPassword "P@ssword" -Location "West US" -Version "12.0" -verbose
		New-AzureSqlDatabase -ServerName $AzureServer.ServerName -DatabaseName  "Database1" -Edition "Standard" -verbose
		New-AzureSqlDatabaseServerFirewallRule -ServerName $AzureServer.ServerName -RuleName "MyFirewallRule" -StartIpAddress "192.168.1.1" -EndIpAddress "192.168.1.1" -verbose

##Risorse

Per altre informazioni sui cmdlet di PowerShell per il database SQL di Azure, [fare clic qui](https://msdn.microsoft.com/library/dn546726.aspx).

<!---HONumber=58--> 