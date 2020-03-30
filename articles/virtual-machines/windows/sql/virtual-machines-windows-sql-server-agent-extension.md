---
title: Automatizzare le attività di gestione con l'estensione dell'agente IaaSAutomate management tasks with the IaaS Agent Extension
description: In questo articolo viene descritto come gestire l'estensione dell'agente di SQL Server IaaS, che automatizza le attività di amministrazione di SQL Server specifiche. Questi includono il backup automatico, l'applicazione automatica di patch e l'integrazione dell'insieme di chiavi di Azure.These include automated backup, automated patching, and Azure Key Vault integration.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 3d16c1950cbae0bcc7dd858e5520eb8bfc6e496d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77030779"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizzare le attività di gestione nelle macchine virtuali di Azure usando l'estensione dell'agente IaaS di SQL ServerAutomate management tasks on Azure virtual machines by using the SQL Server IaaS Agent Extension
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione SQL Server IaaS Agent (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. In questo articolo viene fornita una panoramica dei servizi supportati dall'estensione. In questo articolo vengono inoltre fornite istruzioni per l'installazione, lo stato e la rimozione dell'estensione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [Estensione dell'agente DiSQL Server IaaS per macchine virtuali di SQL Server (classica)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
| --- | --- |
| **Backup automatico di SQL Server** |Automatizza la pianificazione dei backup per tutti i database per l'istanza predefinita o un'istanza denominata di SQL Server [installata correttamente](virtual-machines-windows-sql-server-iaas-faq.md#administration) nella macchina virtuale. Per altre informazioni, vedere [Backup automatico per SQL Server in Macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-sql-automated-backup.md). |
| **Applicazione automatica di patch a SQL Server** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per altre informazioni, vedere Applicazione automatica di patch per SQL Server in macchine virtuali di [Azure (Gestione risorse)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione dell'insieme di credenziali delle chiavi di Azure** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme](virtual-machines-windows-ps-sql-keyvault.md)di credenziali delle chiavi di Azure per SQL Server in Macchine virtuali di Azure (Gestione risorse) . |

Dopo aver installato e in esecuzione l'estensione dell'agente Iaas di SQL Server, le funzionalità di amministrazione sono disponibili:

* Nel pannello SQL Server della macchina virtuale nel portale di Azure e tramite Azure PowerShell per immagini di SQL Server in Azure Marketplace.
* Tramite Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>Prerequisiti
Ecco i requisiti per usare l'estensione dell'agente IaaS di SQL Server nella macchina virtuale:Here are the requirements to use the SQL Server IaaS Agent Extension on your VM:

**Sistema operativo**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versione di SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell:**

* [Scaricare e configurare i comandi di Azure PowerShell più recentiDownload and configure the latest Azure PowerShell commands](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installazione
L'estensione IaaS di SQL Server viene installata quando si registra la macchina virtuale di SQL Server con il provider di [risorse DELLA macchina virtuale SQL.](virtual-machines-windows-sql-register-with-resource-provider.md) Se necessario, è possibile installare manualmente l'agente IaaS di SQL Server usando il comando PowerShell seguente:If necessary, you can install the SQL Server IaaS agent manually using the below PowerShell command: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> L'installazione dell'estensione riavvia il servizio SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installare in una macchina virtuale con una singola istanza denominata di SQL ServerInstall on a VM with a single named SQL Server instance
L'estensione IaaS di SQL Server funzionerà con un'istanza denominata in SQL Server se l'istanza predefinita viene disinstallata e l'estensione IaaS viene reinstallata.

Per utilizzare un'istanza denominata di SQL Server, attenersi alla seguente procedura:
   1. Distribuire una macchina virtuale di SQL Server da Azure Marketplace.Deploy a SQL Server VM from Azure Marketplace. 
   1. Disinstallare l'estensione IaaS dal portale di [Azure.](https://portal.azure.com)
   1. Disinstallare SQL Server completamente all'interno della macchina virtuale di SQL Server.Uninstall SQL Server completely within the SQL Server VM.
   1. Installare SQL Server con un'istanza denominata all'interno della macchina virtuale di SQL Server.Install SQL Server with a named instance within the SQL Server VM. 
   1. Installare l'estensione IaaS dal portale di Azure.Install the IaaS extension from the Azure portal.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Ottenere lo stato dell'estensione IaaS di SQL ServerGet the status of the SQL Server IaaS extension
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nella finestra della macchina virtuale, quindi selezionare **Estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Stato dell'estensione dell'agente IaaS di SQL Server nel portale di AzureStatus of the SQL Server IaaS Agent Extension in the Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet Di Azure PowerShell **Get-AzVMSqlServerExtension:You** can also use the Get-AzVMSqlServerExtension Azure PowerShell cmdlet:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Il comando precedente conferma che l'agente è installato e fornisce informazioni generali sullo stato. È possibile ottenere informazioni specifiche sullo stato del backup automatico e dell'applicazione di patch utilizzando i seguenti comandi:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Rimozione
Nel portale di Azure è possibile disinstallare l'estensione selezionando i ellissi nella finestra **Estensioni** delle proprietà della macchina virtuale. Quindi selezionare **Elimina**.

![Disinstallazione dell'estensione dell'agente IaaS di SQL Server nel portale di AzureUninstalling the SQL Server IaaS Agent Extension in Azure portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È inoltre possibile utilizzare il cmdlet **PowerShell Remove-AzVMSqlServerExtension:**

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passaggi successivi
Iniziare a utilizzare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli articoli a cui si fa riferimento nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per ulteriori informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Che cos'è SQL Server in Macchine virtuali](virtual-machines-windows-sql-server-iaas-overview.md)di Azure? .
