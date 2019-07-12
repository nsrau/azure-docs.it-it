---
title: Registrare la macchina virtuale di SQL Server in Azure con il provider di risorse di VM di SQL | Microsoft Docs
description: Registrare la macchina virtuale di SQL Server con il provider di risorse di VM di SQL per migliorare la gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c78b5d71fffbf579b15f747c048bd65259039749
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786742"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrare la macchina virtuale di SQL Server in Azure con il provider di risorse di VM di SQL

Questo articolo descrive come registrare la macchina virtuale di Server SQL di Azure (VM) con il provider di risorse di VM di SQL. 

Distribuire automaticamente un'immagine del marketplace di VM di SQL Server tramite il portale di Azure registra la macchina virtuale di SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare automaticamente SQL Server in una macchina virtuale di Azure anziché scegliere un'immagine da Azure Marketplace, è consigliabile registrare VM di SQL Server con il provider di risorse attualmente per:

-  **Conformità** : secondo i termini di prodotto Microsoft, i clienti che usano i [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) deve indicare a Microsoft quando si usa il vantaggio Azure Hybrid - e a tale scopo, è necessario registrare con il provider di risorse di VM di SQL. 

-  **I vantaggi delle funzionalità** -la registrazione delle VM di SQL Server con il provider di risorse Sblocca [applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md), [backup automatico](virtual-machines-windows-sql-automated-backup-v2.md), funzionalità di monitoraggio e la gestibilità, nonché [licensing](virtual-machines-windows-sql-ahb.md) e [edizione](virtual-machines-windows-sql-change-edition.md) flessibilità. In precedenza, questi erano disponibili solo per le immagini di VM di SQL Server da Azure Marketplace.

Self-installazione di SQL Server in una macchina virtuale di Azure o il provisioning di una macchina virtuale di Azure da un VHD personalizzato con SQL Server è conforme tramite il vantaggio Azure Hybrid per SQL Server con la condizione che i clienti indicano che utilizzano a Microsoft tramite la registrazione con risorse di VM di SQL provider. 

