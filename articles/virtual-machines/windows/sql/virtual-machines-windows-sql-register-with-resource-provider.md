---
title: Eseguire la registrazione con il provider di risorse della macchina virtuale SQLRegister with the SQL VM resource provider
description: Registrare la macchina virtuale di Azure SQL Server con il provider di risorse della macchina virtuale SQL per abilitare le funzionalità per le macchine virtuali di SQL Server distribuite all'esterno di Azure Marketplace, nonché la conformità e una migliore gestibilità.
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
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985389"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrare una macchina virtuale di SQL Server in Azure con il provider di risorse della macchina virtuale SQLRegister a SQL Server virtual machine in Azure with the SQL VM resource provider

Questo articolo descrive come registrare la macchina virtuale (VM) di SQL Server in Azure con il provider di risorse della macchina virtuale SQL. La registrazione con il provider di risorse crea la _risorsa_ **della macchina virtuale SQL** all'interno della sottoscrizione, che è una risorsa separata dalla risorsa della macchina virtuale. L'annullamento della registrazione della macchina virtuale di SQL Server dal provider di risorse rimuoverà la _risorsa_ della **macchina virtuale SQL,** ma non eliminerà la macchina virtuale effettiva. 

La distribuzione di un'immagine di Azure Marketplace della macchina virtuale di SQL Server tramite il portale di Azure registra automaticamente la macchina virtuale di SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure o eseguire il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la macchina virtuale di SQL Server con il provider di risorse per:However, if you choose to self-install SQL Server on an Azure virtual machine, or provision an Azure virtual machine from a custom VHD, you should register your SQL Server VM with the resource provider for:

- **Vantaggi delle funzionalità**: la registrazione della macchina virtuale di SQL Server con il provider di risorse sblocca l'applicazione automatica di [patch,](virtual-machines-windows-sql-automated-patching.md)il [backup automatico,](virtual-machines-windows-sql-automated-backup-v2.md)nonché le funzionalità di monitoraggio e gestibilità. Inoltre, consente di sbloccare la flessibilità [delle licenze](virtual-machines-windows-sql-ahb.md) e delle [edizioni.](virtual-machines-windows-sql-change-edition.md) In precedenza, queste funzionalità erano disponibili solo per le immagini di macchine virtuali di SQL Server distribuite da Azure Marketplace.Before, these features were only available to SQL Server VM images deployed from Azure Marketplace. 

- **Conformità:** la registrazione con il provider di risorse DI SQL VM offre un metodo semplificato per soddisfare il requisito di notifica a Microsoft che il vantaggio Azure Hybrid è stato abilitato come specificato nei termini del prodotto. Questo processo elimina la necessità di gestire i moduli di registrazione delle licenze per ogni risorsa.  

- **Gestione gratuita**: La registrazione con il provider di risorse SQL VM in tutte e tre le modalità di gestibilità è completamente gratuita. Non sono previsti costi aggiuntivi associati al provider di risorse o alle modalità di gestione che cambiano. 

- **Gestione semplificata delle licenze:** la registrazione con il provider di risorse di SQL VM semplifica la gestione delle licenze di SQL Server e consente di identificare rapidamente le macchine virtuali di SQL Server con il vantaggio Azure Hybrid abilitato tramite il portale di [Azure,](virtual-machines-windows-sql-manage-portal.md)l'interfaccia della riga di comando Az o PowerShell: 

   # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Per utilizzare il provider di risorse della macchina virtuale SQL, è innanzitutto necessario [registrare la sottoscrizione con il provider](#register-subscription-with-rp)di risorse, che consente al provider di risorse di creare risorse all'interno della sottoscrizione specifica.

Per altre informazioni sui vantaggi dell'uso del provider di risorse della macchina virtuale SQL, vedere questo video di [channel9:For](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) more information about the benefits of using the SQL VM resource provider, watch this channel9 video: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Approfitta del provider di risorse VM SQL durante l'installazione automatica di SQL Server in Azure - Video di Microsoft Channel 9"></iframe>


## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario:To register your SQL Server VM with the resource provider, you'll need: 

