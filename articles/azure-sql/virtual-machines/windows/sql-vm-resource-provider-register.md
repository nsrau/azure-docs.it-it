---
title: Installare l'estensione dell'agente IaaS SQL Server
description: Registrare la macchina virtuale di Azure SQL Server con il provider di risorse VM SQL per abilitare le funzionalità per SQL Server macchine virtuali distribuite al di fuori di Azure Marketplace, nonché la conformità e una migliore gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286142"
---
# <a name="install-sql-server-iaas-agent-extension"></a>Installare l'estensione dell'agente IaaS SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

La registrazione della macchina virtuale SQL Server con il provider di risorse VM SQL consente di installare l' [estensione dell'agente IaaS SQL Server](sql-server-iaas-agent-extension-automate-management.md). 

Questo articolo illustra come registrare una singola macchina virtuale SQL Server con il provider di risorse VM SQL. In alternativa, è possibile registrare [automaticamente](sql-vm-resource-provider-automatic-registration.md) tutte le macchine virtuali SQL Server o generate [tramite script in blocco](sql-vm-resource-provider-bulk-register.md).


## <a name="overview"></a>Panoramica

La registrazione con il provider di risorse installa l' [estensione dell'agente IaaS di SQL Server](sql-server-iaas-agent-extension-automate-management.md) e crea anche la _risorsa_ della **macchina virtuale SQL** nella sottoscrizione, che è una risorsa _separata_ dalla risorsa della macchina virtuale. Se si annulla la registrazione della macchina virtuale SQL Server dal provider di risorse, si rimuoverà la **risorsa** _macchina virtuale SQL_ , ma la macchina virtuale effettiva non verrà eliminata.

La distribuzione di un'immagine di Azure Marketplace di macchina virtuale SQL Server tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure o di effettuare il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la VM SQL Server con il provider di risorse VM SQL per installare l'estensione SQL IaaS Agent. 

