---
title: Eseguire la registrazione con il provider di risorse VM SQL
description: Registrare la macchina virtuale di Azure SQL Server con il provider di risorse VM SQL per abilitare le funzionalità per SQL Server macchine virtuali distribuite all'esterno di Azure Marketplace, nonché per la conformità e una migliore gestibilità.
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6b2430b5135a5d3f7ad1f9ef0bd17d9149bf48ee
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793463"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrare una macchina virtuale SQL Server in Azure con il provider di risorse VM SQL

Questo articolo descrive come registrare il SQL Server macchina virtuale (VM) in Azure con il provider di risorse VM SQL. La registrazione con il provider di risorse crea la _risorsa_ della **macchina virtuale SQL** nella sottoscrizione, che è una risorsa separata dalla risorsa della macchina virtuale. Se si annulla la registrazione della VM SQL Server dal provider di risorse, la _risorsa_ della **macchina virtuale SQL** viene rimossa, ma la macchina virtuale non viene eliminata. 

La distribuzione di un'immagine SQL Server VM di Azure Marketplace tramite il portale di Azure registra automaticamente la macchina virtuale SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure o di effettuare il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la VM SQL Server con il provider di risorse per:

- **Vantaggi della funzionalità**: la registrazione della macchina virtuale SQL Server con il provider di risorse Sblocca l'applicazione [automatica di patch](virtual-machines-windows-sql-automated-patching.md), il [backup automatizzato](virtual-machines-windows-sql-automated-backup-v2.md), nonché le funzionalità di monitoraggio e gestione. Sblocca inoltre la flessibilità di [licenze](virtual-machines-windows-sql-ahb.md) ed [edizioni](virtual-machines-windows-sql-change-edition.md) . In precedenza, queste funzionalità erano disponibili solo per SQL Server immagini di VM distribuite da Azure Marketplace. 

- **Conformità**: la registrazione con il provider di risorse VM SQL offre un metodo semplificato per soddisfare la necessità di notificare a Microsoft che la vantaggio Azure Hybrid è stata abilitata come indicato nelle condizioni del prodotto. Questo processo nega la necessità di gestire i moduli di registrazione delle licenze per ogni risorsa.  

- **Gestione gratuita**: la registrazione con il provider di risorse VM SQL in tutte e tre le modalità di gestione è completamente gratuita. Non sono previsti costi aggiuntivi per il provider di risorse o con modalità di gestione mutevoli. 

- **Gestione semplificata delle licenze**: la registrazione con il provider di risorse VM SQL semplifica la gestione delle licenze SQL Server e consente di identificare rapidamente SQL Server VM con la vantaggio Azure Hybrid abilitata usando il [portale di Azure](virtual-machines-windows-sql-manage-portal.md), AZ CLI o PowerShell: 

   # <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Per usare il provider di risorse VM SQL, è necessario prima [registrare la sottoscrizione con il provider di risorse](#register-subscription-with-rp), che consente al provider di risorse di creare risorse all'interno della sottoscrizione specifica.

Per ulteriori informazioni sui vantaggi derivanti dall'utilizzo del provider di risorse VM SQL, guardare questo video [Channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) : 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Vantaggi del provider di risorse VM SQL durante l'installazione automatica di SQL Server in Azure-video Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Un modello di risorse di Azure [SQL Server macchina virtuale](virtual-machines-windows-portal-sql-server-provision.md) distribuita nel cloud pubblico. 
- La versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modalità di gestione

Se l' [estensione SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) non è ancora stata installata, la registrazione con il provider di risorse VM SQL installa automaticamente l'estensione SQL Server IaaS in una delle tre modalità di gestione specificate durante il processo di registrazione. Se non si specifica la modalità di gestione, l'estensione IaaS SQL viene installata in modalità di gestione completa.  