- Una [sottoscrizione di Azure.](https://azure.microsoft.com/free/)
- Una macchina [virtuale di SQL Server](virtual-machines-windows-portal-sql-server-provision.md) del modello di risorse di Azure distribuita nel cloud pubblico o di Azure per enti pubblici. 
- La versione più recente [dell'interfaccia della riga](/cli/azure/install-azure-cli) di comando di Azure o [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modalità di gestione

Se [l'estensione SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md) non è già stata installata, la registrazione con il provider di risorse della macchina virtuale SQL installa automaticamente l'estensione IaaS di SQL Server in una delle tre modalità di gestione, specificate durante il processo di registrazione. Se non si specifica la modalità di gestione, verrà installata l'estensione SQL IaaS in modalità di gestione completa.  

Se l'estensione SQL IaaS è già stata installata manualmente, è già in modalità di gestione completa e la registrazione con il provider di risorse in modalità completa non riavvierà il servizio SQL Server.

Le tre modalità di gestione sono:

- **La** modalità lightweight non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server. Usare questa opzione per le macchine virtuali di SQL ServerSQL Server con più istanze o che partecipano a un'istanza del cluster di failover (FCI). Non vi è alcun impatto sulla memoria o sulla CPU quando si utilizza la modalità leggera e non vi è alcun costo associato. È consigliabile registrare prima la macchina virtuale di SQL Server in modalità leggera e quindi eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione pianificata.  

- **La** modalità completa offre tutte le funzionalità, ma richiede il riavvio delle autorizzazioni di SQL Server e dell'amministratore di sistema. Questa è l'opzione che viene installata per impostazione predefinita quando si installa manualmente l'estensione DI SQL IaaS. Utilizzarlo per la gestione di una macchina virtuale di SQL Server con una singola istanza. La modalità completa installa due servizi di Windows che hanno un impatto minimo sulla memoria e sulla CPU, che possono essere monitorati tramite Task Manager. L'utilizzo della modalità di gestione completa non prevede alcun costo. 

- La modalità **NoAgent** è dedicata a SQL Server 2008 e SQL Server 2008 R2 installati in Windows Server 2008. Non vi è alcun impatto sulla memoria o sulla CPU quando si utilizza la modalità NoAgent. L'utilizzo della modalità di gestibilità NoAgent non prevede alcun costo. 

È possibile visualizzare la modalità corrente dell'agente SQL Server IaaS utilizzando PowerShell:You can view the current mode of your SQL Server IaaS agent by using PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrare la sottoscrizione con RP

Per registrare la macchina virtuale di SQL Server con il provider di risorse della macchina virtuale SQL, è innanzitutto necessario registrare la sottoscrizione con il provider di risorse. In questo modo il provider di risorse della macchina virtuale SQL è in grado di creare risorse all'interno della sottoscrizione.  È possibile farlo usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.You can do so by using the Azure portal, the Azure CLI, or PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Aprire il portale di Azure e passare a **Tutti i servizi**. 
1. Vai a **Abbonamenti** e seleziona la sottoscrizione di interesse.  
1. Nella pagina **Abbonamenti** passare a **Provider di risorse**. 
1. Immettere **sql** nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare **Register**, **Re-register**o **Unregister** per il provider **Microsoft.SqlVirtualMachine,** a seconda dell'azione desiderata. 

![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Riga di comando

Registrare il provider di risorse della macchina virtuale SQL nella sottoscrizione di Azure usando l'interfaccia della riga di comando a z o PowerShell.Register your SQL VM resource provider to your Azure subscription using either Az CLI or PowerShell. 

# <a name="az-cli"></a>[Interfaccia della riga di comando AZ](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Registrare la macchina virtuale SQL con RPRegister SQL VM with RP 

### <a name="lightweight-management-mode"></a>Modalità di gestione leggera

Se [l'estensione dell'agente IaaS](virtual-machines-windows-sql-server-agent-extension.md) di SQL Server non è stata installata nella macchina virtuale, è consigliabile eseguire la registrazione con il provider di risorse della macchina virtuale SQL in modalità leggera. Verrà installata l'estensione SQL IaaS in [modalità leggera](#management-modes) e verrà impedito il riavvio del servizio SQL Server. È quindi possibile eseguire l'aggiornamento alla modalità completa in qualsiasi momento, ma in questo modo verrà riavviato il servizio SQL Server, pertanto è consigliabile attendere una finestra di manutenzione pianificata. 

Specificare il tipo di licenza di SQL`PAYG`Server come pay-as-you-go ( ) da pagare per uso, come vantaggio Azure Hybrid (`AHUB`) per usare la propria licenza o ripristino di emergenza (`DR`) per attivare la licenza di replica di ripristino di [emergenza gratuita.](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)

Le istanze del cluster di failover e le distribuzioni a più istanze possono essere registrate solo con il provider di risorse della macchina virtuale SQL in modalità leggera. 

# <a name="az-cli"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Registrare la macchina virtuale di SQL Server in modalità leggera con l'interfaccia della riga di comando di Az:Register SQL Server VM in lightweight mode with the Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrare la macchina virtuale di SQL Server in modalità leggera con PowerShell:Register SQL Server VM in lightweight mode with PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modalità di gestione completa


Se l'estensione SQL IaaS è già stata installata manualmente nella macchina virtuale, è possibile registrare la macchina virtuale di SQL Server in modalità completa senza riavviare il servizio SQL Server.If the SQL IaaS Extension has already been installed to the VM manually, then you can register the SQL Server VM in full mode without restarting the SQL Server service. **Tuttavia, se l'estensione SQL IaaS non è stata installata, la registrazione in modalità completa installerà l'estensione SQL IaaS in modalità completa e riavvierà il servizio SQL Server. Si prega di procedere con cautela.**


Per registrare la macchina virtuale di SQL Server direttamente in modalità completa (ed eventualmente riavviare il servizio SQL Server), usare il comando di PowerShell seguente:To register your SQL Server VM directly in full mode (and possibly restart your SQL Server service), use the following PowerShell command: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modalità di gestione NoAgent 

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 (_non R2_) possono essere registrati con il provider di risorse VM SQL in [modalità NoAgent](#management-modes). Questa opzione garantisce la conformità e consente di monitorare la macchina virtuale di SQL Server nel portale di Azure con funzionalità limitate.

Specificare `AHUB` `PAYG`i `DR` o i quali **sqlLicenseType**e `SQL2008-WS2008` o `SQL2008R2-WS2008`come **sqlImageOffer**. 

Per registrare SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, utilizzare il seguente frammento di codice a interfaccia della riga di comando Az o PowerShell: 


# <a name="az-cli"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Registrare la macchina virtuale di SQL Server 2008 in modalità NoAgent con l'interfaccia della riga di comando az:Register your SQL Server 2008 VM in NoAgent mode with the Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registrare la macchina virtuale di SQL Server 2008 R2 in modalità NoAgent con l'interfaccia della riga di comando az:Register your SQL Server 2008 R2 VM in NoAgent mode with the Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrare la macchina virtuale di SQL Server 2008 in modalità NoAgent con PowerShell:Register SQL Server 2008 VM in NoAgent mode with PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registrare la macchina virtuale di SQL Server 2008 R2 in modalità NoAgent con PowerShell:Register SQL Server 2008 R2 VM in NoAgent mode with PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Eseguire l'aggiornamento alla modalità di gestione completa 

Le macchine virtuali di SQL Server in cui è installata l'estensione IaaS leggera possono aggiornare la modalità alla _modalità completa_ usando il portale di Azure, l'interfaccia della riga di comando di Az o PowerShell.SQL Server VMs that have the *lightweight* IaaS extension installed can upgrade the mode to full using the Azure portal, the Az CLI, or PowerShell. Le macchine virtuali di SQL Server in modalità _NoAgent_ possono eseguire l'aggiornamento a _pieno_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile eseguire il downgrade: per eseguire questa operazione, sarà necessario [annullare](#unregister-vm-from-rp) la registrazione della macchina virtuale di SQL Server dal provider di risorse della macchina virtuale SQL. In questo modo verrà rimossa la _risorsa_ **macchina virtuale SQL** , ma non verrà eliminata la macchina virtuale effettiva. 

È possibile visualizzare la modalità corrente dell'agente SQL Server IaaS utilizzando PowerShell:You can view the current mode of your SQL Server IaaS agent by using PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Per aggiornare la modalità agente a full: 


### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa [macchine virtuali SQL.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Selezionare la macchina virtuale di SQL Server e scegliere **Panoramica**. 
1. Per le macchine virtuali di SQL Server con la modalità NoAgent o IaaS leggera, selezionare il tipo di **licenza solo e gli aggiornamenti dell'edizione sono disponibili con il messaggio di estensione SQL IaaS.**

   ![Selezioni per la modifica della modalità dal portale](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Selezionare la casella di controllo **Accetto di riavviare il servizio SQL Server nella macchina virtuale** e quindi selezionare **Conferma** per aggiornare la modalità IaaS a full. 

    ![Casella di controllo per l'accordo di riavviare il servizio SQL Server nella macchina virtuale](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Riga di comando

# <a name="az-cli"></a>[Interfaccia della riga di comando AZ](#tab/bash)

Eseguire il frammento di codice Az CLI seguente:Run the following Az CLI code snippet:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Eseguire il frammento di codice di PowerShell seguente:Run the following PowerShell code snippet:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificare lo stato della registrazione
È possibile verificare se la macchina virtuale di SQL Server è già stata registrata con il provider di risorse della macchina virtuale SQL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.You can verify if your SQL Server VM has already been registered with the SQL VM resource provider by using the Azure portal, the Azure CLI, or PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali di SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selezionare la macchina virtuale di SQL Server dall'elenco. Se la macchina virtuale di SQL Server non è elencata qui, è probabile che non sia stata registrata con il provider di risorse della macchina virtuale SQL. 
1. Visualizzare il valore in **Stato**. Se **lo stato** è **Riuscito**, la macchina virtuale di SQL Server è stata registrata correttamente con il provider di risorse della macchina virtuale SQL. 

![Verificare lo stato con la registrazione DI SQL RPVerify status with SQL RP registration](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Riga di comando

Verificare lo stato corrente di registrazione della macchina virtuale di SQL Server usando l'interfaccia della riga di comando Az o PowerShell.Verify current SQL Server VM registration status using either Az CLI or PowerShell. `ProvisioningState`se `Succeeded` la registrazione è riuscita. 

# <a name="az-cli"></a>[Interfaccia della riga di comando AZ](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Un errore indica che la macchina virtuale di SQL Server non è stata registrata con il provider di risorse. 


## <a name="unregister-vm-from-rp"></a>Annullare la registrazione della macchina virtuale dal componenteUnregister VM from RP

Per annullare la registrazione della macchina virtuale di SQL Server con il provider di risorse DELLA macchina virtuale SQL, eliminare la risorsa macchina virtuale SQL usando il portale di Azure o l'interfaccia della riga di comando di Azure.To unregister your SQL Server VM with the SQL VM resource provider, delete the SQL Virtual Machine *resource* using the Azure portal or Azure CLI. L'eliminazione della *risorsa* macchina virtuale SQL non comporta l'eliminazione della macchina virtuale di SQL Server. Prestare tuttavia attenzione e seguire attentamente i passaggi perché è possibile eliminare inavvertitamente la macchina virtuale quando si tenta di rimuovere la *risorsa.* 

L'annullamento della registrazione della macchina virtuale SQL con il provider di risorse della macchina virtuale SQL è necessario per eseguire il downgrade della modalità di gestione da completa. 

### <a name="azure-portal"></a>Portale di Azure

Per annullare la registrazione della macchina virtuale di SQL Server con il provider di risorse tramite il portale di Azure, eseguire la procedura seguente:To unregister your SQL Server VM with the resource provider using the Azure portal, follow these steps:

1. Accedere al portale di [Azure](https://portal.azure.com).
1. Passare alla risorsa VM di SQL Server.Navigate to the SQL Server VM resource. 
  
   ![Risorsa macchine virtuali SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selezionare **Elimina**. 

   ![Eliminare il provider di risorse VM SQLDelete SQL VM resource provider](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Digitare il nome della macchina virtuale SQL e **deselezionare la casella di controllo accanto alla macchina virtuale**.

   ![Eliminare il provider di risorse VM SQLDelete SQL VM resource provider](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se non si deseleziona la casella di controllo accanto al nome della macchina virtuale, la macchina virtuale *verrà eliminata* completamente. Deselezionare la casella di controllo per annullare la registrazione della macchina virtuale di SQL Server dal provider di risorse ma *non eliminare la macchina virtuale effettiva.* 

1. Selezionare **Elimina** per confermare l'eliminazione della *risorsa*risorsa macchina virtuale SQL e non della macchina virtuale di SQL Server. 

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per annullare la registrazione della macchina virtuale di SQL Server dal provider di risorse con l'interfaccia della riga di comando di Azure, usare il comando [az sql vm delete.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) In questo modo verrà rimossa la *risorsa* della macchina virtuale di SQL Server, ma la macchina virtuale non verrà eliminata. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per annullare la registrazione della macchina virtuale di SQL Server dal provider di risorse con l'interfaccia della riga di comando di Azure, usare il comando [New-AzSqlVM.To](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)unregister your SQL Server virtual machine from the resource provider with Azure CLI, use the New-AzSqlVM command. In questo modo verrà rimossa la *risorsa* della macchina virtuale di SQL Server, ma la macchina virtuale non verrà eliminata. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitazioni

Il provider di risorse della macchina virtuale SQL supporta solo:The SQL VM resource provider only supports:
- SQL Server VMs deployed through the Azure Resource Manager. Le macchine virtuali di SQL Server distribuite tramite il modello classico non sono supportate. 
- Macchine virtuali di SQL Server distribuite nel cloud pubblico o di Azure per enti pubblici. Le distribuzioni in altri cloud privati o governativi non sono supportate. 


## <a name="frequently-asked-questions"></a>Domande frequenti 

**È consigliabile registrare la macchina virtuale di SQL Server di cui è stato eseguito il provisioning da un'immagine di SQL Server in Azure Marketplace?**

No. Microsoft automatically registers VMs provisioned from the SQL Server images in Azure Marketplace. La registrazione con il provider di risorse della macchina virtuale SQL è necessaria solo se non è *stato* eseguito il provisioning della macchina virtuale dalle immagini di SQL Server in Azure Marketplace e SQL Server è stato autoinstallato.

**Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?** 

Sì. I clienti devono registrare le macchine virtuali di SQL Server con il provider di risorse della macchina virtuale SQL se non hanno usato un'immagine di SQL Server da Azure Marketplace e invece SQL Server autoinstallato o se hanno portato il disco rigido virtuale personalizzato. Le macchine virtuali di proprietà di tutti i tipi di sottoscrizioni (Direct, Enterprise Agreement e Cloud Solution Provider) possono registrarsi presso il provider di risorse della macchina virtuale SQL.

**È necessario eseguire la registrazione con il provider di risorse della macchina virtuale SQL se nella macchina virtuale di SQL Server è già installata l'estensione IaaS di SQL Server?**

Se la macchina virtuale di SQL Server è autoinstallata e non è stato eseguito il provisioning dalle immagini di SQL Server in Azure Marketplace, è necessario eseguire la registrazione con il provider di risorse della macchina virtuale SQL anche se è stata installata l'estensione IaaS di SQL Server.If your SQL Server VM is self-installed and not provisioned from the SQL Server images in Azure Marketplace, you should register with the SQL VM resource provider even if you installed the SQL Server IaaS extension. La registrazione con il provider di risorse SQL VM crea una nuova risorsa di tipo Microsoft.SqlVirtualMachines.Registering with the SQL VM resource provider creates a new resource of type Microsoft.SqlVirtualMachines. L'installazione dell'estensione IaaS di SQL Server non crea tale risorsa.

**Qual è la modalità di gestione predefinita durante la registrazione con il provider di risorse della macchina virtuale SQL?**

La modalità di gestione predefinita quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL è *completa.* Se la proprietà di gestione di SQL Server non è impostata quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL, la modalità verrà impostata come gestibilità completa e il servizio SQL Server verrà riavviato. È consigliabile eseguire prima la registrazione con il provider di risorse della macchina virtuale SQL in modalità leggera e quindi eseguire l'aggiornamento al completo durante una finestra di manutenzione. 

**Quali sono i prerequisiti per la registrazione con il provider di risorse della macchina virtuale SQL?**

Non esistono prerequisiti per la registrazione con il provider di risorse della macchina virtuale SQL in modalità leggera o senza agente. Il prerequisito per la registrazione con il provider di risorse della macchina virtuale SQL in modalità completa è la presenza dell'estensione IaaS di SQL Server installata nella macchina virtuale, altrimenti il servizio SQL Server verrà riavviato. 

**È possibile eseguire la registrazione con il provider di risorse della macchina virtuale SQL se non è installata l'estensione IaaS di SQL Server nella macchina virtuale?**

Sì, è possibile eseguire la registrazione con il provider di risorse della macchina virtuale SQL in modalità di gestione leggera se nell'appiliato iaaS di SQL Server è installato nella macchina virtuale. In modalità leggera, il provider di risorse della macchina virtuale SQL utilizzerà un'app console per verificare la versione e l'edizione dell'istanza di SQL Server. 

La modalità di gestione SQL predefinita durante la registrazione con il provider di risorse della macchina virtuale SQL è _Full_. Se la proprietà Di gestione SQL non è impostata durante la registrazione con il provider di risorse della macchina virtuale SQL, la modalità verrà impostata come gestibilità completa. È consigliabile eseguire prima la registrazione con il provider di risorse della macchina virtuale SQL in modalità leggera e quindi eseguire l'aggiornamento al completo durante una finestra di manutenzione. 

**La registrazione con il provider di risorse della macchina virtuale SQL installerà un agente nella macchina virtuale?**

No. La registrazione con il provider di risorse della macchina virtuale SQL creerà solo una nuova risorsa di metadati. Non verrà installato un agente nella macchina virtuale.

L'estensione IaaS di SQL Server è necessaria solo per abilitare la gestibilità completa. L'aggiornamento della modalità di gestibilità da leggero a pieno consente di installare l'estensione IaaS di SQL Server e di riavviare SQL Server.

**La registrazione con il provider di risorse VM di SQL Server riavvierà SQL Server nella macchina virtuale?**

Dipende dalla modalità specificata durante la registrazione. Se viene specificata la modalità lightweight o NoAgent, il servizio SQL Server non verrà riavviato. Tuttavia, specificando la modalità di gestione come completa o lasciando vuota la modalità di gestione verrà installata l'estensione SQL IaaS in modalità di gestione completa, che causerà il riavvio del servizio SQL Server. 

**Qual è la differenza tra le modalità di gestione leggera e senza agenti durante la registrazione con il provider di risorse della macchina virtuale SQL?** 

La modalità di gestione senza agenti è disponibile solo per SQL Server 2008 e SQL Server 2008 R2 in Windows Server 2008. È l'unica modalità di gestione disponibile per queste versioni. Per tutte le altre versioni di SQL ServerSQL Server, le due modalità di gestibilità disponibili sono leggere e complete. 

La modalità No-agent richiede che le proprietà della versione e dell'edizione di SQL ServerSQL Server siano impostate dal cliente. La modalità Lightweight esegue una query sulla macchina virtuale per trovare la versione e l'edizione dell'istanza di SQL Server.

**È possibile registrarsi con il provider di risorse DELLA macchina virtuale SQL senza specificare il tipo di licenza di SQL Server?**

No. Il tipo di licenza di SQL Server non è una proprietà facoltativa quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL. È necessario impostare il tipo di licenza di SQL Server come vantaggio con pagamento in base al consumo o vantaggio Azure Hybrid durante la registrazione con il provider di risorse della macchina virtuale SQL in tutte le modalità di gestibilità (senza agente, leggero e completo).

**È possibile aggiornare l'estensione IaaS di SQL Server dalla modalità senza agente alla modalità completa?**

No. L'aggiornamento della modalità di gestibilità a pieno o leggero non è disponibile per la modalità senza agente. Si tratta di una limitazione tecnica di Windows Server 2008. Sarà necessario aggiornare il sistema operativo prima a Windows Server 2008 R2 o versione successiva e quindi sarà possibile eseguire l'aggiornamento alla modalità di gestione completa. 

**È possibile aggiornare l'estensione IaaS di SQL Server dalla modalità leggera alla modalità completa?**

Sì. L'aggiornamento della modalità di gestibilità da leggero a pieno è supportato tramite PowerShell o il portale di Azure.Upgrading the manageability mode from lightweight to full is supported via PowerShell or the Azure portal. Richiede il riavvio del servizio SQL Server.

**È possibile eseguire il downgrade dell'estensione IaaS di SQL Server dalla modalità completa alla modalità di gestione leggera o senza agente?**

No. Il downgrade della modalità di gestibilità dell'estensione IaaS di SQL Server non è supportato. La modalità di gestibilità non può essere declassata dalla modalità completa alla modalità leggera o senza agente e non può essere declassata dalla modalità leggera alla modalità senza agente. 

Per modificare la modalità di gestibilità dalla gestibilità completa, [annullare](#unregister-vm-from-rp) la registrazione della macchina virtuale di SQL Server dal provider di risorse di SQL Server eliminando la *risorsa* di SQL Server e registrando nuovamente la macchina virtuale di SQL Server con il provider di risorse DELLA macchina virtuale SQL in una modalità di gestione diversa.

**È possibile registrarsi con il provider di risorse della macchina virtuale SQL dal portale di Azure?**

No. La registrazione con il provider di risorse della macchina virtuale SQL non è disponibile nel portale di Azure.Registering with the SQL VM resource provider is not available in the Azure portal. La registrazione con il provider di risorse della macchina virtuale SQL è supportata solo con l'interfaccia della riga di comando di Azure o PowerShell.Registering with the SQL VM resource provider is only supported with the Azure CLI or PowerShell. 

**È possibile registrare una macchina virtuale con il provider di risorse della macchina virtuale SQL prima dell'installazione di SQL Server?**

No. Una macchina virtuale deve avere almeno un'istanza di SQL Server (Motore di database) per eseguire correttamente la registrazione con il provider di risorse della macchina virtuale SQL. Se nella macchina virtuale non è presente alcuna istanza di SQL Server, la nuova risorsa Microsoft.SqlVirtualMachine sarà in stato di errore.

**È possibile registrare una macchina virtuale con il provider di risorse VM SQL se sono presenti più istanze di SQL Server?**

Sì. Il provider di risorse della macchina virtuale SQL registrerà solo un'istanza di SQL Server (Motore di database). Il provider di risorse DELLA macchina virtuale SQL registrerà l'istanza predefinita di SQL Server nel caso di più istanze. Se non è presente alcuna istanza predefinita, è supportata solo la registrazione in modalità lightweight. Per eseguire l'aggiornamento dalla modalità di gestibilità completa a quella completa, l'istanza predefinita di SQL Server deve esistere oppure la macchina virtuale deve avere una sola istanza denominata di SQL Server.To upgrade from lightweight to full manageability mode, either the default SQL Server instance should exist or the VM should have only one named SQL Server instance.

**È possibile registrare un'istanza del cluster di failover di SQL Server con il provider di risorse della macchina virtuale SQL?**

Sì. Le istanze del cluster di failover di SQL Server in una macchina virtuale di Azure possono essere registrate con il provider di risorse della macchina virtuale SQL in modalità leggera. Tuttavia, le istanze del cluster di failover di SQL Server non possono essere aggiornate alla modalità di gestibilità completa.

**È possibile registrare la macchina virtuale con il provider di risorse VM SQL se è configurato un gruppo di disponibilità AlwaysOnAlways On availability group is configured?**

Sì. Non esistono restrizioni per la registrazione di un'istanza di SQL Server in una macchina virtuale di Azure con il provider di risorse della macchina virtuale SQL se si fa parte di una configurazione del gruppo di disponibilità AlwaysOnAlways On availability group.

**Qual è il costo per la registrazione con il provider di risorse della macchina virtuale SQL o per l'aggiornamento alla modalità di gestibilità completa?**
No. Non è prevista alcuna commissione associata alla registrazione con il provider di risorse della macchina virtuale SQL o all'utilizzo di una delle tre modalità di gestibilità. La gestione della macchina virtuale di SQL Server con il provider di risorse è completamente gratuita. 

**Qual è l'impatto sulle prestazioni dell'utilizzo delle diverse modalità di gestibilità?**
Non vi è alcun impatto quando si utilizzano le modalità *NoAgent* e di gestibilità *leggera.* L'impatto è minimo quando si utilizza la modalità di gestibilità *completa* da due servizi installati nel sistema operativo. Questi possono essere monitorati tramite task manager e visualizzati nella console dei servizi di Windows integrata. 

I due nomi di servizio sono:
- `SqlIaaSExtensionQuery`(Nome visualizzato `Microsoft SQL Server IaaS Query Service`- )
- `SQLIaaSExtension`(Nome visualizzato `Microsoft SQL Server IaaS Agent`- )


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale WindowsFAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per SQL Server in una macchina virtuale WindowsPricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale WindowsRelease notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