Per usare il provider di risorse VM SQL, è necessario prima [registrare la sottoscrizione con il provider di risorse](#register-subscription-with-rp), che consente al provider di risorse di creare risorse all'interno della sottoscrizione specifica.

> [!IMPORTANT]
> L'estensione SQL IaaS Agent raccoglie i dati per lo scopo esplicito di fornire ai clienti vantaggi facoltativi quando si usa SQL Server all'interno di macchine virtuali di Azure. Microsoft non utilizzerà questi dati per i controlli delle licenze senza il consenso anticipato del cliente. Per ulteriori informazioni, vedere la [SQL Server supplemento sulla privacy](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una macchina virtuale del modello di risorse di Azure [Windows Server 2008 (o versione successiva)](../../../virtual-machines/windows/quick-create-portal.md) con [SQL Server 2008 (o versione successiva)](https://www.microsoft.com/sql-server/sql-server-downloads) distribuita nel cloud pubblico o Azure per enti pubblici. 
- La versione più recente dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o [Azure PowerShell (minimo 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Registrare la sottoscrizione con il provider di risorse

Per registrare la macchina virtuale di SQL Server con il provider di risorse macchine virtuali SQL, è necessario prima registrare la sottoscrizione con il provider di risorse. In questo modo, il provider di risorse macchine virtuali SQL è in grado di creare risorse all'interno della sottoscrizione.  A tale scopo, è possibile usare la portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al portale di Azure e andare a **Tutti i servizi**. 
1. Andare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nella pagina **Sottoscrizioni** andare a **Provider di risorse**. 
1. Digitare **sql** nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra **Registra** , **Ripeti registrazione** , o **Annulla registrazione** per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

   ![Modificare il provider](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Riga di comando

Registrare il provider di risorse VM SQL nella sottoscrizione di Azure usando l'interfaccia della riga di comando di Azure o Azure PowerShell. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Registra con RP

Sono disponibili tre modalità di gestione per l' [SQL Server l'estensione dell'agente IaaS](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

L'installazione dell'estensione in modalità di gestione completa riavvia il servizio SQL Server, quindi è consigliabile installare prima l'estensione in modalità Lightweight, quindi eseguire l' [aggiornamento a full](#upgrade-to-full) durante una finestra di manutenzione. 

### <a name="lightweight-management-mode"></a>Modalità di gestione semplificata

Usare l'interfaccia della riga di comando di Azure o Azure PowerShell per registrare la VM SQL Server con il provider di risorse e installare l'estensione SQL IaaS in modalità lightweight. Il servizio SQL Server non verrà riavviato. È quindi possibile eseguire l'aggiornamento alla modalità completa in qualsiasi momento, ma in questo modo verrà riavviato il servizio SQL Server. Pertanto, è consigliabile attendere una finestra di manutenzione pianificata. 

Indicare il tipo di licenza di SQL Server come con pagamento in base al consumo (`PAYG`) per il pagamento in base all'uso, Vantaggio Azure Hybrid (`AHUB`) per usare la propria licenza o ripristino di emergenza (`DR`) per attivare la [licenza di ripristino di emergenza gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Le istanze del cluster di failover e le distribuzioni a istanze diverse possono essere registrate solo con il provider di risorse VM SQL in modalità lightweight. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Registrare una macchina virtuale SQL Server in modalità Lightweight con l'interfaccia della riga di comando di Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrare una macchina virtuale SQL Server in modalità Lightweight con Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modalità di gestione completa

La registrazione della macchina virtuale SQL Server in modalità completa riavvierà il servizio SQL Server. Procedere con cautela. 

Per registrare la macchina virtuale SQL Server direttamente in modalità completa (ed eventualmente riavviare il servizio SQL Server), usare il comando Azure PowerShell seguente: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modalità di gestione NoAgent 

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 ( _non R2_ ) possono essere registrati con il provider di risorse macchine virtuali SQL in modalità [NoAgent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.

Specificare `AHUB`, `PAYG`o `DR` come **sqlLicenseType** e `SQL2008-WS2008` o `SQL2008R2-WS2008`come **sqlImageOffer**. 

Per registrare l'istanza di SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, usare l'interfaccia della riga di comando di Azure o il frammento di codice Azure PowerShell seguente: 


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Registrare la macchina virtuale di SQL Server 2008 in modalità noagent con l'interfaccia della riga di comando di Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registrare la macchina virtuale di SQL Server 2008 R2 in modalità noagent con l'interfaccia della riga di comando di Azure: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registrare la macchina virtuale SQL Server 2008 in modalità noagent con Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Registrare la macchina virtuale SQL Server 2008 R2 in modalità noagent con Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>Modalità di verifica

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Aggiorna a completo  

SQL Server macchine virtuali con l'estensione IaaS *Lightweight* installata possono aggiornare la modalità _completa_ usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell. Le macchine virtuali SQL Server in modalità _NoAgent_ possono eseguire l'aggiornamento alla modalità _completa_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile effettuare il downgrade. Per farlo, è necessario [annullare la registrazione](#unregister-from-rp) della macchina virtuale SQL Server dal provider di risorse della macchina virtuale SQL. Questa operazione rimuoverà la **risorsa** _macchina virtuale di SQL Server_ ma non eliminerà la macchina virtuale in sé. 


### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Andare alla risorsa [macchine virtuali SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Selezionare la macchina virtuale SQL Server e scegliere **Panoramica**. 
1. Per le macchine virtuali di SQL Server in modalità IaaS NoAgent o semplificata, selezionare il messaggio **Solo il tipo di licenza e gli aggiornamenti dell'edizione sono disponibili con l'estensione IaaS di SQL**.

   ![Selezioni per modificare la modalità dal portale](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Selezionare la casella di controllo **Accetto di riavviare il servizio SQL Server nella macchina virtuale** , quindi **Conferma** per eseguire l'aggiornamento alla modalità IaaS completa. 

    ![Selezionare la casella per accettare di riavviare il servizio SQL Server nella macchina virtuale](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Eseguire il frammento di codice dell'interfaccia della riga di comando di Azure seguente:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Eseguire il frammento di codice Azure PowerShell seguente:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificare lo stato di registrazione
È possibile verificare se la macchina virtuale SQL Server è già stata registrata con il provider di risorse VM SQL usando il portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali SQL Server](manage-sql-vm-portal.md).
1. Selezionare la macchina virtuale di SQL Server dall'elenco. Se la macchina virtuale di SQL Server non è inclusa nell'elenco, è probabile che non sia stata registrata con il provider di risorse della macchina virtuale SQL. 
1. Visualizzare il valore in **Stato**. Se lo **Stato** è **Riuscito** , la macchina virtuale di SQL Server è stata registrata correttamente con il provider di risorse macchine virtuali SQL. 

   ![Verificare lo stato con la registrazione del provider di risorse SQL](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Riga di comando

Verificare lo stato di registrazione della VM SQL Server corrente usando l'interfaccia della riga di comando di Azure o Azure PowerShell. `ProvisioningState` mostrerà `Succeeded` se la registrazione è riuscita. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Un errore indica che la macchina virtuale di SQL Server non è stata registrata con il provider di risorse. 


## <a name="unregister-from-rp"></a>Annulla registrazione da RP

Per annullare la registrazione della VM SQL Server con il provider di risorse VM SQL, eliminare la *risorsa* macchina virtuale SQL usando l'interfaccia della riga di comando di portale di Azure o Azure. L'eliminazione della *risorsa* della macchina virtuale SQL non comporta l'eliminazione della VM SQL Server. Tuttavia, prestare attenzione e seguire attentamente i passaggi perché è possibile eliminare inavvertitamente la macchina virtuale quando si tenta di rimuovere la *risorsa*. 

L'annullamento della registrazione della macchina virtuale SQL con il provider di risorse VM SQL è necessario per eseguire il downgrade della modalità di gestione da piena. 

### <a name="azure-portal"></a>Portale di Azure

Per annullare la registrazione della macchina virtuale di SQL Server con il provider di risorse usando il portale di Azure, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare alla risorsa della macchina virtuale SQL. 
  
   ![Risorsa di macchine virtuali SQL](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Selezionare **Elimina**. 

   ![Selezionare Elimina nella parte superiore dello spostamento](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Digitare il nome della macchina virtuale SQL e **deselezionare la casella di controllo accanto alla macchina virtuale**.

   ![Deselezionare la VM per evitare di eliminare la macchina virtuale effettiva e quindi selezionare Elimina per procedere con l'eliminazione della risorsa VM SQL](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se non si deseleziona la casella di controllo accanto al nome della macchina virtuale, la macchina virtuale verrà *eliminata* completamente. Deselezionare la casella di controllo per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse, operazione che *non eliminerà la macchina virtuale effettiva*. 

1. Selezionare **Elimina** per confermare l'eliminazione della *risorsa* della macchina virtuale SQL e non della VM SQL Server. 

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse con l'interfaccia della riga di comando di Azure, usare il comando [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) . Verrà rimossa la *risorsa* della macchina virtuale SQL Server ma la macchina virtuale non verrà eliminata. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse con Azure PowerShell, usare il comando [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Verrà rimossa la *risorsa* della macchina virtuale SQL Server ma la macchina virtuale non verrà eliminata. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)  
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)
