---
title: Automatizzare le attività di gestione con l'estensione IaaS Agent
description: Questo articolo descrive come gestire l'estensione SQL Server IaaS Agent, che automatizza attività di amministrazione specifiche di SQL Server, tra cui il backup automatizzato, l'applicazione automatica delle patch e l'integrazione di Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
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
ms.openlocfilehash: 89210cda6390fd7c3cf4ca2877b8899559a41321
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668713"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizzare le attività di gestione in macchine virtuali di Azure usando l'estensione SQL Server IaaS Agent
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


> [!div class="op_single_selector"]
> * [Gestione risorse](sql-server-iaas-agent-extension-automate-management.md)
> * [Classico](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione SQL Server IaaS Agent (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo articolo offre una panoramica dei servizi supportati dall'estensione, oltre a istruzioni per l'installazione, lo stato e la rimozione dell'estensione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere l'argomento relativo all'[estensione SQL Server IaaS Agent per le macchine virtuali di SQL Server (distribuzione classica)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).


## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
| --- | --- |
| **Backup automatizzato per SQL Server** |Consente di automatizzare la pianificazione delle operazioni di backup per tutti i database correlati all'istanza predefinita o a un'istanza denominata [correttamente installata](frequently-asked-questions-faq.md#administration) di SQL Server nella macchina virtuale. Per altre informazioni, vedere l'articolo [Backup automatico per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-backup-sql-2014.md). |
| **Applicazione automatica delle patch per SQL Server** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per altre informazioni, vedere l'articolo [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere l'articolo [Configurare l'integrazione di Azure Key Vault per SQL Server in macchine virtuali di Azure (Resource Manager)](azure-key-vault-integration-configure.md). |

Dopo l'installazione e l'esecuzione dell'estensione SQL Server IaaS Agent, le funzionalità di amministrazione sono disponibili:

* Nel pannello SQL Server della macchina virtuale nel portale di Azure e dalle immagini di Azure PowerShell per SQL Server in Azure Marketplace.
* Da Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>Prerequisiti
Di seguito sono riportati i requisiti per l'uso dell'estensione SQL Server Iaas Agent nella VM:

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

**Azure PowerShell**:

* [Scaricare e configurare i comandi di Azure PowerShell più recenti](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>Installazione
L'estensione IaaS SQL Server viene installata quando si registra la VM SQL Server con il [SQL Server provider di risorse VM](sql-vm-resource-provider-register.md). Se necessario, è possibile installare SQL Server IaaS Agent manualmente usando il seguente comando di PowerShell: 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> L'installazione dell'estensione riavvia il servizio SQL Server. 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Eseguire l'installazione in una macchina virtuale con una singola istanza denominata di SQL Server
L'estensione SQL Server IaaS funzionerà con un'istanza denominata in SQL Server se viene disinstallata l'istanza predefinita e viene reinstallata l'estensione IaaS.

Per usare un'istanza denominata di SQL Server, attenersi alla seguente procedura:
   1. Distribuire una macchina virtuale di SQL Server da Azure Marketplace. 
   1. Disinstallare l'estensione IaaS dal [portale di Azure](https://portal.azure.com).
   1. Disinstallare completamente SQL Server nella macchina virtuale di SQL Server.
   1. Installare SQL Server con un'istanza denominata nella macchina virtuale di SQL Server. 
   1. Installare l'estensione IaaS dal portale di Azure.  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>Ottenere lo stato dell'estensione SQL Server IaaS
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nella finestra della macchina virtuale e quindi selezionare **Estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Stato dell'estensione SQL Server IaaS Agent nel portale di Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet **Get-AzVMSqlServerExtension** di Azure PowerShell:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Il comando precedente verifica se l'agente è installato e visualizza informazioni generali sullo stato. È anche possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatica usando i comandi seguenti:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Rimozione
Nel portale di Azure è possibile disinstallare l'estensione selezionando i puntini di sospensione nella finestra **Estensioni** delle proprietà della macchina virtuale. Selezionare **Elimina**.

![Disinstallazione dell'estensione SQL Server IaaS Agent nel portale di Azure](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet **Remove-AzVMSqlServerExtension** di Azure PowerShell:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli articoli citati nella sezione [Servizi supportati](#supported-services) di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server nelle macchine virtuali di Azure, vedere [Cos'è SQL Server in Macchine virtuali di Azure?](sql-server-on-azure-vm-iaas-what-is-overview.md).
