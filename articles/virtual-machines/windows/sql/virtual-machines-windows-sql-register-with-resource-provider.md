---
title: Registrare una macchina virtuale SQL Server in Azure con il provider di risorse VM SQL | Microsoft Docs
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
ms.openlocfilehash: 87db36936ee4aee45b7e8d83e1512d22c2a49eee
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846145"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrare una macchina virtuale SQL Server in Azure con il provider di risorse VM SQL

Questo articolo descrive come registrare il SQL Server macchina virtuale (VM) in Azure con il provider di risorse VM SQL. 

La distribuzione di un'immagine SQL Server VM di Azure Marketplace tramite il portale di Azure registra automaticamente la macchina virtuale SQL Server con il provider di risorse. Se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure anziché scegliere un'immagine da Azure Marketplace o se si esegue il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato con SQL Server, è necessario registrare la VM SQL Server con il provider di risorse per :

- **Conformità**: Secondo le condizioni del prodotto Microsoft, i clienti devono comunicare a Microsoft quando usano il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). A tale scopo, è necessario registrarsi con il provider di risorse della macchina virtuale SQL. 

- **Vantaggi delle funzionalità**: La registrazione della macchina virtuale di SQL Server con il provider di risorse Sblocca l'applicazione [automatica di patch](virtual-machines-windows-sql-automated-patching.md), il [backup automatizzato](virtual-machines-windows-sql-automated-backup-v2.md)e le funzionalità di monitoraggio e gestibilità. Sblocca inoltre la flessibilità di [licenze](virtual-machines-windows-sql-ahb.md) ed [edizioni](virtual-machines-windows-sql-change-edition.md) . In precedenza, queste funzionalità erano disponibili solo per SQL Server immagini di VM di Azure Marketplace.

