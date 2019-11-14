---
title: Automatizzare le attività di gestione con l'estensione dell'agente IaaS
description: Questo articolo descrive come gestire l'estensione dell'agente IaaS SQL Server, che consente di automatizzare attività di amministrazione SQL Server specifiche. Sono inclusi backup automatizzato, applicazione automatica di patch e integrazione Azure Key Vault.
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
ms.openlocfilehash: 9aae386e21df6711fc4984a7abfd34f418399f76
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034188"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizzare le attività di gestione in macchine virtuali di Azure usando l'estensione SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione SQL Server IaaS Agent (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo articolo fornisce una panoramica dei servizi supportati dall'estensione. Questo articolo fornisce anche istruzioni per l'installazione, lo stato e la rimozione dell'estensione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [SQL Server estensione dell'agente IaaS per le vm SQL Server (classica)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | DESCRIZIONE |
| --- | --- |
| **SQL Server backup automatico** |Automatizza la pianificazione dei backup per tutti i database per l'istanza predefinita o un'istanza denominata [corretta installata](virtual-machines-windows-sql-server-iaas-faq.md#administration) di SQL Server nella macchina virtuale. Per ulteriori informazioni, vedere [backup automatico per SQL Server in macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server applicazione automatica di patch** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per ulteriori informazioni, vedere applicazione [automatica di patch per SQL Server in macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente Azure Key Vault nella VM di SQL Server. Per ulteriori informazioni, vedere [configurare l'integrazione Azure Key Vault per SQL Server in macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-ps-sql-keyvault.md). |

Dopo l'installazione e l'esecuzione dell'estensione dell'agente IaaS SQL Server, rende disponibili le funzionalità di amministrazione:

* Nel pannello SQL Server della macchina virtuale nella portale di Azure e Azure PowerShell per le immagini SQL Server in Azure Marketplace.
* Tramite Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>prerequisiti
Di seguito sono riportati i requisiti per l'uso dell'estensione SQL Server IaaS Agent nella macchina virtuale:

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

**Azure PowerShell**:

* [Scaricare e configurare i comandi di Azure PowerShell più recenti](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installare
L'estensione IaaS SQL Server viene installata quando si registra la VM SQL Server con il [provider di risorse della macchina virtuale SQL](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessario, è possibile installare manualmente l'agente di SQL Server IaaS usando il comando PowerShell seguente: 

  ```powershell-interactive
    Set-AzVMExtension -ResourceGroupName "<ResourceGroupName>" `
    -Location "<VMLocation>" -VMName "<VMName>" `
    -Name "SqlIaasExtension" -Publisher "Microsoft.SqlServer.Management" `
    -ExtensionType "SqlIaaSAgent" -TypeHandlerVersion "2.0";  
  ```

> [!NOTE]
> L'installazione dell'estensione riavvia il servizio SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installare in una macchina virtuale con una singola istanza denominata SQL Server
L'estensione SQL Server IaaS funzionerà con un'istanza denominata in SQL Server se viene disinstallata l'istanza predefinita e viene reinstallata l'estensione IaaS.

Per utilizzare un'istanza denominata di SQL Server, attenersi alla seguente procedura:
   1. Distribuire una macchina virtuale SQL Server da Azure Marketplace. 
   1. Disinstallare l'estensione IaaS dalla [portale di Azure](https://portal.azure.com).
   1. Disinstallare completamente SQL Server all'interno della VM SQL Server.
   1. Installare SQL Server con un'istanza denominata nell'SQL Server macchina virtuale. 
   1. Installare l'estensione IaaS dalla portale di Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Ottenere lo stato dell'estensione IaaS SQL Server
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **tutte le impostazioni** nella finestra della macchina virtuale e quindi fare clic su **estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Stato del SQL Server estensione dell'agente IaaS nella portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Il comando precedente conferma che l'agente è installato e fornisce informazioni generali sullo stato. È possibile ottenere informazioni di stato specifiche sul backup automatico e l'applicazione di patch usando i comandi seguenti:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Rimozione
Nella portale di Azure è possibile disinstallare l'estensione selezionando i puntini di sospensione nella finestra **estensioni** delle proprietà della macchina virtuale. Selezionare **Elimina**.

![Disinstallazione dell'estensione SQL Server IaaS Agent in portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet **di PowerShell Remove-AzVMSqlServerExtension** :

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per ulteriori informazioni, vedere gli articoli a cui si fa riferimento nella sezione [servizi supportati](#supported-services) di questo articolo.

Per ulteriori informazioni sull'esecuzione di SQL Server in macchine virtuali di Azure, vedere la pagina relativa alla [SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).
