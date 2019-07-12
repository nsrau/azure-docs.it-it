---
title: Automatizzare le attività di gestione delle macchine virtuali di Azure con l'estensione agente IaaS di SQL Server | Microsoft Docs
description: Questo articolo descrive come gestire l'estensione SQL Server Agent, che automatizza attività di amministrazione specifiche di SQL Server. Queste includono il backup automatizzato, l'applicazione automatica delle patch e l'integrazione dell'insieme di credenziali delle chiavi di Azure.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798062"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatizzare le attività di gestione delle macchine virtuali di Azure con l'estensione agente IaaS di SQL Server
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione SQL Server IaaS Agent (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo articolo offre una panoramica e servizi supportati dall'estensione, nonché istruzioni per l'installazione, lo stato e la rimozione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [Estensione Agente IaaS di SQL Server (distribuzione classica)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Esistono tre modalità di gestione SQL per l'estensione SQL IaaS: **Completa**, **Lightweight**, e **NoAgent**. 

- **Completa** modalità offre tutte le funzionalità, ma è necessario riavviare le autorizzazioni di SQL Server e software Assurance. Questa è l'opzione che viene installato per impostazione predefinita e deve essere utilizzato per la gestione di una VM di SQL Server con una sola istanza. 

- **Lightweight** non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e l'edizione di SQL Server. Questa opzione deve essere usato per le macchine virtuali di SQL Server con più istanze, o coinvolta in un'istanza del cluster di failover (FCI). 

- **NoAgent** dedicata per SQL Server 2008 e SQL Server 2008 R2 installato in Windows Server 2008. Per informazioni su come utilizzare `NoAgent` modalità per l'immagine di Windows Server 2008, vedere [registrazione Windows Server°2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | DESCRIZIONE |
| --- | --- |
| **Backup automatico di SQL** |Consente di automatizzare la pianificazione dei backup per tutti i database tra l'istanza predefinita o una [correttamente installato](virtual-machines-windows-sql-server-iaas-faq.md#administration) un'istanza denominata di SQL Server nella macchina virtuale. Per altre informazioni, vedere [Backup automatico per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Applicazione automatica delle patch di SQL** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per altre informazioni, vedere [Configurare l'integrazione dell'insieme di credenziali delle chiavi di Azure per SQL Server in Macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Dopo averlo installato e messo in esecuzione, l'estensione SQL Server IaaS Agent rende disponibili queste funzionalità di amministrazione nel pannello SQL Server della macchina virtuale nel portale di Azure e tramite Azure PowerShell per le immagini del marketplace di SQL Server e Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>Prerequisiti
Requisiti per l'uso dell'Estensione Agente IaaS di SQL Server nella VM:

**Sistema operativo**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versioni di SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [Scaricare e configurare i comandi di Azure PowerShell più recenti](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Modificare la modalità di gestione

È possibile visualizzare la modalità corrente dell'agente IaaS di SQL con PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Macchine virtuali di SQL Server con il *NoAgent* o *leggero* installata l'estensione IaaS, è possibile aggiornare la modalità *completo* usando il portale di Azure. Non è possibile effettuare il downgrade - a tale scopo, è necessario disinstallare l'estensione SQL IaaS completamente e installarlo di nuovo. 

La modalità di aggiornamento dell'agente per *completo*, eseguire le operazioni seguenti: 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alle [macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) risorsa. 
1. Selezionare la macchina virtuale di SQL Server e selezionare **Panoramica**. 
1. Per le macchine virtuali SQL con il *NoAgent* oppure *lightweight* modalità IaaS, selezionare il messaggio per **solo gli aggiornamenti di edizione e tipo di licenza sono disponibili con l'estensione SQL IaaS**.

    ![Avviare la modalità di modifica dal portale](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Accettare **riavviare il servizio SQL Server** selezionando la casella di controllo e quindi selezionare **conferma** per aggiornare la modalità di IaaS su 'full'. 

    ![Abilitare la gestione completa per l'estensione IaaS](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Installazione
È installata l'estensione SQL IaaS quando si registra una VM di SQL Server con il [provider di risorse di VM di SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). Tuttavia, se necessario, l'agente IaaS di SQL può essere installato anche manualmente usando *completa* oppure *leggero* installazione in modalità. 

Il *completo* estensione agente IaaS di SQL Server viene installato automaticamente quando si effettua il provisioning di una delle immagini della raccolta di macchine virtuali SQL Server nel portale di Azure. 

### <a name="full-mode-installation"></a>Installazione in modalità completa
Il *completo* estensione SQL IaaS offre gestibilità completa per una singola istanza nella VM di SQL Server. Se è presente un'istanza predefinita, l'estensione quindi funzionerà con l'istanza predefinita e non supporterà la gestione di altre istanze. Se è presente alcuna istanza predefinita, ma solo un'istanza denominata, quindi gestiranno l'istanza denominata. Se è presente alcuna istanza predefinita e sono presenti più istanze denominate, quindi l'estensione avrà esito negativo per l'installazione. 

Installare il *completo* modalità di IaaS SQL verrà riavviato il servizio SQL Server. Per evitare il riavvio del servizio SQL Server, installare il *leggero* la modalità con limitato invece la facilità di gestione. 

Installare l'agente IaaS di SQL con *completo* modalità usando PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parametro | Valori accettabili                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, o `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Se l'estensione non è già installato, installare il **completo** estensione riavvia il servizio SQL Server. Uso **leggero** modalità per evitare il riavvio del servizio SQL Server. 
> - L'aggiornamento dell'estensione SQL IaaS non riavvia il servizio SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installare in una macchina virtuale con una singola istanza denominata di SQL Server
L'estensione SQL IaaS funzionerà con un'istanza denominata su un SQL Server se l'istanza predefinita viene disinstallato e si reinstalla l'estensione IaaS.

Per usare un'istanza denominata di SQL Server, eseguire le operazioni seguenti:
   1. Distribuire una VM di SQL Server dal marketplace. 
   1. Disinstallare l'estensione IaaS dall'interno di [portale di Azure](https://portal.azure.com).
   1. Disinstallare SQL Server completamente all'interno di VM di SQL Server.
   1. Installare SQL Server con un'istanza denominata all'interno della VM di SQL Server. 
   1. Installare l'estensione IaaS dal portale di Azure.  


### <a name="install-in-lightweight-mode"></a>Installare in modalità di caricamento leggero
Modalità di caricamento leggero non verrà riavviato il servizio SQL Server, ma offre funzionalità limitate. 

Installare l'agente IaaS di SQL con *leggero* modalità usando PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parametro | Valori accettabili                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, o `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Ottenere lo stato dell'estensione SQL IaaS
Un modo per verificare che l'estensione sia installata consiste nel visualizzare lo stato dell'agente nel portale di Azure. Selezionare **Tutte le impostazioni** nel pannello della macchina virtuale e quindi fare clic su **Estensioni**. L'estensione **SQLIaaSExtension** dovrebbe essere visualizzata nell'elenco.

![Estensione SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

È anche possibile usare il cmdlet **Get-AzVMSqlServerExtension** di Azure PowerShell.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Il comando precedente conferma l'installazione dell'agente e ne fornisce informazioni generali sullo stato. È inoltre possibile ottenere informazioni specifiche sullo stato del backup e dell'applicazione di patch in modalità automatizzata con i comandi seguenti.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Rimozione
Nel portale di Azure, è possibile disinstallare l'estensione facendo clic sui puntini di sospensione sul **estensioni** finestra delle proprietà di macchina virtuale. Fare quindi clic su **Elimina**.

![Disinstallare l'estensione SQL Server IaaS Agent nel portale di Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

È anche possibile usare il cmdlet **Remove-AzVMSqlServerExtension** di Azure PowerShell.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Passaggi successivi
Iniziare a usare uno dei servizi supportati dall'estensione. Per altre informazioni, vedere gli articoli a cui fa riferimento il [servizi supportati](#supported-services) sezione di questo articolo.

Per altre informazioni sull'esecuzione di SQL Server in Macchine virtuali di Azure, vedere [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

