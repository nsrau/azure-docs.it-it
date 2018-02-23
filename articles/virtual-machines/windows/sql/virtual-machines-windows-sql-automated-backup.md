---
title: Backup automatico per macchine virtuali SQL Server 2014 in Azure | Documentazione Microsoft
description: "Viene illustrata la funzionalità di backup automatico per macchine virtuali SQL Server 2014 in esecuzione in Azure. Questo articolo si applica alle macchine virtuali che usano Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/05/2018
ms.author: jroth
ms.openlocfilehash: e7e4aab3a4c4f1ccca6868134ec0b829cb7af2f2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Backup automatico per macchine virtuali SQL Server 2014 (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Backup automatico Configura automaticamente il [backup gestito in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi ed esistenti in una macchina virtuale di Azure con SQL Server 2014 Standard o Enterprise in esecuzione. In questo modo è possibile configurare i backup periodici del database che utilizzano l'archiviazione BLOB di Azure durevole. Il backup automatico dipende dall' [estensione dell'agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>prerequisiti
Per usare il backup automatico, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Versione/edizione di SQL Server**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Il backup automatico funziona con SQL Server 2014. Se si usa SQL Server 2016, è possibile usare il backup automatico versione 2 per eseguire il backup dei database. Per altre informazioni, vedere [Automated Backup v2 for SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md) (Backup automatico versione 2 per macchine virtuali SQL Server 2016 in Azure).

**Configurazione del database**:

- I database di destinazione devono usare il modello di recupero con registrazione completa. Per altre informazioni sull'impatto del modello di recupero con registrazione completa sui backup, vedere [Backup con il modello di recupero con registrazione completa](https://technet.microsoft.com/library/ms190217.aspx).
- I database di destinazione devono trovarsi nell'istanza predefinita di SQL Server. L'estensione SQL Server IaaS non supporta le istanze denominate.

**Modello di distribuzione di Azure**:

- Gestione risorse

**Azure PowerShell**:

- [Installare i comandi di Azure PowerShell più recenti](/powershell/azure/overview) se si prevede di configurare il backup automatico con PowerShell.

> [!NOTE]
> Il backup automatico si basa sull'estensione dell'agente IaaS di SQL Server. Per impostazione predefinita, le attuali immagini della raccolta di macchine virtuali di SQL aggiungono questa estensione. Per altre informazioni, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni

Nella seguente tabella sono descritte le opzioni che possono essere configurate per il backup automatico. I passaggi di configurazione effettivi variano a seconda che venga usato il portale di Azure o i comandi di Windows PowerShell di Azure.

| Impostazione | Intervallo (impostazione predefinita) | DESCRIZIONE |
| --- | --- | --- |
| **Backup automatico** | Enable/Disable (disabilitato) | Abilita o disabilita il backup automatico per una macchina virtuale di Azure in cui viene eseguito SQL Server 2014 Standard o Enterprise. |
| **Periodo di conservazione** | 1-30 giorni (30 giorni) | Numero di giorni di conservazione di un backup. |
| **Storage Account** | Account di archiviazione di Azure | Account di archiviazione di Azure da usare per archiviare i file del backup automatico nell'archiviazione BLOB. In questa posizione viene creato un contenitore per archiviare tutti i file di backup. La convenzione di denominazione dei file di backup include la data, l'ora e il nome del computer. |
| **Crittografia** | Enable/Disable (disabilitato) | Abilita o disabilita la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup sono contenuti nell'account di archiviazione specificato, nello stesso contenitore `automaticbackup` con la stessa convenzione di denominazione. Se la password viene modificata, viene generato un nuovo certificato con tale password, ma il certificato precedente viene mantenuto per ripristinare i backup precedenti. |
| **Password** | Testo della password | Password per le chiavi di crittografia. Questa impostazione è necessaria solo se la crittografia è abilitata. Per ripristinare un backup crittografato, è necessario disporre della password corretta e del certificato correlato usato al momento dell'esecuzione del backup. |

## <a name="configuration-in-the-portal"></a>Configurazione nel Portale

È possibile usare il portale di Azure per configurare il backup automatico durante il provisioning o per le macchine virtuali di SQL Server 2014 esistenti.

### <a name="new-vms"></a>Nuove VM

Usare il portale di Azure per configurare il backup automatico quando si crea una nuova macchina virtuale di SQL Server 2014 nel modello di distribuzione di Resource Manager.

Nel pannello **Impostazioni di SQL Server** selezionare **Backup automatico**. Nella seguente schermata del Portale di Azure viene mostrato il pannello **Backup automatico di SQL** .

![Configurazione del backup automatico di SQL nel Portale di Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Per il contesto, vedere l'argomento completo sul [provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VM esistenti

Per le macchine virtuali SQL Server esistenti, selezionare la macchina virtuale SQL Server. Dopodiché, selezionare la sezione **Configurazione di SQL Server** del pannello **Impostazioni**.

![Backup automatico di SQL per le VM esistenti](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Nel pannello **Configurazione di SQL Server** fare clic sul pulsante **Modifica** nella sezione dei backup automatici.

![Configurare il backup automatico di SQL per le VM esistenti](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Al termine, fare clic sul pulsante **OK** in fondo al pannello **Configurazione di SQL Server** per salvare le modifiche.

Se si intende abilitare il backup automatico per la prima volta, Azure configura l'agente IaaS di SQL Server in background. Durante questo periodo, nel portale di Azure potrebbe non essere visualizzata l'informazione relativa alla configurazione del backup automatico. Attendere alcuni minuti per l'installazione e la configurazione dell'agente. A questo punto, nel portale di Azure verranno visualizzate le nuove impostazioni.

> [!NOTE]
> È inoltre possibile configurare il backup automatico mediante un modello. Per altre informazioni, vedere l'articolo relativo al [modello di avvio rapido di Azure per il backup automatico](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell

È possibile usare PowerShell per configurare Backup automatico. Prima di iniziare, è necessario eseguire queste operazioni:

- [Scaricare e installare la versione di Azure PowerShell più recente](http://aka.ms/webpi-azps).
- Aprire Windows PowerShell e associarlo al proprio account. A tale scopo, seguire la procedura descritta nella sezione per la [configurazione della sottoscrizione](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) dell'argomento sul provisioning.

### <a name="install-the-sql-iaas-extension"></a>Installare l'estensione di SQL IaaS
Se è stato eseguito il provisioning di una macchina virtuale di SQL Server dal portale di Azure, l'estensione di SQL Server IaaS dovrebbe già essere installata. È possibile verificarlo eseguendo il comando **Get-AzureRmVM** ed esaminando la proprietà **Extensions**.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Se l'estensione Agente IaaS di SQL Server è installata, verrà visualizzata come "SqlIaaSAgent" o "SQLIaaSExtension". La proprietà **ProvisioningState** per l'estensione deve inoltre mostrare lo stato "Succeeded".

Nel caso in cui l'estensione non sia installata o non ne sia stato eseguito il provisioning, è possibile installarla con il comando seguente. Oltre al nome della macchina virtuale e al gruppo di risorse, è necessario anche specificare l'area (**$region**) in cui si trova la macchina virtuale.

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Se non è già stata eseguita, l'installazione dell'estensione riavvia il servizio SQL Server.

### <a id="verifysettings"></a> Verificare le impostazioni correnti

Se si è abilitato il backup automatico durante il provisioning, è possibile usare PowerShell per verificare la configurazione corrente. Eseguire il comando **Get-AzureRmVMSqlServerExtension** ed esaminare la proprietà **AutoBackupSettings**:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

L'output dovrebbe essere simile al seguente:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

Se l'output indica che **Enable** è impostato su **False**, è necessario abilitare il backup automatico. I passaggi per farlo sono identici a quelli per abilitare e configurare Backup automatico. Per altre informazioni, vedere la sezione seguente.

> [!NOTE] 
> Se si verificano le impostazioni subito dopo una modifica, è possibile che vengano visualizzati i valori precedenti. Attendere alcuni minuti e verificare nuovamente le impostazioni per assicurarsi che siano state applicate le modifiche.

### <a name="configure-automated-backup"></a>Configurare Backup automatico
È possibile usare PowerShell per abilitare Backup automatico e per modificarne configurazione e comportamento in qualsiasi momento.

In primo luogo, selezionare o creare un account di archiviazione per i file di backup. Lo script seguente consente di selezionare un account di archiviazione o di crearne uno.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Backup automatico non supporta l'archiviazione di backup nell'archiviazione Premium, ma può ottenere i backup da dischi di macchine virtuali che usano l'archiviazione Premium.

Eseguire quindi il comando **New-AzureVMSqlServerAutoBackupConfig** per abilitare e configurare le impostazioni di Backup automatico per archiviare i backup nell'account di archiviazione di Azure. In questo esempio, viene impostata su 10 giorni la conservazione dei backup. Il secondo comando, **Set-AzureRmVMSqlServerExtension** aggiorna la macchina virtuale di Azure specificata con queste impostazioni.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Potrebbero essere necessari diversi minuti per installare e configurare l'agente IaaS di SQL Server.

> [!NOTE]
> Sono presenti altre impostazioni per **New-AzureRmVMSqlServerAutoBackupConfig** che si applicano solo a SQL Server 2016 e Automated Backup v2. SQL Server 2014 non supporta le seguenti impostazioni: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours** e **LogBackupFrequencyInMinutes**. Se si tenta di configurare queste impostazioni in una macchina virtuale di SQL Server 2014, non si verificano errori ma le impostazioni non vengono applicate. Se si desidera usare queste impostazioni in una macchina virtuale di SQL Server 2016, vedere [Backup automatico v2 per macchine virtuali SQL Server 2016 in Azure](virtual-machines-windows-sql-automated-backup-v2.md).

Per abilitare la crittografia, modificare lo script precedente in modo da passare il parametro **EnableEncryption** e una password (stringa sicura) per il parametro **CertificatePassword**. Il seguente script abilita le impostazioni del backup automatico nell'esempio precedente e aggiunge la crittografia.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Per confermare l'applicazione delle impostazioni, [verificare la configurazione di Backup automatico](#verifysettings).

### <a name="disable-automated-backup"></a>Disabilitare Backup automatico

Per disabilitare Backup automatico, eseguire lo stesso script senza il parametro **-Enable** per il comando **New-AzureRmVMSqlServerAutoBackupConfig**. L'assenza del parametro **-Enable** segnala il comando per disabilitare la funzionalità. Come per l'installazione, la disabilitazione del backup automatico può richiedere alcuni minuti.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Script di esempio

Lo script seguente fornisce un set di variabili da personalizzare per abilitare e configurare Backup automatico per la propria macchina virtuale. In questo caso, potrebbe essere necessario personalizzare lo script in base alle esigenze. Ad esempio, potrebbe essere necessario apportare modifiche se si desidera disabilitare il backup dei database di sistema o abilitare la crittografia.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Passaggi successivi

Backup automatico configura backup gestito in Macchine virtuali di Azure. Pertanto è importante [esaminare la documentazione per il backup gestito](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare ulteriori indicazioni sul backup e sul ripristino per SQL Server in macchine virtuali di Azure nell'argomento seguente: [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](virtual-machines-windows-sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server nelle macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

