<properties
	pageTitle="Backup automatico per SQL Server in Macchine virtuali | Microsoft Azure"
	description="Illustra la funzionalità di backup automatico per SQL Server in esecuzione in macchine virtuali di Azure usando il modello di distribuzione Gestione risorse."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-resource-manager" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/12/2015"
	ms.author="jroth" />

# Backup automatico per SQL Server in Macchine virtuali di Azure

Backup automatico Configura automaticamente il [backup gestito in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi ed esistenti in una macchina virtuale di Azure con SQL Server 2014 Standard o Enterprise in esecuzione. In questo modo è possibile configurare i backup periodici del database che utilizzano l'archiviazione BLOB di Azure durevole.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.

## Impostazioni del backup automatico

Nella seguente tabella sono descritte le opzioni che possono essere configurate per il backup automatico. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di anteprima di Azure o i comandi Windows PowerShell di Azure.

|Impostazione|Intervallo (impostazione predefinita)|Descrizione|
|---|---|---|
|**Backup automatico**|Enable/Disable (disabilitato)|Abilita o disabilita il backup automatico per una macchina virtuale di Azure in cui viene eseguito SQL Server 2014 Standard o Enterprise.|
|**Periodo di conservazione**|1-30 giorni (30 giorni)|Numero di giorni di conservazione di un backup.|
|**Storage Account**|Account di archiviazione di Azure (account di archiviazione creato per la macchina virtuale specificata)|Account di archiviazione di Azure da usare per archiviare i file del backup automatico nell'archiviazione BLOB. In questa posizione viene creato un contenitore per archiviare tutti i file di backup. La convenzione di denominazione dei file di backup include la data, l'ora e il nome del computer.|
|**Crittografia**|Enable/Disable (disabilitato)|Abilita o disabilita la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup sono contenuti nell'account di archiviazione specificato, nello stesso contenitore automaticbackup con la stessa convenzione di denominazione Se la password viene modificata, viene generato un nuovo certificato con tale password, ma il certificato precedente viene mantenuto per ripristinare i backup precedenti.|
|**Password**|Testo della password (nessuno)|Password per le chiavi di crittografia. Questa impostazione è necessaria solo se la crittografia è abilitata. Per ripristinare un backup crittografato, è necessario disporre della password corretta e del certificato correlato usato al momento dell'esecuzione del backup.|

## Configurare il backup automatico nel portale di anteprima di Azure

È possibile usare il portale di anteprima di Azure per configurare il backup automatico quando si crea una nuova macchina virtuale di SQL Server 2014.

>[AZURE.NOTE]Il backup automatico si basa sull'agente IaaS di SQL Server. Per installare e configurare l'agente, è necessario che nella macchina virtuale di destinazione sia in esecuzione l'agente di macchine virtuali di Azure. Per le immagini della raccolta di macchine virtuali più recenti questa opzione è abilitata per impostazione predefinita, ma è possibile che l'agente di macchine virtuali di Azure non sia presente nelle macchine virtuali esistenti. Se si usa la propria immagine di macchina virtuale, sarà inoltre necessario installare l'agente IaaS di SQL Server. Per altre informazioni, vedere la pagina relativa all'[agente e alle estensioni di macchine virtuali](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

La schermata seguente del portale di anteprima di Azure mostra queste opzioni in **CONFIGURAZIONE FACOLTATIVA** | **BACKUP AUTOMATICO DI SQL**.

![Configurazione del backup automatico di SQL nel portale di anteprima di Azure](./media/virtual-machines-sql-server-automated-backup/IC778483.jpg)

Per le macchine virtuali esistenti di SQL Server 2014, selezionare le impostazioni relative al **backup automatico** nella sezione di **configurazione** delle proprietà della macchina virtuale. Nella finestra relativa al **backup automatico** è possibile abilitare la funzionalità, impostare il periodo di memorizzazione, selezionare l'account di archiviazione e impostare la crittografia. Queste impostazioni sono illustrate nella seguente schermata.

![Configurazione del backup automatico nel portale di anteprima di Azure](./media/virtual-machines-sql-server-automated-backup/IC792133.jpg)

>[AZURE.NOTE]Quando si abilita il backup automatico per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo intervallo di tempo, nel portale di anteprima di Azure non viene visualizzata l'informazione relativa alla configurazione del backup automatico. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di anteprima di Azure verranno visualizzate le nuove impostazioni.

## Configurare il backup automatico con PowerShell

Nel seguente esempio di PowerShell il backup automatico è configurato per una macchina virtuale esistente di SQL Server 2014. Il comando **New-AzureVMSqlServerAutoBackupConfig** configura le impostazioni di backup automatico per archiviare i backup nell'account di archiviazione di Azure specificato dalla variabile $storageaccount. Questi backup verranno conservati per 10 giorni. Il comando **Set-AzureVMSqlServerExtension** aggiorna con queste impostazioni la macchina virtuale di Azure specificata.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

Per abilitare la crittografia, modificare lo script precedente in modo da passare il parametro EnableEncryption e una password (stringa sicura) per il parametro CertificatePassword. Il seguente script abilita le impostazioni del backup automatico nell'esempio precedente e aggiunge la crittografia.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Per disabilitare il backup automatico, eseguire lo stesso script senza il parametro **-Enable** in **New-AzureVMSqlServerAutoBackupConfig**. Come per l'installazione, la disabilitazione del backup automatico può richiedere alcuni minuti.

## Disabilitazione e disinstallazione dell'agente IaaS di SQL Server

Se si desidera disabilitare l'agente IaaS di SQL Server per il backup e l'applicazione di patch in modalità automatica, usare il seguente comando:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -Disable | Update-AzureVM

Per disinstallare l'agente IaaS di SQL Server, usare la seguente sintassi:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension –Uninstall | Update-AzureVM

È anche possibile disinstallare l'estensione usando il comando **Remove-AzureVMSqlServerExtension**:

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Remove-AzureVMSqlServerExtension | Update-AzureVM

>[AZURE.NOTE]La disabilitazione e la disinstallazione dell'agente IaaS di SQL Server non comporta la rimozione delle impostazioni di backup gestito configurate in precedenza. È consigliabile disabilitare il backup automatico prima di disabilitare o disinstallare l'agente IaaS di SQL Server.

## Compatibilità

I seguenti prodotti sono compatibili con le funzionalità dell'agente IaaS di SQL Server per il backup automatico.

- Windows Server 2012

- Windows Server 2012 R2

- SQL Server 2014 Standard

- SQL Server 2014 Enterprise

## Passaggi successivi

Backup automatico configura backup gestito in Macchine virtuali di Azure. Pertanto, è importante [esaminare la documentazione per il backup gestito](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare ulteriori indicazioni sul backup e sul ripristino per SQL Server in macchine virtuali di Azure nell'argomento seguente: [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-backup-and-restore.md).

Una funzionalità correlata per le macchine virtuali di SQL Server in Azure è [Applicazione automatica di patch per SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-automated-patching.md).

Esaminare altre [risorse per l'esecuzione di SQL Server in Macchine virtuali di Azure](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1125_2015-->