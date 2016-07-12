<properties
	pageTitle="Backup automatico per macchine virtuali SQL Server (distribuzione classica) | Microsoft Azure"
	description="Illustra la funzionalità di backup automatico per SQL Server in esecuzione nelle macchine virtuali di Azure che usano Resource Manager. "
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/18/2016"
	ms.author="jroth" />

# Backup automatico per SQL Server in macchine virtuali di Azure (distribuzione classica)

> [AZURE.SELECTOR]
- [Gestione risorse](virtual-machines-windows-sql-automated-backup.md)
- [Classico](virtual-machines-windows-classic-sql-automated-backup.md)

Backup automatico Configura automaticamente il [backup gestito in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi ed esistenti in una macchina virtuale di Azure con SQL Server 2014 Standard o Enterprise in esecuzione. In questo modo è possibile configurare i backup periodici del database che utilizzano l'archiviazione BLOB di Azure durevole. Il backup automatico dipende dall'[estensione dell'agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
Per visualizzare la versione Resource Manager di questo articolo, vedere [Automated Backup for SQL Server in Azure Virtual Machines Resource Manager](virtual-machines-windows-sql-automated-backup.md) (Backup automatico per SQL Server in macchine virtuali di Azure (distribuzione classica)).

## Prerequisiti

Per usare il backup automatico, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versione/edizione di SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise
- SQL Server 2016 Standard
- SQL Server 2016 Enterprise

**Configurazione del database**:

- I database di destinazione devono usare il modello di recupero con registrazione completa

**Azure PowerShell**:

- [Installare i comandi di Azure PowerShell più recenti](../powershell-install-configure.md) se si prevede di configurare il backup automatico con PowerShell.

>[AZURE.NOTE] Il backup automatico si basa sull'estensione dell'agente IaaS di SQL Server. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## Impostazioni

Nella seguente tabella sono descritte le opzioni che possono essere configurate per il backup automatico. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

|Impostazione|Intervallo (impostazione predefinita)|Descrizione|
|---|---|---|
|**Backup automatico**|Enable/Disable (disabilitato)|Abilita o disabilita il backup automatico per una macchina virtuale di Azure in cui viene eseguito SQL Server 2014 Standard o Enterprise.|
|**Periodo di conservazione**|1-30 giorni (30 giorni)|Numero di giorni di conservazione di un backup.|
|**Storage Account**|Account di archiviazione di Azure (account di archiviazione creato per la macchina virtuale specificata)|Account di archiviazione di Azure da usare per archiviare i file del backup automatico nell'archiviazione BLOB. In questa posizione viene creato un contenitore per archiviare tutti i file di backup. La convenzione di denominazione dei file di backup include la data, l'ora e il nome del computer.|
|**Crittografia**|Enable/Disable (disabilitato)|Abilita o disabilita la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup sono contenuti nell'account di archiviazione specificato, nello stesso contenitore automaticbackup con la stessa convenzione di denominazione Se la password viene modificata, viene generato un nuovo certificato con tale password, ma il certificato precedente viene mantenuto per ripristinare i backup precedenti.|
|**Password**|Testo della password (nessuno)|Password per le chiavi di crittografia. Questa impostazione è necessaria solo se la crittografia è abilitata. Per ripristinare un backup crittografato, è necessario disporre della password corretta e del certificato correlato usato al momento dell'esecuzione del backup.|

## Configurazione nel portale

È possibile usare il portale di Azure per configurare il backup automatico quando si crea una nuova macchina virtuale di SQL Server 2014 nel modello di distribuzione classica.

La seguente schermata del portale di Azure mostra queste opzioni in **CONFIGURAZIONE FACOLTATIVA** | **BACKUP AUTOMATIZZATO SQL**.

![Configurazione del backup automatizzato di SQL nel portale di Azure](./media/virtual-machines-windows-classic-sql-automated-backup/IC778483.jpg)

Per le macchine virtuali esistenti di SQL Server 2014, selezionare le impostazioni relative al **backup automatizzato** nella sezione di **configurazione** delle proprietà della macchina virtuale Nella finestra relativa al **backup automatico** è possibile abilitare la funzionalità, impostare il periodo di memorizzazione, selezionare l'account di archiviazione e impostare la crittografia. Queste impostazioni sono illustrate nella seguente schermata.

![Configurazione del backup automatizzato nel portale di Azure](./media/virtual-machines-windows-classic-sql-automated-backup/IC792133.jpg)

>[AZURE.NOTE] Quando si abilita il backup automatico per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione del backup automatico. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure verranno visualizzate le nuove impostazioni.

## Configurazione con PowerShell

Nel seguente esempio di PowerShell il backup automatico è configurato per una macchina virtuale esistente di SQL Server 2014. Il comando **New-AzureVMSqlServerAutoBackupConfig** configura le impostazioni di backup automatizzato per archiviare i backup nell'account di archiviazione di Azure specificato dalla variabile $storageaccount. Questi backup verranno conservati per 10 giorni. Il comando **Set-AzureVMSqlServerExtension** aggiorna con queste impostazioni la macchina virtuale di Azure specificata.

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

Per disabilitare il backup automatizzato, eseguire lo stesso script senza il parametro **-Enable** per **New-AzureVMSqlServerAutoBackupConfig**. Come per l'installazione, la disabilitazione del backup automatico può richiedere alcuni minuti.

>[AZURE.NOTE] La disabilitazione e la disinstallazione dell'agente IaaS di SQL Server non comporta la rimozione delle impostazioni di backup gestito configurate in precedenza. È consigliabile disabilitare il backup automatico prima di disabilitare o disinstallare l'agente IaaS di SQL Server.

## Passaggi successivi

Backup automatico configura backup gestito in Macchine virtuali di Azure. Pertanto è importante [esaminare la documentazione per il backup gestito](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare ulteriori indicazioni sul backup e sul ripristino per SQL Server in macchine virtuali di Azure nell'argomento seguente: [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

<!---HONumber=AcomDC_0629_2016-->