---
title: Registrare SQL Server macchina virtuale in Azure con il provider di risorse VM SQL | Microsoft Docs
description: Registrare la macchina virtuale di SQL Server con il provider di risorse VM SQL per migliorare la gestibilità.
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
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305877"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrare SQL Server macchina virtuale in Azure con il provider di risorse VM SQL

Questo articolo descrive come registrare la macchina virtuale di Azure SQL Server (VM) con il provider di risorse VM SQL. 

La distribuzione di un'immagine del Marketplace SQL Server VM tramite il portale di Azure registra automaticamente la macchina virtuale SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure anziché scegliere un'immagine da Azure Marketplace, è necessario registrare la VM SQL Server con il provider di risorse per:

-  **Conformità** : in base alle condizioni del prodotto Microsoft, i clienti che usano il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) devono indicare a Microsoft quando usano il vantaggio Azure Hybrid e, a tale scopo, devono registrarsi con il provider di risorse della macchina virtuale SQL. 

-  **Vantaggi della funzionalità** : la registrazione della macchina virtuale di SQL Server con il provider di risorse Sblocca l'applicazione [automatica di patch](virtual-machines-windows-sql-automated-patching.md), il [backup automatico](virtual-machines-windows-sql-automated-backup-v2.md), le funzionalità di monitoraggio e gestione, nonché la flessibilità di [licenze](virtual-machines-windows-sql-ahb.md) ed [edizioni](virtual-machines-windows-sql-change-edition.md) . In precedenza erano disponibili solo per SQL Server immagini di VM da Azure Marketplace.

L'installazione automatica di SQL Server in una macchina virtuale di Azure o il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato con SQL Server è conforme al Vantaggio Azure Hybrid per SQL Server con la condizione che i clienti indichino che usano a Microsoft mediante la registrazione con la risorsa VM SQL provider. 

Per usare il provider di risorse VM SQL, è necessario registrare anche il provider di risorse VM SQL con la sottoscrizione. Questa operazione può essere eseguita con il portale di Azure, l'interfaccia della riga di comando di Azure e PowerShell. 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure e [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Eseguire la registrazione con il provider di risorse VM SQL
Se l' [estensione SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) è già installata nella macchina virtuale, la registrazione con il provider di risorse VM SQL crea semplicemente una risorsa di metadati di tipo Microsoft. SqlVirtualMachine/SqlVirtualMachines. Di seguito è riportato il frammento di codice da registrare con il provider di risorse VM SQL se l'estensione IaaS SQL è già installata nella macchina virtuale. È necessario specificare il tipo di licenza SQL Server desiderata per la registrazione con il provider di risorse VM SQL come ' PAYG ' o ' vantaggio Ahu '. 