Per poter usare il provider di risorse di VM di SQL, è anche necessario registrare il provider di risorse di VM di SQL con la sottoscrizione. Ciò può essere eseguita con il portale di Azure, della riga di comando di Azure e PowerShell. 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Oggetto [VM di SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) e [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Registrare con il provider di risorse di VM di SQL
Se il [estensione SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) è già installato nella VM, quindi la registrazione con il provider di risorse di VM di SQL è semplicemente la creazione di una risorsa dei metadati di tipo Microsoft.SqlVirtualMachine/SqlVirtualMachines. Di seguito è il frammento di codice per registrare il provider di risorse di VM di SQL, se l'estensione SQL IaaS è già installato nella macchina virtuale. È necessario specificare il tipo di licenza di SQL Server desiderato quando si registra con provider di risorse di VM di SQL come ' in base al consumo ' o 'AHUB'. 

Registrare VM di SQL Server usando PowerShell con il frammento di codice seguente:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'}  
  
  ```

Se l'estensione SQL IaaS non è installato nella macchina virtuale, è possibile registrare con il provider di risorse di VM di SQL specificando la modalità di gestione di SQL leggera. Modalità di gestione lightweight SQL, VM di SQL del provider di risorse verrà automaticamente Installa estensione SQL IaaS nel [modalità di caricamento leggero](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verificare SQL Server i metadati dell'istanza; questo verrà non riavviare il servizio SQL Server. È necessario specificare il tipo di licenza di SQL Server desiderato quando si registra con provider di risorse di VM di SQL come ' in base al consumo ' o 'AHUB'. 

Registrare VM di SQL Server in modalità di gestione SQL semplice uso di PowerShell con il frammento di codice seguente:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```
La registrazione con il provider di risorse di VM di SQL nel [modalità di caricamento leggero](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) verrà garantire la conformità e abilitare la gestione flessibile delle licenze, nonché gli aggiornamenti edizione di SQL Server sul posto. Istanze del Cluster di failover e le distribuzioni a istanze multiple possono essere registrate con provider di risorse di VM di SQL solo in modalità di caricamento leggero. È possibile seguire le istruzioni disponibili nel portale di Azure per eseguire l'aggiornamento a [completi della modalità](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) e abilita il set di funzionalità di gestibilità completa con un riavvio di SQL Server in qualsiasi momento. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrare SQL Server 2008/R2 su macchine virtuali Windows Server 2008

SQL Server 2008 e 2008 R2 installato in Windows Server 2008 possono essere registrate con la VM SQL resource forniscono nel [NoAgent](virtual-machines-windows-sql-server-agent-extension.md) modalità. Questa opzione assicura la conformità e consente la VM di SQL Server da monitorare nel portale di Azure con funzionalità limitata.

Nella tabella seguente illustra nel dettaglio i valori accettabili per i parametri specificati durante la registrazione:

| Parametro | Valori accettabili                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`, o `'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` o `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Per registrare il SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, usare il frammento di codice Powershell seguente:  

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;sqlLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```



## <a name="verify-registration-status"></a>Verificare lo stato della registrazione
È possibile verificare se SQL Server è già stato registrato con il provider di risorse di VM di SQL usando il portale di Azure, della riga di comando di Azure o PowerShell. 

# <a name="azure-portaltabazure-portal"></a>[Portale di Azure](#tab/azure-portal)
Per verificare lo stato della registrazione nel portale di Azure, eseguire le operazioni seguenti.

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md).
1. Selezionare la macchina virtuale di SQL Server dall'elenco. Se la macchina virtuale di SQL Server non è elencata qui, è probabile che la macchina virtuale di SQL Server non è stata registrata con il provider di risorse di VM di SQL. 
1. Visualizzare il valore in `Status`. Se `Status = Succeeded`, quindi la VM SQL Server è stata registrata correttamente con il provider di risorse di VM di SQL. 

    ![Verificare lo stato con la registrazione SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Verificare lo stato di registrazione corrente della macchina virtuale di SQL Server con il seguente comando AZ dell'interfaccia della riga. `ProvisioningState` verranno visualizzati `Succeeded` se la registrazione ha esito positivo. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Verificare lo stato di registrazione corrente della macchina virtuale di SQL Server con il cmdlet di PowerShell seguente. `ProvisioningState` verranno visualizzati `Succeeded` se la registrazione ha esito positivo. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Un errore indica che la macchina virtuale di SQL Server non è stata registrata con il provider di risorse. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrare il provider di risorse di VM di SQL con la sottoscrizione 

Per registrare la macchina virtuale di SQL Server con il provider di risorse di VM di SQL, è necessario registrare il provider di risorse nella sottoscrizione. È possibile farlo con il portale di Azure o Azure CLI.

# <a name="azure-portaltabazure-portal"></a>[Portale di Azure](#tab/azure-portal)


La procedura seguente verrà registrato il provider di risorse di VM di SQL alla sottoscrizione di Azure usando il portale di Azure. 

1. Accedere al Portale di Azure e passare a **Tutti i servizi**. 
1. Passare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nel **abbonamenti** pagina, passare alla **i provider di risorse**. 
1. Digitare `sql` nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra *Registra*, *Ripeti registrazione*, o *Annulla registrazione* per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

   ![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Il frammento di codice seguente verrà registrato il provider di risorse di VM di SQL alla sottoscrizione di Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Il frammento di codice PowerShell seguente verrà registrato il provider di risorse di VM di SQL alla sottoscrizione di Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Note

 - Il provider di risorse di VM di SQL supporta solo macchine virtuali di SQL Server distribuite tramite il gestore di risorse ''. VM di SQL Server distribuito usando che il modello classico non è supportato. 
 - Il provider di risorse di VM di SQL supporta solo macchine virtuali di SQL Server distribuito nel cloud pubblico. Le distribuzioni al privato o cloud per enti pubblici, non sono supportate. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


