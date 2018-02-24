---
title: "Automatizzare le attività di gestione in macchine virtuali SQL (Resource Manager) | Microsoft Docs"
description: "Questo articolo descrive come gestire l'estensione SQL Server Agent, che automatizza attività di amministrazione specifiche di SQL Server. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: craigg
editor: 
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/05/2018
ms.author: jroth
ms.openlocfilehash: 6510c4205234f5dfb98a7463af61b1293061bb4e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-resource-manager"></a>Automatizzare le attività di gestione in macchine virtuali SQL con l'estensione SQL Server Agent (Resource Manager)
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'Estensione Agente IaaS di SQL Server (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo articolo fornisce una panoramica dei servizi supportati dall'estensione e delle istruzioni per l'installazione, lo stato e la rimozione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [Estensione Agente IaaS di SQL Server (distribuzione classica)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | DESCRIZIONE |
| --- | --- |
| **Backup automatico di SQL** |Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita di SQL Server nella VM. Per altre informazioni, vedere [Backup automatico per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Applicazione automatica delle patch di SQL** |Consente di configurare una finestra di manutenzione durante la quale eseguire gli aggiornamenti della VM, evitandone l'esecuzione durante i periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in Macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Dopo averlo installato e messo in esecuzione, l'estensione SQL Server IaaS Agent rende disponibili queste funzionalità di amministrazione nel pannello SQL Server della macchina virtuale nel portale di Azure e tramite Azure PowerShell per le immagini del marketplace di SQL Server e Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>prerequisiti
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

> [!IMPORTANT]
> Se non è già stata eseguita, l'installazione dell'estensione riavvia il servizio SQL Server.

È possibile anche installare l'estensione SQL Server IaaS Agent in una macchina virtuale Windows Server con il solo sistema operativo. Questa soluzione è supportata solo se SQL Server è stato installato manualmente sul computer. Installare quindi l'estensione manualmente usando lo stesso cmdlet di PowerShell **Set-AzureVMSqlServerExtension**.

> [!NOTE]
> Se si installa manualmente l'estensione Agente IaaS di SQL Server in una macchina virtuale Windows Server con il solo sistema operativo, non è possibile gestire le impostazioni di configurazione di SQL Server tramite il portale di Azure. In questo scenario è necessario eseguire tutte le modifiche con PowerShell.

## <a name="status"></a>Status
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nel pannello della macchina virtuale e quindi fare clic su **Estensioni**. Verrà elencata l'estensione **SQLIaaSExtension** .

![Estensione SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet **Get-AzureVMSqlServerExtension** di Azure PowerShell.

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

Il comando precedente conferma l'installazione dell'agente e ne fornisce informazioni generali sullo stato. È inoltre possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatizzata con i comandi seguenti.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Rimozione
Nel portale di Azure è possibile disinstallare l'estensione facendo clic sui puntini di sospensione nella finestra **Estensioni** delle proprietà della macchina virtuale. Fare quindi clic su **Elimina**.

![Disinstallare l'estensione SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet **Remove-AzureRmVMSqlServerExtension** di PowerShell.

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli articoli citati nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