Registrare SQL Server VM con PowerShell con il frammento di codice seguente:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Se l'estensione SQL IaaS non è installata nella macchina virtuale, è possibile eseguire la registrazione con il provider di risorse VM SQL specificando la modalità di gestione SQL Lightweight. In modalità di gestione SQL Lightweight, il provider di risorse VM SQL installerà automaticamente l'estensione IaaS di SQL in [modalità Lightweight](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verificherà SQL Server metadati dell'istanza; il servizio SQL Server non verrà riavviato. È necessario specificare il tipo di licenza SQL Server desiderata per la registrazione con il provider di risorse VM SQL come ' PAYG ' o ' vantaggio Ahu '. 

Registrare SQL Server VM in modalità Lightweight SQL Management usando PowerShell con il frammento di codice seguente:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

La registrazione con il provider di risorse VM SQL in [modalità Lightweight](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) garantisce la conformità e Abilita le licenze flessibili, nonché gli aggiornamenti sul posto SQL Server edizione. Le istanze del cluster di failover e le distribuzioni a istanze diverse possono essere registrate con il provider di risorse VM SQL solo in modalità lightweight. È possibile seguire le istruzioni disponibili in portale di Azure per eseguire l'aggiornamento alla [modalità completa](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) e abilitare la funzionalità di gestibilità completa con un SQL Server riavviare in qualsiasi momento. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrare SQL Server 2008/R2 in macchine virtuali Windows Server 2008

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 possono essere registrati con la risorsa VM SQL disponibile in modalità [noagent](virtual-machines-windows-sql-server-agent-extension.md) . Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.

La tabella seguente illustra in dettaglio i valori accettabili per i parametri forniti durante la registrazione:

| Parametro | Valori accettabili                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`o`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` o `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Per registrare la SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, usare il frammento di codice di PowerShell seguente:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Verificare lo stato della registrazione
È possibile verificare se il SQL Server è già stato registrato con il provider di risorse VM SQL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 
Per verificare lo stato della registrazione usando il portale di Azure, seguire questa procedura.

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md).
1. Selezionare il SQL Server VM dall'elenco. Se la macchina virtuale SQL Server non è inclusa nell'elenco, è probabile che la macchina virtuale di SQL Server non sia stata registrata con il provider di risorse della macchina virtuale SQL. 
1. Visualizzare il valore in `Status`. Se `Status = Succeeded`, la macchina virtuale SQL Server è stata registrata correttamente con il provider di risorse della macchina virtuale SQL. 

    ![Verificare lo stato con la registrazione di SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>INTERFACCIA della riga di comando AZ

Verificare lo stato di registrazione della macchina virtuale SQL Server corrente con il comando AZ CLI seguente. `ProvisioningState`indicherà `Succeeded` se la registrazione è stata completata. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Verificare lo stato di registrazione della macchina virtuale SQL Server corrente con il cmdlet di PowerShell seguente. `ProvisioningState`indicherà `Succeeded` se la registrazione è stata completata. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Un errore indica che la macchina virtuale SQL Server non è stata registrata con il provider di risorse. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrare il provider di risorse VM SQL con la sottoscrizione 

Per registrare la macchina virtuale di SQL Server con il provider di risorse VM SQL, è necessario registrare il provider di risorse nella sottoscrizione. Questa operazione può essere eseguita con il portale di Azure o l'interfaccia della riga di comando di Azure.

### <a name="azure-portal"></a>Portale di Azure

La procedura seguente consente di registrare il provider di risorse VM SQL nella sottoscrizione di Azure usando il portale di Azure. 

1. Accedere al Portale di Azure e passare a **Tutti i servizi**. 
1. Passare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nella pagina **sottoscrizioni** passare a **provider di risorse**. 
1. Digitare `sql` nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra *Registra*, *Ripeti registrazione*, o *Annulla registrazione* per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

   ![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>INTERFACCIA della riga di comando AZ
Il frammento di codice seguente registrerà il provider di risorse VM SQL nella sottoscrizione di Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

Il frammento di codice di PowerShell seguente registrerà il provider di risorse VM SQL nella sottoscrizione di Azure.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Note

 - Il provider di risorse VM SQL supporta solo SQL Server macchine virtuali distribuite con il "Gestione risorse". SQL Server macchine virtuali distribuite con il "modello classico" non sono supportate. 
 - Il provider di risorse VM SQL supporta solo SQL Server macchine virtuali distribuite nel cloud pubblico. Le distribuzioni nel cloud privato o per enti pubblici non sono supportate. 
 
## <a name="frequently-asked-questions"></a>Domande frequenti 

**È necessario registrare la macchina virtuale SQL Server sottoposta a provisioning da un'immagine SQL in Azure Marketplace?**

No. Microsoft registra le macchine virtuali di cui è stato effettuato il provisioning dalle immagini SQL in Azure Marketplace. La registrazione con il provider di risorse VM SQL è necessaria solo se non è stato effettuato il provisioning della macchina virtuale dalle immagini SQL in Azure Marketplace e SQL Server è stato installato autonomamente.

**Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?** 

Sì. I clienti devono registrare la macchina virtuale SQL Server con il provider di risorse VM SQL se non hanno usato un'immagine di SQL Server da Azure Marketplace e SQL Server autoinstallati o hanno portato il disco rigido virtuale personalizzato. Le macchine virtuali di proprietà di tutti i tipi di sottoscrizioni (Direct, EA e CSP) possono eseguire la registrazione con il provider di risorse VM SQL.

**È consigliabile eseguire la registrazione con il provider di risorse VM SQL se per la macchina virtuale SQL Server è già installata l'estensione SQL IaaS?**

Se la macchina virtuale SQL Server è installata autonomamente, senza provisioning dalle immagini SQL in Azure Marketplace, è necessario registrarsi con il provider di risorse VM SQL anche se è stata installata l'estensione SQL IaaS. La registrazione con il provider di risorse VM SQL crea una nuova risorsa di tipo Microsoft. SqlVirtualMachines. L'installazione dell'estensione SQL IaaS non comporta la creazione di tale risorsa.

**Qual è la modalità di gestione SQL predefinita durante la registrazione con il provider di risorse VM SQL?**

La modalità di gestione SQL predefinita quando si esegue la registrazione con la macchina virtuale SQL RP è _piena_. Se la proprietà di gestione SQL non è impostata quando si esegue la registrazione con il provider di risorse VM SQL, la modalità verrà impostata come piena gestibilità. L'installazione di SQL IaaS Extension nella VM è il prerequisito per la registrazione con il provider di risorse VM SQL in modalità di gestione completa.

**Quali sono i prerequisiti per la registrazione con il provider di risorse VM SQL?**

Non sono previsti prerequisiti per la registrazione con il provider di risorse VM SQL in modalità lightweight o senza agente. I prerequisiti per la registrazione con il provider di risorse VM SQL in modalità completa hanno l'estensione SQL IaaS installata nella macchina virtuale.

**È possibile eseguire la registrazione con il provider resouce di macchine virtuali SQL se non è installata l'estensione SQL IaaS nella macchina virtuale?**

Sì, è possibile eseguire la registrazione con il provider di risorse VM SQL in modalità di gestione Lightweight se non è stata installata l'estensione SQL IaaS nella macchina virtuale. In modalità Lightweight, il provider resouce per macchine virtuali SQL userà un'app console per verificare la versione e l'edizione dell'istanza di SQL. L'app console si arresterà automaticamente dopo aver verificato la presenza di almeno un'istanza di SQL in esecuzione nella macchina virtuale. La registrazione con il provider di risorse VM SQL in modalità Lightweight non viene riavviata SQL Server e non creerà un agente nella macchina virtuale.

**Si registrerà con il provider di risorse VM SQL installare un agente nella macchina virtuale?**

No. La registrazione con il provider di risorse VM SQL creerà solo una nuova risorsa di metadati e non installerà un agente nella macchina virtuale. L'estensione SQL IaaS è necessaria solo per abilitare la gestibilità completa, quindi l'aggiornamento della modalità di gestibilità da Lightweight a full installa l'estensione SQL IaaS e riavvia SQL Server.

**La registrazione con il provider di risorse VM SQL riavvierà SQL Server nella macchina virtuale?**

No. La registrazione con il provider di risorse VM SQL creerà solo una nuova risorsa di metadati e non riavvierà SQL Server sulla macchina virtuale. Il riavvio di SQL Server è necessario solo per l'installazione dell'estensione IaaS SQL; per abilitare la gestibilità completa, è necessaria l'estensione SQL IaaS. Se si aggiorna la modalità di gestibilità da Lightweight a completo, si installerà l'estensione SQL IaaS e si riavvierà SQL Server.

**Qual è la differenza tra modalità di gestione Lightweight e senza agente durante la registrazione con il provider di risorse VM SQL?** 

La modalità di gestione senza agenti è disponibile solo per SQL Server 2008/R2 in Windows Server 2008; ed è l'unica modalità di gestione disponibile per queste versioni. Per tutte le altre versioni di SQL Server, le due modalità di gestione disponibili sono leggero e pieno. Per la modalità No Agent sono richieste SQL Server proprietà Version ed Edition impostate dal cliente. la modalità Lightweight esegue una query sulla macchina virtuale per trovare la versione e l'edizione dell'istanza di SQL.

**È possibile eseguire la registrazione con il provider di risorse VM SQL in modalità lightweight o senza agente con l'interfaccia della riga di comando di Azure?**

No. La proprietà modalità di gestione SQL è disponibile solo quando si esegue la registrazione con il provider di risorse VM SQL con Azure PowerShell. L'interfaccia della riga di comando di Azure non supporta l'impostazione della proprietà di gestibilità SQL e viene sempre registrata con il provider di risorse VM SQL nella modalità predefinita di gestione completa.

**È possibile eseguire la registrazione con il provider di risorse VM SQL senza specificare il tipo di licenza SQL?**

No. Il tipo di licenza SQL non è una proprietà facoltativa per la registrazione con la macchina virtuale SQL RP. È necessario impostare il tipo di licenza SQL come PAYG o vantaggio Ahu quando si esegue la registrazione con il provider di risorse VM SQL in tutte le modalità di gestibilità (senza agente, leggero e completo) con AZ CLI e PowerShell.

**È possibile aggiornare l'estensione SQL IaaS dalla modalità senza agente alla modalità completa?**

No. L'aggiornamento della modalità di gestione di SQL a full o Lightweight non è disponibile per la modalità senza agente. Si tratta di una limitazione tecnica di Windows Server 2008.

**È possibile aggiornare l'estensione IaaS di SQL dalla modalità Lightweight alla modalità completa?**

Sì. L'aggiornamento della modalità di gestione di SQL da Lightweight a Full è supportato tramite PowerShell o portale di Azure; e richiede il riavvio SQL Server.

**È possibile effettuare il downgrade dell'estensione IaaS SQL dalla modalità completa alle modalità di gestione senza agenti o Lightweight?**

No. Il downgrade della modalità di gestione delle estensioni SQL IaaS non è supportato. Non è possibile effettuare il downgrade della modalità di gestione di SQL dalla modalità completa alla modalità lightweight o senza agente; e non è possibile effettuare il downgrade dalla modalità Lightweight alla modalità senza agente. Per modificare la modalità di gestibilità dalla gestione completa; rimuovere l'estensione IaaS SQL; eliminare la risorsa tratti. SqlVirtualMachine e ripetere la registrazione SQL Server VM con il provider di risorse della macchina virtuale SQL.

**È possibile eseguire la registrazione con il provider di risorse VM SQL da portale di Azure?**

No. La registrazione con il provider di risorse VM SQL non è disponibile nel portale di Azure. La registrazione con il provider di risorse VM SQL in modalità di gestibilità completa è supportata con l'interfaccia della riga di comando di Azure o PowerShell. la registrazione con il provider di risorse VM SQL in modalità di gestibilità Lightweight o senza agenti è supportata solo dalle API Azure PowerShell.

**È possibile registrare una macchina virtuale con il provider di risorse VM SQL prima di installare SQL Server?**

No. Per eseguire correttamente la registrazione con il provider di risorse VM SQL, la macchina virtuale deve avere almeno un'istanza di SQL. Se non è presente alcuna istanza di SQL nella macchina virtuale, la nuova risorsa Microsoft. SqlVirtualMachine sarà in stato di errore.

**È possibile registrare una macchina virtuale con una risorsa VM SQL se sono presenti più istanze di SQL?**

Sì. Il provider di risorse VM SQL registrerà una sola istanza di SQL. Il provider di risorse VM SQL registrerà l'istanza SQL predefinita nel caso di più istanze. Se non è presente alcuna istanza predefinita, è supportata solo la registrazione in modalità lightweight. Per eseguire l'aggiornamento dalla modalità Lightweight alla gestione completa, è necessario che sia presente l'istanza SQL predefinita oppure che la macchina virtuale disponga di una sola istanza di SQL denominata.

**È possibile registrare un'istanza del cluster di failover di SQL Server con il provider di risorse VM SQL?**

Sì. Le istanze del cluster di failover SQL in una macchina virtuale di Azure possono essere registrate con il provider di risorse VM SQL in modalità lightweight. Tuttavia, non è possibile aggiornare le istanze di FCI SQL alla modalità di gestione completa.

**È possibile registrare la VM con SQL VM RP se è configurato un gruppo di disponibilità Always ON?**

Sì. Non sono previste restrizioni per la registrazione di un'istanza di SQL Server in una macchina virtuale di Azure con il provider di risorse VM SQL se si partecipa a una configurazione del gruppo di disponibilità Always ON.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
