---
title: "Automatizzare le attività di gestione in macchine virtuali SQL (Resource Manager) | Microsoft Docs"
description: "In questo articolo viene descritto come gestire l'estensione di agente SQL Server, che consente di automatizzare attività di amministrazione di SQL Server specifiche. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 414c985d21112d658b6e22473f67ed1c3afd00ef
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizzare le attività di gestione in macchine virtuali SQL con l'estensione SQL Server Agent (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../classic/sql-server-agent-extension.md)
> 
> 

L'Estensione Agente IaaS di SQL Server (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. In questo articolo viene fornita una panoramica dei servizi supportati da estensione, nonché istruzioni per l'installazione, stato e rimozione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [Estensione Agente IaaS di SQL Server (distribuzione classica)](../classic/sql-server-agent-extension.md).

## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | DESCRIZIONE |
| --- | --- |
| **Backup automatico di SQL** |Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella VM. Per altre informazioni, vedere [Backup automatico per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Applicazione automatica delle patch di SQL** |Consente di configurare una finestra di manutenzione durante la quale eseguire gli aggiornamenti della VM, evitandone l'esecuzione durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in Macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Dopo aver installato e in esecuzione, l'estensione di SQL Server IaaS Agent disponibili queste funzionalità di amministrazione nel Pannello di SQL Server della macchina virtuale nel portale di Azure e Azure PowerShell per le immagini di SQL Server marketplace e Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>Prerequisiti
Requisiti per l'uso dell'Estensione Agente IaaS di SQL Server nella VM:

**Sistema operativo**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Versioni di SQL Server**:

* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

**Azure PowerShell**:

* [Scaricare e configurare i comandi di Azure PowerShell più recenti](/powershell/azure/overview)

## <a name="installation"></a>Installazione
L'Estensione Agente IaaS di SQL Server viene installata automaticamente quando si esegue il provisioning di una delle immagini della galleria di macchine virtuali SQL Server. Se è necessario reinstallare manualmente l'estensione in una di queste macchine virtuali di SQL Server, usare il comando PowerShell seguente:

```powershell
Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2" -Location "East US 2"
```

È possibile anche installare l'estensione SQL Server IaaS Agent in una macchina virtuale Windows Server con il solo sistema operativo. Questa soluzione è supportata solo se SQL Server è stato installato manualmente sul computer. Installare quindi l'estensione manualmente usando lo stesso cmdlet di PowerShell **Set-AzureVMSqlServerExtension**.

> [!NOTE]
> Se si installa manualmente l'estensione Agente IaaS di SQL Server in una macchina virtuale Windows Server con il solo sistema operativo, non è possibile gestire le impostazioni di configurazione di SQL Server tramite il portale di Azure. In questo scenario è necessario eseguire tutte le modifiche con PowerShell.

## <a name="status"></a>Status
Un modo per verificare che sia installato l'estensione è di visualizzare lo stato dell'agente nel portale di Azure. Selezionare **tutte le impostazioni** nella finestra di macchina virtuale e quindi fare clic su **estensioni**. Verrà elencata l'estensione **SQLIaaSExtension** .

![Estensione di SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È inoltre possibile utilizzare il **Get AzureVMSqlServerExtension** cmdlet PowerShell di Azure.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Il comando precedente conferma l'installazione dell'agente e ne fornisce informazioni generali sullo stato. È inoltre possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatizzata con i comandi seguenti.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Rimozione
Nel portale di Azure, è possibile disinstallare l'estensione facendo clic sui puntini di sospensione di **estensioni** finestra delle proprietà di macchina virtuale. Fare quindi clic su **Elimina**.

![Disinstallazione dell'estensione di SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È inoltre possibile utilizzare il **Remove AzureRmVMSqlServerExtension** cmdlet di PowerShell.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per ulteriori informazioni, vedere gli articoli a cui fa riferimento il [servizi supportati](#supported-services) sezione di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

