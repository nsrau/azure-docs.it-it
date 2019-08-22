---
title: Automatizzare le attività di gestione in macchine virtuali di Azure usando l'estensione SQL Server agente IaaS | Microsoft Docs
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59b5138950e0fb94ea0051fa9cfe9aa75cd7d770
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877798"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Automatizzare le attività di gestione in macchine virtuali di Azure usando l'estensione SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Gestione risorse](virtual-machines-windows-sql-server-agent-extension.md)
> * [Classico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

L'estensione SQL Server IaaS Agent (SQLIaaSExtension) viene eseguita su macchine virtuali di Azure per automatizzare le attività di amministrazione. Questo articolo fornisce una panoramica dei servizi supportati dall'estensione. Questo articolo fornisce anche istruzioni per l'installazione, lo stato e la rimozione dell'estensione.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Per visualizzare la versione classica di questo articolo, vedere [SQL Server estensione dell'agente IaaS per le vm SQL Server (classica)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Sono disponibili tre modalità di gestibilità per il SQL Server estensione IaaS: 

- La modalità **completa** offre tutte le funzionalità, ma richiede il riavvio delle autorizzazioni di SQL Server e di amministratore di sistema. Questa è l'opzione che viene installata per impostazione predefinita. Usarlo per la gestione di una macchina virtuale SQL Server con una singola istanza. 

- **Lightweight** non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server. Usare questa opzione per SQL Server VM con più istanze o per partecipare a un'istanza del cluster di failover di. 

- **Noagent** è dedicato a SQL Server 2008 e SQL Server 2008 R2 installato in Windows Server 2008. Per informazioni sull'uso di questa modalità per l'immagine di Windows Server 2008, vedere [registrazione di Windows server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Servizi supportati
L'Estensione Agente IaaS di SQL Server supporta le attività di amministrazione seguenti:

| Funzionalità di amministrazione | Descrizione |
| --- | --- |
| **SQL Server backup automatico** |Automatizza la pianificazione dei backup per tutti i database per l'istanza predefinita o un'istanza denominata [corretta installata](virtual-machines-windows-sql-server-iaas-faq.md#administration) di SQL Server nella macchina virtuale. Per ulteriori informazioni, vedere [backup automatico per SQL Server in macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-sql-automated-backup.md). |
| **SQL Server applicazione automatica di patch** |Consente di configurare una finestra di manutenzione durante la quale è possibile eseguire aggiornamenti importanti di Windows nella macchina virtuale, evitandone l'esecuzione nei periodi di picco del carico di lavoro. Per ulteriori informazioni, vedere applicazione [automatica di patch per SQL Server in macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-sql-automated-patching.md). |
| **Integrazione di Azure Key Vault** |Consente di installare e configurare automaticamente l'insieme di credenziali delle chiavi di Azure nella VM di SQL Server. Per ulteriori informazioni, vedere [configurare l'integrazione Azure Key Vault per SQL Server in macchine virtuali di Azure (Gestione risorse)](virtual-machines-windows-ps-sql-keyvault.md). |

Dopo l'installazione e l'esecuzione dell'estensione dell'agente IaaS SQL Server, rende disponibili le funzionalità di amministrazione:

* Nel pannello SQL Server della macchina virtuale nella portale di Azure e Azure PowerShell per le immagini SQL Server in Azure Marketplace.
* Tramite Azure PowerShell per le installazioni manuali dell'estensione. 

## <a name="prerequisites"></a>Prerequisiti
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


## <a name="change-management-modes"></a>Modalità di gestione delle modifiche

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server le macchine virtuali con l'estensione IaaS *Lightweight* installata possono aggiornare la modalità a _full_ usando il portale di Azure. SQL Server le macchine virtuali in modalità _senza agente_ possono eseguire l'aggiornamento alla _versione completa_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile effettuare il downgrade. a tale scopo, sarà necessario disinstallare completamente l'estensione IaaS di SQL e installarla nuovamente. 

Per aggiornare la modalità agente a Full: 


# <a name="azure-portaltabazure-portal"></a>[Portale di Azure](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa [macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selezionare la macchina virtuale SQL Server e scegliere **Panoramica**. 
1. Per SQL Server VM con la modalità noagent o Lightweight IaaS, selezionare gli **unici tipi di licenza e gli aggiornamenti dell'edizione sono disponibili con il messaggio di estensione SQL IaaS** .

   ![Selezioni per modificare la modalità dal portale](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selezionare la casella di controllo **Accetto di riavviare il servizio SQL Server nella macchina virtuale** e quindi selezionare **conferma** per aggiornare la modalità IaaS a completa. 

    ![Casella di controllo per accettare il riavvio del servizio SQL Server nella macchina virtuale](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Eseguire il frammento di codice AZ CLI seguente:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Eseguire il frammento di codice PowerShell seguente:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Installazione
L'estensione IaaS SQL Server viene installata quando si registra la VM SQL Server con il [provider di risorse della macchina virtuale SQL](virtual-machines-windows-sql-register-with-resource-provider.md). Se necessario, è possibile installare manualmente l'agente di SQL Server IaaS usando la modalità Full o Lightweight. 

L'estensione SQL Server agente IaaS in modalità completa viene installata automaticamente quando si esegue il provisioning di una delle immagini SQL Server macchine virtuali di Azure Marketplace usando il portale di Azure. 

### <a name="install-in-full-mode"></a>Installare in modalità completa
La modalità completa per l'estensione IaaS SQL Server offre la gestibilità completa per una singola istanza nella macchina virtuale SQL Server. Se è presente un'istanza predefinita, l'estensione funzionerà con l'istanza predefinita e non supporterà la gestione di altre istanze. Se non esiste un'istanza predefinita, ma solo un'istanza denominata, l'istanza denominata verrà gestita. Se non esiste un'istanza predefinita e sono presenti più istanze denominate, l'estensione non verrà installata. 

Installare il SQL Server agente IaaS con la modalità completa usando PowerShell:

  ```powershell-interactive
     #Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with 'Full' SQL Server IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parametro | Valori accettabili                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` o `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Se l'estensione non è già installata, l'installazione dell'estensione completa riavvia il servizio SQL Server. Per evitare di riavviare il servizio SQL Server, installare invece la modalità Lightweight con una gestione limitata.
> 
> L'aggiornamento dell'estensione IaaS SQL Server non comporta il riavvio del servizio SQL Server. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installare in una macchina virtuale con una singola istanza denominata SQL Server
L'estensione SQL Server IaaS funzionerà con un'istanza denominata in SQL Server se viene disinstallata l'istanza predefinita e viene reinstallata l'estensione IaaS.

Per utilizzare un'istanza denominata di SQL Server:
   1. Distribuire una macchina virtuale SQL Server da Azure Marketplace. 
   1. Disinstallare l'estensione IaaS dalla [portale di Azure](https://portal.azure.com).
   1. Disinstallare completamente SQL Server all'interno della VM SQL Server.
   1. Installare SQL Server con un'istanza denominata nell'SQL Server macchina virtuale. 
   1. Installare l'estensione IaaS dalla portale di Azure.  


### <a name="install-in-lightweight-mode"></a>Installare in modalità Lightweight
La modalità Lightweight non riavvia il servizio SQL Server, ma offre funzionalità limitate. 

Installare il SQL Server agente IaaS con la modalità Lightweight usando PowerShell:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parametro | Valori accettabili                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` o `PAYG`     |
| &nbsp;             | &nbsp;                          |


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

