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
ms.openlocfilehash: a0e5076f6ecb102b239a94b986830235eb720125
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512370"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrare una macchina virtuale SQL Server in Azure con il provider di risorse VM SQL

Questo articolo descrive come registrare il SQL Server macchina virtuale (VM) in Azure con il provider di risorse VM SQL. 

La distribuzione di un'immagine SQL Server VM di Azure Marketplace tramite il portale di Azure registra automaticamente la macchina virtuale SQL Server con il provider di risorse. Se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure anziché scegliere un'immagine da Azure Marketplace o se si esegue il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato con SQL Server, è necessario registrare la VM SQL Server con il provider di risorse per :

- **Semplificare la gestione delle licenze**: in base alle condizioni del prodotto Microsoft, i clienti devono comunicare a Microsoft quando usano la [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). La registrazione con il provider di risorse VM SQL semplifica la gestione delle licenze SQL Server e consente di identificare rapidamente SQL Server VM usando le Vantaggio Azure Hybrid nel [portale](virtual-machines-windows-sql-manage-portal.md) o AZ CLI: 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **Vantaggi della funzionalità**: la registrazione della macchina virtuale di SQL Server con il provider di risorse Sblocca l'applicazione [automatica di patch](virtual-machines-windows-sql-automated-patching.md), il [backup automatizzato](virtual-machines-windows-sql-automated-backup-v2.md)e le funzionalità di monitoraggio e gestibilità. Sblocca inoltre la flessibilità di [licenze](virtual-machines-windows-sql-ahb.md) ed [edizioni](virtual-machines-windows-sql-change-edition.md) . In precedenza, queste funzionalità erano disponibili solo per SQL Server immagini di VM di Azure Marketplace.

- **Gestione gratuita**: la registrazione con il provider di risorse VM SQL e tutte le modalità di gestione sono completamente gratuite. Non sono previsti costi aggiuntivi per il provider di risorse o con modalità di gestione mutevoli. 

Per usare il provider di risorse VM SQL, è necessario registrare anche il provider di risorse VM SQL con la sottoscrizione. A tale scopo, è possibile usare l'portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

  > [!NOTE]
  > Non sono previsti requisiti di licenza aggiuntivi associati alla registrazione con il provider di risorse. La registrazione con il provider di risorse VM SQL offre un metodo semplificato per soddisfare i requisiti di notifica a Microsoft che la Vantaggio Azure Hybrid è stata abilitata al posto della gestione dei moduli di registrazione delle licenze per ogni risorsa. 

Per ulteriori informazioni sui vantaggi derivanti dall'utilizzo del provider di risorse VM SQL, vedere il video [Channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) seguente: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Vantaggi del provider di risorse VM SQL durante l'installazione automatica di SQL Server in Azure-video Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- La versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Eseguire la registrazione con il provider di risorse VM SQL
Se l' [estensione dell'agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md) non è installata nella macchina virtuale, è possibile eseguire la registrazione con il provider di risorse VM SQL specificando la modalità di gestione SQL Lightweight. 

Quando viene specificato Lightweight durante il processo di registrazione, il provider di risorse VM SQL installerà automaticamente l'estensione IaaS di SQL in [modalità Lightweight](#change-management-modes) e verificherà i metadati dell'istanza di SQL Server; il servizio SQL Server non verrà riavviato. È necessario specificare il tipo di licenza SQL Server desiderata per la registrazione con il provider di risorse VM SQL come ' PAYG ' o ' vantaggio Ahu '.

