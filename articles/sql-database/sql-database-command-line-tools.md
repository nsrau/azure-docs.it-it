<properties 
	pageTitle="Gestire le risorse del database SQL di Azure con PowerShell" 
	description="Gestione del database SQL di Azure con PowerShell" 
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
	ms.date="07/28/2015" 
	ms.author="vinsonyu"/>

# Gestire le risorse del database SQL di Azure con PowerShell


In questo argomento sono disponibili comandi PowerShell per eseguire molte attività del database SQL di Azure utilizzando i cmdlet di Gestione risorse di Azure.


## Prerequisiti

Per eseguire i cmdlet di PowerShell, è necessario disporre di Azure PowerShell installato e in esecuzione e a seconda della versione potrebbe essere necessario passarlo alla modalità di gestione delle risorse per accedere ai cmdlet di PowerShell di Gestione risorse di Azure.

Per scaricare e installare i moduli Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Per informazioni dettagliate, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

I cmdlet per la creazione e la gestione dei database SQL di Azure si trovano nel modulo di Gestione risorse di Azure. Quando si avvia Azure PowerShell, i cmdlet nel modulo di Azure vengono importati per impostazione predefinita. Per passare al modulo AzureResourceManager, usare il cmdlet **Switch-AzureMode**.

	Switch-AzureMode -Name AzureResourceManager

Se viene visualizzato un avviso che indica 'Il cmdlet Switch-AzureMode è obsoleto e verrà rimosso in una versione futura'. È possibile ignorarlo e passare alla sezione successiva.

Per informazioni dettagliate, vedere [Utilizzo di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).



## Configurazione delle credenziali

Per eseguire i cmdlet PowerShell nella sottoscrizione di Azure, è necessario innanzitutto stabilire l'accesso al proprio account Azure. Eseguire le operazioni seguenti e verrà visualizzata una schermata di accesso per immettere le credenziali. Utilizzare lo stesso indirizzo email e password utilizzati per accedere al portale di Azure.

	Add-AzureAccount

Dopo aver effettuato l’accesso, sullo schermo dovrebbero essere visualizzate informazioni tra cui l’ID utilizzato per l’accesso con le sottoscrizioni Azure per le quali si dispone dell’accesso


## Selezionare la sottoscrizione ad Azure

Per selezionare la sottoscrizione con cui lavorare, è necessario l’ID sottoscrizione (\*\*-SubscriptionId\*\*) o il nome della sottoscrizione (\*\*-SubscriptionName\*\*), che può essere copiato dal passaggio precedente o, se si dispone di più sottoscrizioni, è possibile eseguire il cmdlet **Get-AzureSubscription** e copiare le informazioni di sottoscrizione desiderate dal set di risultati.

Eseguire il cmdlet seguente con le informazioni della sottoscrizione per impostare la sottoscrizione corrente:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

I comandi seguenti verranno eseguiti con la sottoscrizione appena selezionata.

## Creare un gruppo di risorse

Creare il gruppo di risorse che conterrà il server. È possibile modificare il comando successivo per utilizzare qualunque percorso valido.

Per un elenco delle posizioni dei server dei database SQL di Azure validi, eseguire i cmdlet seguenti:

	$AzureSQLLocations = Get-AzureLocation | Where-Object Name -Like "*SQL/Servers"
	$AzureSQLLocations.Locations

Se si dispone già di un gruppo di risorse, è possibile proseguire per creare un server oppure è possibile modificare ed eseguire il comando seguente per creare un nuovo gruppo di risorse:

	New-AzureResourceGroup -Name "resourcegroupJapanWest" -Location "Japan West"

## Creare un server 

