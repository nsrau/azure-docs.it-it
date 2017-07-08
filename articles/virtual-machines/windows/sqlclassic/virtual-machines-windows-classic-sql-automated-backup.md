---
title: Backup automatico per macchine virtuali SQL Server (distribuzione classica) | Microsoft Docs
description: "Illustra la funzionalità di backup automatico per SQL Server in esecuzione nelle macchine virtuali di Azure che usano Resource Manager. "
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 9e7cbf383eff5344f652024606164c9cf264015c
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Backup automatico per SQL Server in macchine virtuali di Azure (distribuzione classica)
> [!div class="op_single_selector"]
> * [Gestione risorse](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Classico](../classic/sql-automated-backup.md)
> 
> 

Backup automatico Configura automaticamente il [backup gestito in Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) per tutti i database nuovi ed esistenti in una macchina virtuale di Azure con SQL Server 2014 Standard o Enterprise in esecuzione. In questo modo è possibile configurare i backup periodici del database che utilizzano l'archiviazione BLOB di Azure durevole. Il backup automatico dipende dall' [estensione dell'agente IaaS di SQL Server](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per visualizzare la versione di Resource Manager di questo articolo, vedere [Backup automatico per SQL Server nelle macchine virtuali di Azure (Resource manager)](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Prerequisiti
Per usare il backup automatico, tenere in considerazione i seguenti prerequisiti:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versione/edizione di SQL Server**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 non supporta ancora il backup automatizzato.
> 
> 

**Configurazione del database**:

* I database di destinazione devono usare il modello di recupero con registrazione completa.

**Azure PowerShell**:

* [Installare i comandi di Azure PowerShell più recenti](/powershell/azure/overview).

**Estensione di SQL Server IaaS**:

* [Installare l'estensione di SQL Server IaaS](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Impostazioni
Nella seguente tabella sono descritte le opzioni che possono essere configurate per il backup automatico. Nella macchine virtuali classiche, per configurare queste impostazioni è necessario usare PowerShell.

| Impostazione | Intervallo (impostazione predefinita) | Descrizione |
| --- | --- | --- |
| **Backup automatico** |Enable/Disable (disabilitato) |Abilita o disabilita il backup automatico per una macchina virtuale di Azure in cui viene eseguito SQL Server 2014 Standard o Enterprise. |
| **Periodo di conservazione** |1-30 giorni (30 giorni) |Numero di giorni di conservazione di un backup. |
| **Storage Account** |Account di archiviazione di Azure (account di archiviazione creato per la macchina virtuale specificata) |Account di archiviazione di Azure da usare per archiviare i file del backup automatico nell'archiviazione BLOB. In questa posizione viene creato un contenitore per archiviare tutti i file di backup. La convenzione di denominazione dei file di backup include la data, l'ora e il nome del computer. |
| **Crittografia** |Enable/Disable (disabilitato) |Abilita o disabilita la crittografia. Quando è abilitata la crittografia, i certificati usati per ripristinare il backup sono contenuti nell'account di archiviazione specificato, nello stesso contenitore automaticbackup con la stessa convenzione di denominazione Se la password viene modificata, viene generato un nuovo certificato con tale password, ma il certificato precedente viene mantenuto per ripristinare i backup precedenti. |
| **Password** |Testo della password (nessuno) |Password per le chiavi di crittografia. Questa impostazione è necessaria solo se la crittografia è abilitata. Per ripristinare un backup crittografato, è necessario disporre della password corretta e del certificato correlato usato al momento dell'esecuzione del backup. | **Backup system databases** (Backup dei database di sistema) | Enable/Disable (disabilitato) | Eseguire backup completi di database master, modello e MSDB |
| **Configure backup schedule** (Configura la pianificazione dei backup) | Manual/Automated (Automated) (Manuale/Automatizzato - Automatizzato) | Selezionare **Automated** (Automatizzato) per eseguire automaticamente backup completi e di log basati sull'aumento delle dimensioni del log. Selezionare **Manual** (Manuale) per specificare la pianificazione per backup completi e di log. |

## <a name="configuration-with-powershell"></a>Configurazione con PowerShell
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

> [!NOTE]
> La disabilitazione e la disinstallazione dell'agente IaaS di SQL Server non comporta la rimozione delle impostazioni di backup gestito configurate in precedenza. È consigliabile disabilitare il backup automatico prima di disabilitare o disinstallare l'agente IaaS di SQL Server.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Backup automatico configura backup gestito in Macchine virtuali di Azure. Pertanto è importante [esaminare la documentazione per il backup gestito](https://msdn.microsoft.com/library/dn449496.aspx) per comprendere il comportamento e le implicazioni.

È possibile trovare ulteriori indicazioni sul backup e sul ripristino per SQL Server in macchine virtuali di Azure nell'argomento seguente: [Backup e ripristino per SQL Server in Macchine virtuali di Azure](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Per informazioni sulle altre attività di automazione disponibili, vedere [Estensione Agente IaaS di SQL Server](../classic/sql-server-agent-extension.md).

Per altre informazioni sull'esecuzione di SQL Server nelle VM di Azure, vedere [Panoramica di SQL Server nelle macchine virtuali di Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).