La registrazione con il provider di risorse VM SQL in modalità Lightweight garantisce la conformità e Abilita le licenze flessibili, nonché gli aggiornamenti sul posto SQL Server edizione. Le istanze del cluster di failover e le distribuzioni a istanze diverse possono essere registrate con il provider di risorse VM SQL solo in modalità lightweight. È possibile [eseguire l'aggiornamento](#change-management-modes) alla modalità di gestione completa in qualsiasi momento, ma in questo modo il servizio SQL Server viene riavviato. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Usare il frammento di codice seguente per eseguire la registrazione con il provider di risorse VM SQL se l'estensione SQL Server IaaS è già installata nella macchina virtuale. Per la registrazione con il provider di risorse VM SQL è necessario specificare il tipo di licenza di SQL Server, ovvero con pagamento in base al consumo (`PAYG`) o Vantaggio Azure Hybrid (`AHUB`). 

Registrare la macchina virtuale SQL Server usando il frammento di codice di PowerShell seguente:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Per le edizioni a pagamento (Enterprise o standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

Per le edizioni gratuite (Developer, Web o Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Se l'estensione SQL IaaS è stata installata manualmente nella macchina virtuale, è possibile eseguire la registrazione con il provider di risorse VM SQL in modalità completa semplicemente creando una risorsa di metadati di tipo Microsoft. SqlVirtualMachine/SqlVirtualMachines. Di seguito è riportato il frammento di codice da registrare con il provider di risorse VM SQL se l'estensione IaaS SQL è già installata nella macchina virtuale. È necessario specificare il tipo di licenza SQL Server desiderata come ' PAYG ' o ' vantaggio Ahu '. Per eseguire la registrazione in modalità di gestione completa, usare il comando di PowerShell seguente:

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registrare SQL Server 2008 o 2008 R2 in macchine virtuali Windows Server 2008

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 possono essere registrati con il provider di risorse VM SQL in [modalità senza agenti](#change-management-modes). Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.

La tabella seguente illustra in dettaglio i valori accettabili per i parametri forniti durante la registrazione:

| Parametro | Valori accettabili                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` oppure `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` oppure `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Per registrare l'istanza di SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, usare il frammento di codice PowerShell o AZ CLI seguente:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Verificare lo stato della registrazione
È possibile verificare se la macchina virtuale SQL Server è già stata registrata con il provider di risorse VM SQL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selezionare il SQL Server VM dall'elenco. Se la macchina virtuale SQL Server non è inclusa nell'elenco, è probabile che non sia stata registrata con il provider di risorse della macchina virtuale SQL. 
1. Visualizzare il valore in **stato**. Se **lo stato** è **succeeded**, la macchina virtuale SQL Server è stata registrata correttamente con il provider di risorse della macchina virtuale SQL. 

![Verificare lo stato con la registrazione di SQL RP](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Riga di comando

Verificare lo stato di registrazione della macchina virtuale SQL Server corrente usando AZ CLI o PowerShell. `ProvisioningState` visualizzerà `Succeeded` se la registrazione ha avuto esito positivo. 

# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Un errore indica che la macchina virtuale SQL Server non è stata registrata con il provider di risorse. 

## <a name="change-management-modes"></a>Modalità di gestione delle modifiche

Sono disponibili tre modalità di gestibilità gratuite per la SQL Server estensione IaaS: 

- La modalità **completa** offre tutte le funzionalità, ma richiede il riavvio delle autorizzazioni di SQL Server e di amministratore di sistema. Questa è l'opzione che viene installata per impostazione predefinita. Usarlo per la gestione di una macchina virtuale SQL Server con una singola istanza. La modalità completa installa due servizi Windows che hanno un effetto minimo sulla memoria e sulla CPU, che possono essere monitorati tramite Gestione attività. Non è previsto alcun costo associato all'utilizzo della modalità di gestione completa. 

- **Lightweight** non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server. Usare questa opzione per SQL Server VM con più istanze o per partecipare a un'istanza del cluster di failover di. Non vi sono effetti sulla memoria o sulla CPU quando si usa la modalità lightweight. Non è previsto alcun costo associato all'utilizzo della modalità di gestibilità semplificata. 

- **Noagent** è dedicato a SQL Server 2008 e SQL Server 2008 R2 installato in Windows Server 2008. Non vi è alcun effetto sulla memoria o sulla CPU quando si usa la modalità noagent. Non è previsto alcun costo associato all'utilizzo della modalità di gestibilità di noagent. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server le macchine virtuali con l'estensione IaaS *Lightweight* installata possono aggiornare la modalità a _full_ usando il portale di Azure. SQL Server le macchine virtuali in modalità _senza agente_ possono eseguire l'aggiornamento alla _versione completa_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile effettuare il downgrade. a tale scopo, sarà necessario eliminare la risorsa del provider di risorse VM SQL usando il portale di Azure e registrarsi nuovamente con il provider di risorse VM SQL. 

Per aggiornare la modalità agente a Full: 


### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa [macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selezionare la macchina virtuale SQL Server e scegliere **Panoramica**. 
1. Per SQL Server VM con la modalità noagent o Lightweight IaaS, selezionare gli **unici tipi di licenza e gli aggiornamenti dell'edizione sono disponibili con il messaggio di estensione SQL IaaS** .

   ![Selezioni per modificare la modalità dal portale](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selezionare la casella di controllo **Accetto di riavviare il servizio SQL Server nella macchina virtuale** e quindi selezionare **conferma** per aggiornare la modalità IaaS a completa. 

    ![Casella di controllo per accettare il riavvio del servizio SQL Server nella macchina virtuale](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Riga di comando

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

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Registrare il provider di risorse VM SQL con una sottoscrizione 

Per registrare la macchina virtuale di SQL Server con il provider di risorse VM SQL, è necessario registrare il provider di risorse con la sottoscrizione. A tale scopo, è possibile usare la portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Aprire il portale di Azure e passare a **tutti i servizi**. 
1. Passare a **sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nella pagina **sottoscrizioni** passare a **provider di risorse**. 
1. Immettere **SQL** nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare **registra**, **Esegui di nuovo**la registrazione o Annulla la **registrazione** per il provider **Microsoft. SqlVirtualMachine** , a seconda dell'azione desiderata. 

![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Riga di comando

Registrare il provider di risorse VM SQL nella sottoscrizione di Azure usando AZ CLI o PowerShell. 

# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)
Il frammento di codice seguente registrerà il provider di risorse VM SQL nella sottoscrizione di Azure. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Osservazioni

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

La modalità di gestione SQL predefinita durante la registrazione con il provider di risorse VM SQL è _piena_. Se la proprietà di gestione SQL non è impostata quando si esegue la registrazione con il provider di risorse VM SQL, la modalità verrà impostata come piena gestibilità. L'installazione di SQL IaaS Extension nella VM è il prerequisito per la registrazione con il provider di risorse VM SQL in modalità di gestione completa.

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

Per modificare la modalità di gestibilità dalla gestione completa, eliminare la risorsa Microsoft. SqlVirtualMachine e registrare di nuovo la macchina virtuale di SQL Server con il provider di risorse della macchina virtuale SQL.

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

**Qual è il costo per la registrazione con il provider di risorse VM SQL o con l'aggiornamento alla modalità di gestibilità completa?**
None. Non sono previste tariffe associate alla registrazione con il provider di risorse VM SQL o con una delle tre modalità di gestibilità. La gestione della macchina virtuale SQL Server con il provider di risorse è completamente gratuita. 

**Qual è l'effetto sulle prestazioni dell'utilizzo delle diverse modalità di gestibilità?**
Non vi è alcun effetto quando si utilizzano le modalità *noagent* e la gestibilità *leggera* . Quando si usa la modalità di gestibilità *completa* di due servizi installati nel sistema operativo, l'effetto è minimo. Questi possono essere monitorati tramite Gestione attività. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