Per creare un nuovo uso server V12, utilizzare il comando [New-AzureSqlServer](https://msdn.microsoft.com/library/mt163526.aspx). Sostituire server12 con il nome del server. Deve essere univoco per i server SQL di Azure, per cui è possibile che venga visualizzato un messaggio di errore se il nome del server è già in uso. Vale inoltre la pena notare che il completamento di questo comando potrebbe richiedere alcuni minuti. Una volta creato il server, verranno visualizzati il prompt PowerShell e i dettagli del server. È possibile modificare il comando per utilizzare qualunque percorso valido.

	New-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -Location "Japan West" -ServerVersion "12.0"

Quando si esegue questo comando, viene visualizzata una finestra in cui vengono richiesti **Nome utente** e **Password**. Non si tratta delle credenziali di Azure. Immettere il nome utente e password che saranno le credenziali di amministratore che si desidera creare per il nuovo server.

## Creare una regola del firewall del server

Per creare una regola del firewall per accedere al server, utilizzare il comando [New-AzureSqlServerFirewallRule](https://msdn.microsoft.com/library/mt125953.aspx). Eseguire il comando seguente sostituendo gli indirizzi IP di inizio e fine con i valori validi per il client.

Se il server deve consentire l'accesso ad altri servizi Azure, aggiungere lo switch **- AllowAllAzureIPs**, che aggiungerà una regola firewall speciale e consentirà l’accesso al server per tutto il traffico Azure.

	New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -FirewallRuleName "clientFirewallRule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Per ulteriori informazioni, vedere [Firewall di database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx).

## Creazione di un database SQL

Per creare un database, utilizzare il comando [New-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125915.aspx). È necessario un server per creare un database. Nell'esempio seguente viene creato un database SQL denominato TestDB12. Il database viene creato come database Standard S1.

	New-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S1"


## Modifica del livello di prestazioni di un database SQL

È possibile scalare il database verso l'alto o verso il basso con il comando [Set-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125814.aspx). L'esempio seguente scala verso l’alto un database SQL denominato TestDB12 dal livello di prestazioni corrente a un livello Standard S3.

	Set-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12" -Edition Standard -RequestedServiceObjectiveName "S3"


## Eliminazione di un database SQL

È possibile eliminare un database SQL con il comando [Remove-AzureSqlDatabase](https://msdn.microsoft.com/library/mt125977.aspx). Nell'esempio seguente viene eliminato un database SQL denominato TestDB12.

	Remove-AzureSqlDatabase -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12" -DatabaseName "TestDB12"

## Eliminazione di un server

È inoltre possibile eliminare un server con il comando [Remove-AzureSqlServer](https://msdn.microsoft.com/library/mt125891.aspx). Nell'esempio seguente viene eliminato un server denominato server12.

	Remove-AzureSqlServer -ResourceGroupName "resourcegroupJapanWest" -ServerName "server12"



Se si prevede di creare nuovamente queste risorse SQL di Azure o risorse simili, è possibile:

- Salvare il codice come file di script di PowerShell (\*.ps1).
- Salvare il codice come runbook di Automazione di Azure nella sezione Automazione del portale di gestione di Azure. 

## Passaggi successivi

Combinare i comandi e automatizzare. Ad esempio, sostituire tutto ciò che si trova tra le virgolette, inclusi i < and > caratteri, con i valori per creare un server, la regola del firewall e il database:


    New-AzureResourceGroup -Name "<resourceGroupName>" -Location "<Location>"
    New-AzureSqlServer -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -Location "<Location>" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -FirewallRuleName "<firewallRuleName>" -StartIpAddress "<192.168.0.198>" -EndIpAddress "<192.168.0.199>"
    New-AzureSqlDatabase -ResourceGroupName "<resourceGroupName>" -ServerName "<serverName>" -DatabaseName "<databaseName>" -Edition <Standard> -RequestedServiceObjectiveName "<S1>"

## Informazioni correlate

- [Cmdlet di Gestione risorse del database SQL di Azure](https://msdn.microsoft.com/library/mt163521.aspx)
- [Cmdlet di Gestione servizi del database SQL di Azure](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=July15_HO5-->