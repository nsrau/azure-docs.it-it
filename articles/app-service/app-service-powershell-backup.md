<properties
	pageTitle="Usare PowerShell per eseguire il backup e il ripristino di app del servizio app"
	description="Informazioni su come usare PowerShell per eseguire il backup e il ripristino di un'app nel servizio app di Azure"
	services="app-service"
	documentationCenter=""
	authors="NKing92"
	manager="wpickett"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="nicking"/>
# Usare PowerShell per eseguire il backup e il ripristino di app del servizio app

> [AZURE.SELECTOR]
- [PowerShell](app-service-powershell-backup.md)
- [API REST](../app-service-web/websites-csm-backup.md)

Informazioni su come usare Azure PowerShell per eseguire il backup e il ripristino di [app del servizio app](https://azure.microsoft.com/services/app-service/web/). Per altre informazioni sui backup delle app Web, inclusi i requisiti e le restrizioni, vedere [Eseguire il backup di un'app Web nel servizio app di Azure](../app-service-web/web-sites-backup.md).

## Prerequisiti
Per usare PowerShell per gestire i backup delle app, è necessario quanto segue:

- Un **URL di firma di accesso condiviso** che consente l'accesso in lettura e scrittura a un contenitore di archiviazione di Azure. Per altre informazioni sugli URL di firma di accesso condiviso, vedere [Informazioni sul modello di firma di accesso condiviso](../storage/storage-dotnet-shared-access-signature-part-1.md).
- Una **stringa di connessione del database** se si vuole eseguire il backup di un database insieme all'app Web.

##Installare Azure PowerShell 1.3.2 o versioni successive

Per istruzioni sull'installazione e sull'uso di Azure PowerShell, vedere [Uso di Azure PowerShell con Azure Resource Manager](../powershell-install-configure.md).

## Creare un backup

Usare il cmdlet New-AzureRmWebAppBackup per creare un backup di un'app Web.

		$sasUrl = "<your SAS URL>"
		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl

Verrà creato un backup con un nome generato automaticamente. Se si vuole specificare un nome per il backup, usare il parametro facoltativo BackupName.

		$backup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -StorageAccountUrl $sasUrl -BackupName MyBackup

Se si vuole includere un database come parte del backup, creare prima di tutto un'impostazione di backup del database usando il cmdlet New-AzureRmWebAppDatabaseBackupSetting, quindi fornire tale informazione nel parametro Databases del cmdlet New-AzureRmWebAppBackup. Il parametro Databases accetta una matrice di impostazioni del database, consentendo di eseguire il backup di più database.

		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbBackup = New-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupName MyBackup -StorageAccountUrl $sasUrl -Databases $dbSetting1,$dbSetting2

## Ottenere i backup

Il cmdlet Get-AzureRmWebAppBackupList restituirà una matrice di backup per un'app Web. È necessario specificare il nome dell'app Web e il rispettivo gruppo di risorse.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$backups = Get-AzureRmWebAppBackupList -Name $appName -ResourceGroupName $resourceGroupName

Per ottenere un backup specifico, usare il cmdlet Get-AzureRmWebAppBackup.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102

Per semplificare, è anche possibile inviare tramite pipe un oggetto app Web in uno dei cmdlet di gestione dei backup.

		$app = Get-AzureRmWebApp -Name ContosoApp -ResourceGroupName Default-Web-WestUS
		$backupList = $app | Get-AzureRmWebAppBackupList
		$backup = $app | Get-AzureRmWebAppBackup -BackupId 10102

## Pianificare backup automatici

È possibile pianificare i backup in modo che vengano eseguiti automaticamente a un intervallo specificato. Per configurare una pianificazione di backup, usare il cmdlet Edit-AzureRmWebAppBackupConfiguration. Questo cmdlet accetta alcuni parametri:

- **Name**: nome dell'app Web.
- **ResourceGroupName**: nome del gruppo di risorse che include l'app Web.
- **Slot**: facoltativo. Nome dello slot dell'app Web.
- **StorageAccountUrl**: URL di firma di accesso condiviso del contenitore di archiviazione di Azure usato per archiviare i backup.
- **FrequencyInterval**: valore numerico relativo alla frequenza con cui devono essere eseguiti i backup. Deve essere un intero positivo.
- **FrequencyUnit**: unità di tempo relativa alla frequenza con cui devono essere eseguiti i backup.. Le opzioni disponibili sono Hour e Day.
- **RetentionPeriodInDays**: giorni per cui è necessario conservare i backup automatici prima dell'eliminazione automatica.
- **StartTime**: facoltativo. Ora in cui devono iniziare i backup automatici. I backup inizieranno immediatamente se questo valore è Null. Deve essere un valore DateTime.
- **Databases**: facoltativo. Una matrice di DatabaseBackupSettings per i database da sottoporre a backup.
- **KeepAtLeastOneBackup**: parametro facoltativo scambiato. Specificare questo valore se è necessario conservare sempre un backup nell'account di archiviazione, indipendentemente da quando è stato creato.

Ecco un esempio di uso del cmdlet.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Edit-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName -Slot $slotName `
		  -StorageAccountUrl "<your SAS URL>" -FrequencyInterval 6 -FrequencyUnit Hour -Databases $dbSetting1,$dbSetting2 `
		  -KeepAtLeastOneBackup -StartTime (Get-Date).AddHours(1)

Per ottenere la pianificazione di backup corrente, usare il cmdlet Get-AzureRmWebAppBackupConfiguration. Può essere utile per modificare una pianificazione già configurata.

		$configuration = Get-AzureRmWebAppBackupConfiguration -Name $appName -ResourceGroupName $resourceGroupName

		# Modify the configuration slightly
		$configuration.FrequencyInterval = 2
		$configuration.FrequencyUnit = "Day"

		# Apply the new configuration by piping it into the Edit-AzureRmWebAppBackupConfiguration cmdlet
		$configuration | Edit-AzureRmWebAppBackupConfiguration

## Ripristinare un'app Web da un backup

Per ripristinare un'app Web da un backup, usare il cmdlet Restore-AzureRmWebAppBackup. Il modo più semplice per usare questo cmdlet consiste nell'inviare tramite pipe un oggetto backup recuperato dal cmdlet Get-AzureRmWebAppBackup o dal cmdlet Get-AzureRmWebAppBackupList.

Dopo avere ottenuto un oggetto backup, è possibile inviarlo tramite pipe nel cmdlet Restore-AzureRmWebAppBackup. È necessario specificare il parametro opzionale Overwrite per indicare che si intende sovrascrivere i contenuti dell'app Web con i contenuti del backup. Se il backup contiene database, verranno ripristinati anche tali database.

		$backup | Restore-AzureRmWebAppBackup -Overwrite

Ecco un esempio di uso del cmdlet Restore-AzureRmWebAppBackup con tutti i parametri specificati.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		$slotName = "StagingSlot"
		$blobName = "ContosoBackup.zip"
		$dbSetting1 = New-AzureRmWebAppDatabaseBackupSetting -Name DB1 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		$dbSetting2 = New-AzureRmWebAppDatabaseBackupSetting -Name DB2 -DatabaseType SqlAzure -ConnectionString "<connection_string>"
		Restore-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -Slot $slotName -StorageAccountUrl "<your SAS URL>" -BlobName $blobName -Databases $dbSetting1,$dbSetting2 -Overwrite

## Eliminazione di un backup

Per eliminare un backup, usare il cmdlet Remove-AzureRmWebAppBackup. Il backup verrà rimosso dall'account di archiviazione. È necessario specificare il nome dell'app, il rispettivo gruppo di risorse e l'ID del backup da eliminare.

		$resourceGroupName = "Default-Web-WestUS"
		$appName = "ContosoApp"
		Remove-AzureRmWebAppBackup -ResourceGroupName $resourceGroupName -Name $appName -BackupId 10102

È anche possibile inviare tramite pipe un oggetto backup al cmdlet Remove-AzureRmWebAppBackup per eliminarlo.

		$backup = Get-AzureRmWebAppBackup -Name $appName -ResourceGroupName $resourceGroupName -BackupId 10102
		$backup | Remove-AzureRmWebAppBackup -Overwrite

<!---HONumber=AcomDC_0601_2016-->