Per usare il provider di risorse VM SQL, è necessario registrare anche il provider di risorse VM SQL con la sottoscrizione. A tale scopo, è possibile usare l'portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- L' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure e [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-the-sql-vm-resource-provider"></a>Eseguire la registrazione con il provider di risorse VM SQL
Se la [SQL Server estensione agente IaaS](virtual-machines-windows-sql-server-agent-extension.md) è già installata nella macchina virtuale, la registrazione con il provider di risorse VM SQL crea semplicemente una risorsa di metadati di tipo Microsoft. SqlVirtualMachine/SqlVirtualMachines. 

Usare il frammento di codice seguente per eseguire la registrazione con il provider di risorse VM SQL se l'estensione SQL Server IaaS è già installata nella macchina virtuale. Per la registrazione con il provider di risorse VM SQL è necessario specificare il tipo di licenza di SQL Server, ovvero con pagamento in base al consumo (`PAYG`) o vantaggio Azure Hybrid (`AHUB`). 

Registrare la macchina virtuale SQL Server usando il frammento di codice di PowerShell seguente:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with the SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Se la SQL Server estensione IaaS non è installata nella macchina virtuale, è possibile eseguire la registrazione con il provider di risorse VM SQL specificando la modalità di gestione leggera. In modalità di gestione Lightweight, il provider di risorse VM SQL installerà automaticamente l'estensione IaaS SQL Server in [modalità Lightweight](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) e verificherà SQL Server metadati dell'istanza. Il servizio SQL Server non verrà riavviato. Per la registrazione con il provider di risorse VM SQL è necessario specificare il tipo di licenza di SQL Server, ovvero con pagamento in base al consumo (`PAYG`) o vantaggio Azure Hybrid (`AHUB`). 

Registrare la macchina virtuale SQL Server in modalità di gestione semplice usando il frammento di codice di PowerShell seguente:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register the SQL VM with the lightweight SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

La registrazione con il provider di risorse VM SQL in [modalità Lightweight](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) garantisce la conformità e Abilita le licenze flessibili, nonché gli aggiornamenti sul posto SQL Server edizione. Le istanze del cluster di failover e le distribuzioni a più istanze possono essere registrate con il provider di risorse VM SQL solo in modalità lightweight. In qualsiasi momento, è possibile seguire le istruzioni disponibili nella portale di Azure per eseguire l'aggiornamento alla [modalità completa](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) e abilitare una funzionalità di gestibilità completa con un riavvio del SQL Server. 

## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registrare SQL Server 2008 o 2008 R2 in macchine virtuali Windows Server 2008

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 possono essere registrati con il provider di risorse VM SQL nella modalità ([Nessun agente](virtual-machines-windows-sql-server-agent-extension.md)). Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.

La tabella seguente illustra in dettaglio i valori accettabili per i parametri forniti durante la registrazione:

| Parametro | Valori accettabili                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` o `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` o `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Per registrare l'istanza di SQL Server 2008 o 2008 R2 in Windows Server 2008, usare il frammento di codice di PowerShell seguente:  

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Verificare lo stato della registrazione
È possibile verificare se la macchina virtuale SQL Server è già stata registrata con il provider di risorse VM SQL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selezionare il SQL Server VM dall'elenco. Se la macchina virtuale SQL Server non è inclusa nell'elenco, è probabile che non sia stata registrata con il provider di risorse della macchina virtuale SQL. 
1. Visualizzare il valore in **stato**. Se **lo stato** è **succeeded**, la macchina virtuale SQL Server è stata registrata correttamente con il provider di risorse della macchina virtuale SQL. 

    ![Verificare lo stato con la registrazione di SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```
`ProvisioningState`indicherà `Succeeded` se la registrazione è stata completata. 

### <a name="powershell"></a>PowerShell

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
`ProvisioningState`indicherà `Succeeded` se la registrazione è stata completata.

Un errore indica che la macchina virtuale SQL Server non è stata registrata con il provider di risorse. 

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Registrare il provider di risorse VM SQL con una sottoscrizione 

Per registrare la macchina virtuale di SQL Server con il provider di risorse VM SQL, è necessario registrare il provider di risorse con la sottoscrizione. A tale scopo, è possibile usare la portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Aprire il portale di Azure e passare a **tutti i servizi**. 
1. Passare a **sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nella pagina **sottoscrizioni** passare a **provider di risorse**. 
1. Immettere **SQL** nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare **registra**, **Esegui di nuovo**la registrazione o Annulla la **registrazione** per il provider **Microsoft. SqlVirtualMachine** , a seconda dell'azione desiderata. 

![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

## <a name="remarks"></a>Note

- Il provider di risorse VM SQL supporta solo SQL Server macchine virtuali distribuite tramite Azure Resource Manager. SQL Server macchine virtuali distribuite con il modello classico non sono supportate. 
- Il provider di risorse VM SQL supporta solo SQL Server macchine virtuali distribuite nel cloud pubblico. Le distribuzioni nel cloud privato o per enti pubblici non sono supportate. 
 
## <a name="frequently-asked-questions"></a>Domande frequenti 

**È necessario registrare la macchina virtuale SQL Server sottoposta a provisioning da un'immagine di SQL Server in Azure Marketplace?**

No. Microsoft registra automaticamente le VM di cui è stato effettuato il provisioning dalle immagini SQL Server in Azure Marketplace. La registrazione con il provider di risorse VM SQL è necessaria solo se *non* è stato effettuato il provisioning della macchina virtuale dalla SQL Server immagini in Azure Marketplace e SQL Server è stato installato autonomamente.

**Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?** 

Sì. I clienti devono registrare le proprie macchine virtuali SQL Server con il provider di risorse VM SQL se non usano un'immagine di SQL Server da Azure Marketplace e invece SQL Server autoinstallate oppure se hanno portato il disco rigido virtuale personalizzato. Le macchine virtuali di proprietà di tutti i tipi di sottoscrizioni (Direct, Enterprise Agreement e Cloud Solution Provider) possono eseguire la registrazione con il provider di risorse della macchina virtuale SQL.

**È necessario eseguire la registrazione con il provider di risorse VM SQL se per la macchina virtuale SQL Server è già installata l'estensione SQL Server IaaS?**

Se la macchina virtuale SQL Server è installata e non viene sottoposta a provisioning dalle immagini SQL Server in Azure Marketplace, è necessario registrarsi con il provider di risorse VM SQL anche se è stata installata l'estensione SQL Server IaaS. La registrazione con il provider di risorse VM SQL crea una nuova risorsa di tipo Microsoft. SqlVirtualMachines. L'installazione dell'estensione SQL Server IaaS non crea tale risorsa.

**Qual è la modalità di gestione predefinita durante la registrazione con il provider di risorse VM SQL?**

La modalità di gestione predefinita quando si esegue la registrazione con il provider di risorse VM SQL è *piena*. Se la proprietà di gestione SQL Server non è impostata quando si esegue la registrazione con il provider di risorse VM SQL, la modalità verrà impostata come piena gestibilità. L'installazione dell'estensione SQL Server IaaS nella macchina virtuale è il prerequisito per la registrazione con il provider di risorse VM SQL in modalità di gestione completa.

**Quali sono i prerequisiti per la registrazione con il provider di risorse VM SQL?**

Non sono previsti prerequisiti per la registrazione con il provider di risorse VM SQL in modalità lightweight o senza agente. Il prerequisito per la registrazione con il provider di risorse VM SQL in modalità completa è la SQL Server dell'estensione IaaS installata nella macchina virtuale.

**È possibile eseguire la registrazione con il provider di risorse VM SQL se non è installata la SQL Server estensione IaaS nella macchina virtuale?**

Sì, è possibile eseguire la registrazione con il provider di risorse VM SQL in modalità di gestione Lightweight se non è stata installata l'estensione SQL Server IaaS nella macchina virtuale. In modalità Lightweight, il provider di risorse VM SQL userà un'app console per verificare la versione e l'edizione dell'istanza di SQL Server. 

L'app console si arresterà automaticamente dopo aver verificato che sia presente almeno un'istanza di SQL Server in esecuzione nella macchina virtuale. La registrazione con il provider di risorse VM SQL in modalità Lightweight non viene riavviata SQL Server e non creerà un agente nella macchina virtuale.

**Si registrerà con il provider di risorse VM SQL per installare un agente nella macchina virtuale?**

No. La registrazione con il provider di risorse VM SQL creerà solo una nuova risorsa di metadati. Non installerà un agente nella macchina virtuale.

L'estensione SQL Server IaaS è necessaria solo per consentire una gestione completa. Se si aggiorna la modalità di gestibilità da Lightweight a completo, si installerà l'estensione SQL Server IaaS e si riavvierà SQL Server.

**La registrazione con il provider di risorse SQL Server VM di riavvio SQL Server nella macchina virtuale?**

No. La registrazione con il provider di risorse VM SQL creerà solo una nuova risorsa di metadati. Non verrà riavviato SQL Server nella macchina virtuale. 

Il riavvio di SQL Server è necessario solo per l'installazione dell'estensione IaaS SQL Server. Per abilitare la gestibilità completa, è necessaria l'estensione SQL Server IaaS. Se si aggiorna la modalità di gestibilità da Lightweight a completo, si installerà l'estensione SQL Server IaaS e si riavvierà SQL Server.

**Qual è la differenza tra modalità di gestione Lightweight e senza agente durante la registrazione con il provider di risorse VM SQL?** 

La modalità di gestione dell'agente non è disponibile solo per SQL Server 2008 e SQL Server 2008 R2 in Windows Server 2008. Si tratta dell'unica modalità di gestione disponibile per queste versioni. Per tutte le altre versioni di SQL Server, le due modalità di gestione disponibili sono leggero e pieno. 

Per la modalità No Agent sono richieste SQL Server proprietà Version ed Edition impostate dal cliente. La modalità Lightweight esegue una query sulla macchina virtuale per trovare la versione e l'edizione dell'istanza di SQL Server.

**È possibile eseguire la registrazione con il provider di risorse VM SQL in modalità lightweight o senza agente usando l'interfaccia della riga di comando di Azure?**

No. La proprietà modalità di gestione è disponibile solo quando si esegue la registrazione con il provider di risorse VM SQL utilizzando Azure PowerShell. L'interfaccia della riga di comando di Azure non supporta l'impostazione della proprietà SQL Server gestibilità. Viene sempre registrato con il provider di risorse VM SQL in modalità predefinita, piena gestibilità.

**È possibile eseguire la registrazione con il provider di risorse VM SQL senza specificare il tipo di licenza SQL Server?**

No. Il tipo di licenza SQL Server non è una proprietà facoltativa quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL. È necessario impostare il tipo di licenza SQL Server come Vantaggio Azure Hybrid con pagamento in base al consumo o quando si esegue la registrazione con il provider di risorse VM SQL in tutte le modalità di gestibilità (senza agente, leggero e completo) usando l'interfaccia della riga di comando di Azure e PowerShell.

**È possibile aggiornare l'estensione IaaS SQL Server dalla modalità senza agente alla modalità completa?**

No. L'aggiornamento della modalità di gestibilità a full o Lightweight non è disponibile per la modalità senza agente. Si tratta di una limitazione tecnica di Windows Server 2008.

**È possibile aggiornare l'estensione IaaS SQL Server dalla modalità Lightweight alla modalità completa?**

Sì. L'aggiornamento della modalità di gestibilità da Lightweight a Full è supportato tramite PowerShell o il portale di Azure. È necessario riavviare SQL Server.

**È possibile effettuare il downgrade della SQL Server estensione IaaS dalla modalità completa alla modalità di gestione senza agenti o Lightweight?**

No. Il downgrade del SQL Server modalità di gestione dell'estensione IaaS non è supportato. Non è possibile effettuare il downgrade della modalità di gestibilità dalla modalità completa alla modalità lightweight o senza agente e non è possibile effettuare il downgrade dalla modalità Lightweight alla modalità senza agente. 

Per modificare la modalità di gestibilità dalla gestione completa, rimuovere l'SQL Server estensione IaaS. Quindi, eliminare la risorsa tratti. SqlVirtualMachine e registrare di nuovo la macchina virtuale di SQL Server con il provider di risorse della macchina virtuale SQL.

**È possibile eseguire la registrazione con il provider di risorse VM SQL dalla portale di Azure?**

No. La registrazione con il provider di risorse VM SQL non è disponibile nel portale di Azure. La registrazione con il provider di risorse VM SQL in modalità di gestibilità completa è supportata con l'interfaccia della riga di comando di Azure o PowerShell. La registrazione con il provider di risorse VM SQL in modalità di gestibilità leggera o senza agente è supportata solo dalle API Azure PowerShell.

**È possibile registrare una macchina virtuale con il provider di risorse VM SQL prima di installare SQL Server?**

No. Per eseguire correttamente la registrazione con il provider di risorse VM SQL, una macchina virtuale deve disporre di almeno un'istanza di SQL Server. Se non è presente alcuna istanza di SQL Server nella macchina virtuale, la nuova risorsa Microsoft. SqlVirtualMachine sarà in stato di errore.

**È possibile registrare una macchina virtuale con il provider di risorse VM SQL se sono presenti più istanze di SQL Server?**

Sì. Il provider di risorse VM SQL registrerà una sola istanza di SQL Server. Il provider di risorse VM SQL registrerà l'istanza predefinita di SQL Server nel caso di più istanze. Se non è presente alcuna istanza predefinita, è supportata solo la registrazione in modalità lightweight. Per eseguire l'aggiornamento dalla modalità Lightweight alla gestione completa, è necessario che sia presente l'istanza predefinita di SQL Server o che la macchina virtuale disponga di una sola istanza denominata SQL Server.

**È possibile registrare un'istanza del cluster di failover di SQL Server con il provider di risorse VM SQL?**

Sì. SQL Server istanze del cluster di failover in una macchina virtuale di Azure possono essere registrate con il provider di risorse VM SQL in modalità lightweight. Tuttavia, SQL Server le istanze del cluster di failover non possono essere aggiornate alla modalità di gestione completa.

**È possibile registrare la macchina virtuale con il provider di risorse VM SQL se è configurato un gruppo di disponibilità Always On?**

Sì. Non sono previste restrizioni per la registrazione di un'istanza di SQL Server in una macchina virtuale di Azure con il provider di risorse VM SQL se si partecipa a una configurazione del gruppo di disponibilità Always On.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