Se l'estensione SQL IaaS è già stata installata manualmente, è già in modalità di gestione completa e la registrazione con il provider di risorse in modalità completa non riavvierà il servizio SQL Server.

Le tre modalità di gestione sono:

- La modalità **Lightweight** non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server. Usare questa opzione per SQL Server VM con più istanze o per partecipare a un'istanza del cluster di failover (FCI). Non vi è alcun impatto sulla memoria o sulla CPU quando si usa la modalità lightweight e non vi sono costi associati. È consigliabile registrare prima la macchina virtuale SQL Server in modalità lightweight e quindi eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione pianificata.  

- La modalità **completa** offre tutte le funzionalità, ma richiede il riavvio delle autorizzazioni di SQL Server e di amministratore di sistema. Questa è l'opzione che viene installata per impostazione predefinita quando si installa manualmente l'estensione SQL IaaS. Usarlo per la gestione di una macchina virtuale SQL Server con una singola istanza. La modalità completa installa due servizi Windows che hanno un effetto minimo sulla memoria e sulla CPU, che possono essere monitorati tramite Gestione attività. Non è previsto alcun costo associato all'utilizzo della modalità di gestione completa. 

- La modalità **noagent** è dedicata a SQL Server 2008 e SQL Server 2008 R2 installato in Windows Server 2008. Non vi è alcun effetto sulla memoria o sulla CPU quando si usa la modalità noagent. Non è previsto alcun costo associato all'utilizzo della modalità di gestibilità di noagent. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrare la sottoscrizione con RP

Per registrare la macchina virtuale di SQL Server con il provider di risorse VM SQL, è necessario prima registrare la sottoscrizione con il provider di risorse. In questo modo, il provider di risorse VM SQL è in grado di creare risorse all'interno della sottoscrizione.  A tale scopo, è possibile usare la portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

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

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Registrare una macchina virtuale SQL con RP 

### <a name="lightweight-management-mode"></a>Modalità di gestione semplice

Se la [SQL Server estensione agente IaaS](virtual-machines-windows-sql-server-agent-extension.md) non è stata installata nella macchina virtuale, si consiglia di eseguire la registrazione con il provider di risorse VM SQL in modalità lightweight. Questa operazione installerà l'estensione SQL IaaS in [modalità Lightweight](#management-modes) e impedirà il riavvio del servizio SQL Server. È quindi possibile eseguire l'aggiornamento alla modalità completa in qualsiasi momento, ma in questo modo verrà riavviato il servizio SQL Server, quindi è consigliabile attendere fino a una finestra di manutenzione pianificata. È necessario fornire il tipo di licenza di SQL Server come pagamento in base al consumo (`PAYG`) per il pagamento in base all'utilizzo oppure Vantaggio Azure Hybrid (`AHUB`) per usare la propria licenza.

Le istanze del cluster di failover e le distribuzioni a istanze diverse possono essere registrate solo con il provider di risorse VM SQL in modalità lightweight. 

# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Registrare SQL Server VM in modalità Lightweight con AZ CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Registrare SQL Server VM in modalità Lightweight con PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modalità di gestione completa


Se l'estensione SQL IaaS è già stata installata manualmente nella macchina virtuale, è possibile registrare la macchina virtuale SQL Server in modalità completa senza riavviare il servizio SQL Server. **Tuttavia, se l'estensione SQL IaaS non è stata installata, la registrazione in modalità completa installerà l'estensione SQL IaaS in modalità completa e riavvierà il servizio SQL Server. Procedere con cautela.**

Di seguito è riportato il frammento di codice da registrare con il provider di risorse VM SQL in modalità completa. Per eseguire la registrazione in modalità di gestione completa, usare il comando di PowerShell seguente:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```


### <a name="noagent-management-mode"></a>Modalità di gestione noagent 

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 possono essere registrati con il provider di risorse VM SQL in [modalità noagent](#management-modes). Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.

Specificare `AHUB` o `PAYG` come **sqlLicenseType**e `SQL2008-WS2008` o `SQL2008R2-WS2008` come **sqlImageOffer**. 

Per registrare l'istanza di SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, usare il seguente frammento di codice AZ CLI o PowerShell: 


# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Registrare SQL Server VM in modalità noagent con AZ CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Registrare SQL Server VM in modalità noagent con PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Eseguire l'aggiornamento alla modalità di gestione completa 

SQL Server le macchine virtuali con l'estensione IaaS *Lightweight* installata possono aggiornare la modalità _completa_ usando il portale di Azure, AZ CLI o PowerShell. SQL Server le macchine virtuali in modalità _noagent_ possono eseguire l'aggiornamento alla _versione completa_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile effettuare il downgrade. a tale scopo, sarà necessario [annullare la registrazione](#unregister-vm-from-rp) della macchina virtuale SQL Server dal provider di risorse VM SQL. In questo modo, la _risorsa_della **macchina virtuale SQL** viene rimossa, ma non viene eliminata la macchina virtuale effettiva. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

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
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

  # Update to full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
     -LicenseType PAYG -SqlManagementType Full
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


## <a name="unregister-vm-from-rp"></a>Annulla la registrazione della macchina virtuale da RP

Per annullare la registrazione della VM SQL Server con il provider di risorse VM SQL, eliminare la *risorsa* macchina virtuale SQL usando l'interfaccia della riga di comando di portale di Azure o Azure. L'eliminazione della *risorsa* della macchina virtuale SQL non comporta l'eliminazione della VM SQL Server. Tuttavia, prestare attenzione e seguire attentamente i passaggi perché è possibile eliminare inavvertitamente la macchina virtuale quando si tenta di rimuovere la *risorsa*. 

L'annullamento della registrazione della macchina virtuale SQL con il provider di risorse VM SQL è necessario per eseguire il downgrade della modalità di gestione da piena. 

### <a name="azure-portal"></a>Portale di Azure

Per annullare la registrazione della macchina virtuale SQL Server con il provider di risorse usando il portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa SQL Server VM. 
  
   ![Risorsa macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selezionare **Elimina**. 

   ![Elimina provider di risorse VM SQL](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Digitare il nome della macchina virtuale SQL e **deselezionare la casella di controllo accanto alla macchina virtuale**.

   ![Elimina provider di risorse VM SQL](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se non si deseleziona la casella di controllo accanto al nome della macchina virtuale, la macchina virtuale verrà *eliminata* completamente. Deselezionare la casella di controllo per annullare la registrazione della VM SQL Server dal provider di risorse, ma *non eliminare la macchina virtuale effettiva*. 

1. Selezionare **Elimina** per confermare l'eliminazione della *risorsa*della macchina virtuale SQL e non della macchina virtuale SQL Server. 

### <a name="command-line"></a>Riga di comando

# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)
Per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse con l'interfaccia della riga di comando di Azure, usare il comando [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Verrà rimossa la *risorsa* della macchina virtuale SQL Server ma la macchina virtuale non verrà eliminata. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse con l'interfaccia della riga di comando di Azure, usare il comando [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm). Verrà rimossa la *risorsa* della macchina virtuale SQL Server ma la macchina virtuale non verrà eliminata. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitazioni

Il provider di risorse VM SQL supporta solo:
- SQL Server macchine virtuali distribuite tramite l'Azure Resource Manager. SQL Server macchine virtuali distribuite con il modello classico non sono supportate. 
- SQL Server macchine virtuali distribuite nel cloud pubblico. Le distribuzioni nel cloud privato o per enti pubblici non sono supportate. 


## <a name="frequently-asked-questions"></a>Domande frequenti 

**È necessario registrare la macchina virtuale SQL Server sottoposta a provisioning da un'immagine di SQL Server in Azure Marketplace?**

No. Microsoft registra automaticamente le VM di cui è stato effettuato il provisioning dalle immagini SQL Server in Azure Marketplace. La registrazione con il provider di risorse VM SQL è necessaria solo se *non* è stato effettuato il provisioning della macchina virtuale dalla SQL Server immagini in Azure Marketplace e SQL Server è stato installato autonomamente.

**Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?** 

Sì. I clienti devono registrare le proprie macchine virtuali SQL Server con il provider di risorse VM SQL se non usano un'immagine di SQL Server da Azure Marketplace e invece SQL Server autoinstallate oppure se hanno portato il disco rigido virtuale personalizzato. Le macchine virtuali di proprietà di tutti i tipi di sottoscrizioni (Direct, Enterprise Agreement e Cloud Solution Provider) possono eseguire la registrazione con il provider di risorse della macchina virtuale SQL.

**È necessario eseguire la registrazione con il provider di risorse VM SQL se per la macchina virtuale SQL Server è già installata l'estensione SQL Server IaaS?**

Se la macchina virtuale SQL Server è installata e non viene sottoposta a provisioning dalle immagini SQL Server in Azure Marketplace, è necessario registrarsi con il provider di risorse VM SQL anche se è stata installata l'estensione SQL Server IaaS. La registrazione con il provider di risorse VM SQL crea una nuova risorsa di tipo Microsoft. SqlVirtualMachines. L'installazione dell'estensione SQL Server IaaS non crea tale risorsa.

**Qual è la modalità di gestione predefinita durante la registrazione con il provider di risorse VM SQL?**

La modalità di gestione predefinita quando si esegue la registrazione con il provider di risorse VM SQL è *piena*. Se la proprietà gestione SQL Server non è impostata quando si esegue la registrazione con il provider di risorse VM SQL, la modalità verrà impostata come piena gestibilità e il servizio di SQL Server verrà riavviato. Si consiglia di eseguire prima la registrazione con il provider di risorse VM SQL in modalità Lightweight, quindi eseguire l'aggiornamento a completo durante una finestra di manutenzione. 

**Quali sono i prerequisiti per la registrazione con il provider di risorse VM SQL?**

Non sono previsti prerequisiti per la registrazione con il provider di risorse VM SQL in modalità lightweight o senza agente. Il prerequisito per la registrazione con il provider di risorse VM SQL in modalità completa prevede l'installazione dell'estensione SQL Server IaaS nella macchina virtuale, in caso contrario verrà riavviato il servizio di SQL Server. 

**È possibile eseguire la registrazione con il provider di risorse VM SQL se non è installata la SQL Server estensione IaaS nella macchina virtuale?**

Sì, è possibile eseguire la registrazione con il provider di risorse VM SQL in modalità di gestione Lightweight se non è stata installata l'estensione SQL Server IaaS nella macchina virtuale. In modalità Lightweight, il provider di risorse VM SQL userà un'app console per verificare la versione e l'edizione dell'istanza di SQL Server. 

La modalità di gestione SQL predefinita durante la registrazione con il provider di risorse VM SQL è _piena_. Se la proprietà di gestione SQL non è impostata quando si esegue la registrazione con il provider di risorse VM SQL, la modalità verrà impostata come piena gestibilità. Si consiglia di eseguire prima la registrazione con il provider di risorse VM SQL in modalità Lightweight, quindi eseguire l'aggiornamento a completo durante una finestra di manutenzione. 

**Si registrerà con il provider di risorse VM SQL per installare un agente nella macchina virtuale?**

No. La registrazione con il provider di risorse VM SQL creerà solo una nuova risorsa di metadati. Non installerà un agente nella macchina virtuale.

L'estensione SQL Server IaaS è necessaria solo per consentire una gestione completa. Se si aggiorna la modalità di gestibilità da Lightweight a completo, si installerà l'estensione SQL Server IaaS e si riavvierà SQL Server.

**La registrazione con il provider di risorse SQL Server VM di riavvio SQL Server nella macchina virtuale?**

Dipende dalla modalità specificata durante la registrazione. Se si specifica la modalità lightweight o noagent, il servizio SQL Server non verrà riavviato. Tuttavia, se si specifica la modalità di gestione come completa o se si lascia vuota la modalità di gestione, verrà installata l'estensione IaaS di SQL in modalità di gestione completa che comporterà il riavvio del servizio SQL Server. 

**Qual è la differenza tra modalità di gestione Lightweight e senza agente durante la registrazione con il provider di risorse VM SQL?** 

La modalità di gestione dell'agente non è disponibile solo per SQL Server 2008 e SQL Server 2008 R2 in Windows Server 2008. Si tratta dell'unica modalità di gestione disponibile per queste versioni. Per tutte le altre versioni di SQL Server, le due modalità di gestione disponibili sono leggero e pieno. 

Per la modalità No Agent sono richieste SQL Server proprietà Version ed Edition impostate dal cliente. La modalità Lightweight esegue una query sulla macchina virtuale per trovare la versione e l'edizione dell'istanza di SQL Server.

**È possibile eseguire la registrazione con il provider di risorse VM SQL senza specificare il tipo di licenza SQL Server?**

No. Il tipo di licenza SQL Server non è una proprietà facoltativa quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL. È necessario impostare il tipo di licenza SQL Server come Vantaggio Azure Hybrid con pagamento in base al consumo o quando si esegue la registrazione con il provider di risorse VM SQL in tutte le modalità di gestibilità (senza agente, leggero e completo).

**È possibile aggiornare l'estensione IaaS SQL Server dalla modalità senza agente alla modalità completa?**

No. L'aggiornamento della modalità di gestibilità a full o Lightweight non è disponibile per la modalità senza agente. Si tratta di una limitazione tecnica di Windows Server 2008. Sarà necessario aggiornare prima il sistema operativo a Windows Server 2008 R2 o versione successiva e quindi sarà possibile eseguire l'aggiornamento alla modalità di gestione completa. 

**È possibile aggiornare l'estensione IaaS SQL Server dalla modalità Lightweight alla modalità completa?**

Sì. L'aggiornamento della modalità di gestibilità da Lightweight a Full è supportato tramite PowerShell o il portale di Azure. Richiede il riavvio del servizio SQL Server.

**È possibile effettuare il downgrade della SQL Server estensione IaaS dalla modalità completa alla modalità di gestione senza agenti o Lightweight?**

No. Il downgrade del SQL Server modalità di gestione dell'estensione IaaS non è supportato. Non è possibile effettuare il downgrade della modalità di gestibilità dalla modalità completa alla modalità lightweight o senza agente e non è possibile effettuare il downgrade dalla modalità Lightweight alla modalità senza agente. 

Per modificare la modalità di gestibilità dalla gestione completa, [annullare la registrazione](#unregister-vm-from-rp) della macchina virtuale SQL Server dal provider di risorse SQL Server eliminando la *risorsa* SQL Server e registrare nuovamente la VM SQL Server con il provider di risorse VM SQL in una modalità di gestione diversa.

**È possibile eseguire la registrazione con il provider di risorse VM SQL dalla portale di Azure?**

No. La registrazione con il provider di risorse VM SQL non è disponibile nel portale di Azure. La registrazione con il provider di risorse VM SQL è supportata solo con l'interfaccia della riga di comando di Azure o PowerShell. 

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
Non vi è alcun effetto quando si utilizzano le modalità *noagent* e la gestibilità *leggera* . Quando si usa la modalità di gestibilità *completa* di due servizi installati nel sistema operativo, l'effetto è minimo. Questi possono essere monitorati tramite Gestione attività e visualizzati nella console dei servizi Windows incorporata. 

I due nomi di servizio sono:
- `SqlIaaSExtensionQuery` (nome visualizzato-`Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (nome visualizzato-`Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)
