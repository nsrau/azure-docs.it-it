---
title: Eseguire la registrazione con un provider di risorse di macchine virtuali SQL
description: Registrare la macchina virtuale di Azure SQL Server con il provider di risorse VM SQL per abilitare le funzionalità per SQL Server macchine virtuali distribuite al di fuori di Azure Marketplace, nonché la conformità e una migliore gestibilità.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 11e8a2fd709b40c68b90e5ed139f18997e4cb29e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396965"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider-rp"></a>Registrare una macchina virtuale SQL Server in Azure con il provider di risorse VM SQL (RP)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo descrive come registrare la macchina virtuale SQL Server (VM) in Azure con il provider di risorse VM SQL (RP). La registrazione con il provider di risorse crea la **risorsa** _macchina virtuale SQL_ all'interno della sottoscrizione, che è una risorsa separata dalla risorsa della macchina virtuale. Se si annulla la registrazione della macchina virtuale SQL Server dal provider di risorse, si rimuoverà la **risorsa** _macchina virtuale SQL_, ma la macchina virtuale effettiva non verrà eliminata. 

La distribuzione di un'immagine di Azure Marketplace di macchina virtuale SQL Server tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, se si sceglie di installare autonomamente SQL Server in una macchina virtuale di Azure o di eseguire il provisioning di una macchina virtuale di Azure da un disco rigido virtuale personalizzato, è necessario registrare la macchina virtuale di SQL Server con il provider di risorse per:

- **Vantaggi della funzionalità**: la registrazione della macchina virtuale di SQL Server con il provider di risorse sblocca l'[applicazione automatica delle patch](automated-patching.md), i [backup automatizzati](automated-backup.md), nonché le funzionalità di monitoraggio e gestibilità. Sblocca anche flessibilità per [licenze](licensing-model-azure-hybrid-benefit-ahb-change.md) ed [edizioni](change-sql-server-edition.md). In precedenza, queste funzionalità erano disponibili solo per le immagini di macchine virtuali di SQL Server distribuite da Azure Marketplace. 

- **Conformità**: la registrazione con il provider di risorse macchine virtuali SQL offre un metodo semplificato per soddisfare la necessità di notificare a Microsoft che il Vantaggio Azure Hybrid è stato abilitato come indicato nelle condizioni del prodotto. Questo processo rimuove la necessità di gestire i moduli di registrazione delle licenze per ogni risorsa.  

- **Gestione gratuita**: la registrazione con il provider di risorse VM SQL in tutte e tre le modalità di gestione è completamente gratuita. Non sono previsti costi aggiuntivi per il provider di risorse o con modalità di gestione diverse. 

- **Gestione semplificata delle licenze**: la registrazione con il provider di risorse macchine virtuali SQL semplifica la gestione delle licenze SQL Server e consente di identificare rapidamente le macchine virtuali di SQL Server con Vantaggio Azure Hybrid abilitato usando il [portale di Azure](manage-sql-vm-portal.md), l'interfaccia della riga di comando di Azure o PowerShell: 

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

Per usare il provider di risorse VM SQL, è necessario prima [registrare la sottoscrizione con il provider di risorse](#register-subscription-with-rp), che consente al provider di risorse di creare risorse all'interno della sottoscrizione specifica.

## <a name="prerequisites"></a>Prerequisiti

Per registrare la macchina virtuale di SQL Server con il provider di risorse, è necessario quanto segue: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale di SQL Server](create-sql-vm-portal.md) del modello di risorse di Azure distribuita nel cloud pubblico o Azure per enti pubblici. 
- La versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) o di [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Modalità di gestione

Se l'[estensione IaaS di SQL](sql-server-iaas-agent-extension-automate-management.md) non è ancora stata installata, la registrazione con il provider di risorse macchine virtuali SQL la installerà automaticamente in una delle tre modalità di gestione, specificata durante il processo di registrazione. Se non si specifica la modalità di gestione, l'estensione IaaS SQL verrà installata in modalità di gestione completa.  

Se l'estensione SQL IaaS è già stata installata manualmente, è già in modalità di gestione completa e la registrazione con il provider di risorse in modalità completa non riavvierà il servizio SQL Server.

Le tre modalità di gestione sono:

- La modalità **semplificata** non richiede il riavvio di SQL Server, ma supporta solo la modifica del tipo di licenza e dell'edizione di SQL Server. Usare questa opzione per le macchine virtuali SQL Server con più istanze o per partecipare a un'istanza del cluster di failover (FCI). Non vi è alcun impatto sulla memoria o sulla CPU quando si usa la modalità semplificata e non vi sono costi associati. Si consiglia di eseguire prima la registrazione della macchina virtuale di SQL Server in modalità semplificata, quindi di eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione pianificata.  

- La modalità **completa** offre tutte le funzionalità, ma richiede il riavvio di SQL Server e le autorizzazioni di amministratore di sistema. Questa è l'opzione predefinita quando si installa manualmente l'estensione IaaS di SQL. Usarla per gestire una macchina virtuale di SQL Server con una singola istanza. La modalità completa installa due servizi Windows che hanno un effetto minimo sulla memoria e sulla CPU, monitorabili tramite Gestione attività. Non è previsto alcun costo associato all'uso della modalità di gestione completa. 

- La modalità **NoAgent** è dedicata a SQL Server 2008 e SQL Server 2008 R2 installati in Windows Server 2008. Non vi è alcun effetto sulla memoria o sulla CPU quando si usa la modalità NoAgent. Non è previsto alcun costo associato all'uso della modalità di gestione NoAgent. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrare la sottoscrizione con il provider di risorse

Per registrare la macchina virtuale di SQL Server con il provider di risorse macchine virtuali SQL, è necessario prima registrare la sottoscrizione con il provider di risorse. In questo modo, il provider di risorse macchine virtuali SQL è in grado di creare risorse all'interno della sottoscrizione.  È possibile farlo usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al portale di Azure e andare a **Tutti i servizi**. 
1. Andare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nella pagina **Sottoscrizioni** andare a **Provider di risorse**. 
1. Digitare **sql** nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra **Registra**, **Ripeti registrazione**, o **Annulla registrazione** per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

   ![Modificare il provider](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Riga di comando

Registrare il provider di risorse macchine virtuali SQL nella sottoscrizione di Azure usando l'interfaccia della riga di comando di Azure o PowerShell. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

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

## <a name="register-with-rp"></a>Registra con RP

### <a name="lightweight-management-mode"></a>Modalità di gestione semplificata

Se la [SQL Server estensione agente IaaS](sql-server-iaas-agent-extension-automate-management.md) non è stata installata nella macchina virtuale, si consiglia di eseguire la registrazione con il provider di risorse VM SQL in modalità lightweight. Questa operazione installerà l'estensione IaaS di SQL in [modalità semplificata](#management-modes) e impedirà il riavvio del servizio SQL Server. È quindi possibile eseguire l'aggiornamento alla modalità completa in qualsiasi momento, ma in questo modo verrà riavviato il servizio SQL Server. Pertanto, è consigliabile attendere una finestra di manutenzione pianificata. 

Indicare il tipo di licenza di SQL Server come con pagamento in base al consumo (`PAYG`) per il pagamento in base all'uso, Vantaggio Azure Hybrid (`AHUB`) per usare la propria licenza o ripristino di emergenza (`DR`) per attivare la [licenza di ripristino di emergenza gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Le istanze del cluster di failover e le distribuzioni a istanze diverse possono essere registrate solo con il provider di risorse VM SQL in modalità lightweight. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Registrare una macchina virtuale SQL Server in modalità Lightweight con l'interfaccia della riga di comando di Azure: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrare una macchina virtuale SQL Server in modalità Lightweight con PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Modalità di gestione completa


Se l'estensione SQL IaaS è già stata installata manualmente nella macchina virtuale, è possibile registrare la VM SQL Server in modalità completa senza riavviare il servizio SQL Server. **Tuttavia, se l'estensione IaaS di SQL non è stata installata, la registrazione in modalità completa installerà l'estensione IaaS di SQL in modalità completa e riavvierà il servizio SQL Server. Procedere con cautela.**


Per registrare la macchina virtuale SQL Server direttamente in modalità completa ed eventualmente riavviare il servizio SQL Server, usare il comando PowerShell seguente: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Modalità di gestione NoAgent 

SQL Server 2008 e 2008 R2 installati in Windows Server 2008 (_non R2_) possono essere registrati con il provider di risorse macchine virtuali SQL in modalità [NoAgent](#management-modes). Questa opzione assicura la conformità e consente il monitoraggio della macchina virtuale SQL Server nel portale di Azure con funzionalità limitate.

Specificare `AHUB`, `PAYG`o `DR` come **sqlLicenseType**e `SQL2008-WS2008` o `SQL2008R2-WS2008`come **sqlImageOffer**. 

Per registrare SQL Server 2008 o 2008 R2 nell'istanza di Windows Server 2008, usare il frammento di codice dell'interfaccia della riga di comando di Azure o di PowerShell seguente: 


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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrare la macchina virtuale di SQL Server 2008 in modalità noagent con PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registrare la macchina virtuale di SQL Server 2008 R2 in modalità noagent con PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full"></a>Aggiorna a completo  

Le macchine virtuali SQL Server IaaS *semplificata* installata possono essere aggiornate alla modalità _completa_ usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. Le macchine virtuali SQL Server in modalità _NoAgent_ possono eseguire l'aggiornamento alla modalità _completa_ dopo l'aggiornamento del sistema operativo a Windows 2008 R2 e versioni successive. Non è possibile effettuare il downgrade. Per farlo, è necessario [annullare la registrazione](#unregister-from-rp) della macchina virtuale SQL Server dal provider di risorse della macchina virtuale SQL. Questa operazione rimuoverà la **risorsa** _macchina virtuale di SQL Server_ ma non eliminerà la macchina virtuale in sé. 

È possibile visualizzare la modalità corrente dell'agente di SQL Server IaaS usando PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Per eseguire l'aggiornamento alla modalità completa dell'agente: 


### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Andare alla risorsa [macchine virtuali SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). 
1. Selezionare la macchina virtuale SQL Server e scegliere **Panoramica**. 
1. Per le macchine virtuali di SQL Server in modalità IaaS NoAgent o semplificata, selezionare il messaggio **Solo il tipo di licenza e gli aggiornamenti dell'edizione sono disponibili con l'estensione IaaS di SQL**.

   ![Selezioni per modificare la modalità dal portale](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Selezionare la casella di controllo **Accetto di riavviare il servizio SQL Server nella macchina virtuale**, quindi **Conferma** per eseguire l'aggiornamento alla modalità IaaS completa. 

    ![Selezionare la casella per accettare di riavviare il servizio SQL Server nella macchina virtuale](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)

Eseguire il frammento di codice dell'interfaccia della riga di comando di Azure seguente:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Eseguire il frammento di codice di PowerShell seguente per:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verificare lo stato di registrazione
È possibile verificare se la macchina virtuale di SQL Server è già stata registrata con il provider di risorse macchine virtuali SQL usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

### <a name="azure-portal"></a>Portale di Azure 

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle [macchine virtuali SQL Server](manage-sql-vm-portal.md).
1. Selezionare la macchina virtuale di SQL Server dall'elenco. Se la macchina virtuale di SQL Server non è inclusa nell'elenco, è probabile che non sia stata registrata con il provider di risorse della macchina virtuale SQL. 
1. Visualizzare il valore in **Stato**. Se lo **Stato** è **Riuscito**, la macchina virtuale di SQL Server è stata registrata correttamente con il provider di risorse macchine virtuali SQL. 

   ![Verificare lo stato con la registrazione del provider di risorse SQL](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Riga di comando

Verificare lo stato di registrazione della macchina virtuale SQL Server corrente usando l'interfaccia della riga di comando di Azure o PowerShell. `ProvisioningState` mostrerà `Succeeded` se la registrazione è riuscita. 

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

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

   ![Eliminare un provider di risorse per macchine virtuali SQL](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Digitare il nome della macchina virtuale SQL e **deselezionare la casella di controllo accanto alla macchina virtuale**.

   ![Eliminare un provider di risorse per macchine virtuali SQL](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Se non si deseleziona la casella di controllo accanto al nome della macchina virtuale, la macchina virtuale verrà *eliminata* completamente. Deselezionare la casella di controllo per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse, operazione che *non eliminerà la macchina virtuale effettiva*. 

1. Selezionare **Elimina** per confermare l'eliminazione della *risorsa*della macchina virtuale SQL e non della VM SQL Server. 

### <a name="command-line"></a>Riga di comando

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
Per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse con l'interfaccia della riga di comando di Azure, usare il comando [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Verrà rimossa la *risorsa* della macchina virtuale SQL Server ma la macchina virtuale non verrà eliminata. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per annullare la registrazione della macchina virtuale SQL Server dal provider di risorse con PowerShell, usare il comando [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm). Verrà rimossa la *risorsa* della macchina virtuale SQL Server ma la macchina virtuale non verrà eliminata. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Limitazioni

Il provider di risorse di macchine virtuali SQL supporta solo:
- Macchine virtuali di SQL Server distribuite tramite Azure Resource Manager. Le macchine virtuali di SQL Server distribuite tramite il modello classico non sono supportate. 
- Macchine virtuali di SQL Server distribuite nel cloud pubblico o Azure per enti pubblici. Le distribuzioni in altri cloud privati o governativi non sono supportate. 


## <a name="frequently-asked-questions"></a>Domande frequenti 

**È necessario registrare la macchina virtuale di SQL Server sottoposta a provisioning da un'immagine di SQL Server in Azure Marketplace?**

No. Microsoft registra automaticamente le macchine virtuali di cui è stato eseguito il provisioning dalle immagini di SQL Server in Azure Marketplace. La registrazione con il provider di risorse macchine virtuali SQL è necessaria solo se la macchina virtuale *non* è stata sottoposta a provisioning dalle immagini SQL Server in Azure Marketplace e SQL Server è stato installato autonomamente.

**Il provider di risorse di macchine virtuali SQL è disponibile per tutti i clienti?** 

Sì. I clienti devono registrare le proprie macchine virtuali SQL Server con il provider di risorse macchine virtuali SQL se non hanno usato un'immagine di SQL Server da Azure Marketplace ma dispongono invece di SQL Server installato automaticamente o hanno usato un disco rigido virtuale personalizzato. Le macchine virtuali di proprietà di tutti i tipi di sottoscrizioni (Direct, Contratto Enterprise e Cloud Solution Provider) possono eseguire la registrazione con il provider di risorse macchine virtuali SQL.

**È necessario eseguire la registrazione con il provider di risorse macchine virtuali SQL se per la macchina virtuale di SQL Server è già installata l'estensione IaaS di SQL Server?**

Se la macchina virtuale di SQL Server è installata e non viene sottoposta a provisioning dalle immagini di SQL Server in Azure Marketplace, è necessario effettuarne la registrazione con il provider di risorse macchine virtuali SQL anche se è stata installata l'estensione IaaS di SQL Server. La registrazione con il provider di risorse VM SQL crea una nuova risorsa di tipo Microsoft. SqlVirtualMachine. L'installazione dell'estensione IaaS di SQL Server non crea tale risorsa.

**Qual è la modalità di gestione predefinita durante la registrazione con il provider di risorse macchine virtuali SQL?**

La modalità di gestione predefinita durante la registrazione con il provider di risorse macchine virtuali SQL è quella *completa*. Se la proprietà di gestione di SQL Server non è impostata quando si esegue la registrazione con il provider di risorse macchine virtuali SQL, la modalità verrà impostata come gestibilità completa e il servizio di SQL Server verrà riavviato. Si consiglia di eseguire prima la registrazione con il provider di risorse di macchine virtuali SQL in modalità semplificata, quindi di eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione. 

**Quali sono i prerequisiti per la registrazione con il provider di risorse macchine virtuali SQL?**

Non sono previsti prerequisiti per la registrazione con il provider di risorse macchine virtuali SQL in modalità semplificata o senza agente. Il prerequisito per la registrazione con il provider di risorse macchine virtuali SQL in modalità completa prevede l'installazione dell'estensione IaaS di SQL nella macchina virtuale, in caso contrario il servizio SQL Server verrà riavviato. 

**È possibile eseguire la registrazione con il provider di risorse macchine virtuali SQL se sulla macchina virtuale è già installata l'estensione IaaS di SQL Server?**

Sì, è possibile eseguire la registrazione con il provider di risorse macchine virtuali SQL in modalità di gestione semplificata se nella macchina virtuale non è installata l'estensione IaaS di SQL Server. In modalità semplificata, il provider di risorse macchine virtuali SQL userà un'app console per verificare la versione e l'edizione dell'istanza di SQL Server. 

La modalità di gestione predefinita di SQL durante la registrazione con il provider di risorse macchine virtuali SQL è quella _completa_. Se la proprietà di gestione SQL non è impostata quando si esegue la registrazione con il provider di risorse macchine virtuali SQL, la modalità verrà impostata come Gestibilità completa. Si consiglia di eseguire prima la registrazione con il provider di risorse di macchine virtuali SQL in modalità semplificata, quindi di eseguire l'aggiornamento alla modalità completa durante una finestra di manutenzione. 

**La registrazione con il provider di risorse macchine virtuali SQL installerà un agente nella macchina virtuale?**

Sì. la registrazione con il provider di risorse VM SQL installerà un agente nella macchina virtuale.

Il SQL Server estensione IaaS si basa sull'agente per eseguire una query sui metadati per SQL Server. L'unico momento in cui un agente non è installato è quando il provider di risorse VM SQL è regsitered in modalità noagent

**La registrazione con il provider di risorse VM SQL riavvierà SQL Server nella macchina virtuale?**

Dipende dalla modalità specificata durante la registrazione. Se si specifica la modalità semplificata o NoAgent, il servizio SQL Server non verrà riavviato. Tuttavia, se si specifica la modalità di gestione completa o se si lascia vuota, verrà installata l'estensione IaaS di SQL in modalità di gestione completa, cosa che comporterà il riavvio del servizio SQL Server. 

**Qual è la differenza tra le modalità di gestione semplificata e senza agente durante la registrazione con il provider di risorse macchine virtuali SQL?** 

La modalità di gestione senza agente è disponibile solo per SQL Server 2008 e SQL Server 2008 R2 in Windows Server 2008. Si tratta dell'unica modalità di gestione disponibile per queste versioni. Per tutte le altre versioni di SQL Server, le due modalità di gestione disponibili sono la modalità semplificata e la modalità completa. 

Per la modalità senza agente, il cliente deve configurare le proprietà di versione ed edizione di SQL Server. La modalità semplificata esegue una query sulla macchina virtuale per trovare la versione e l'edizione dell'istanza di SQL Server.

**È possibile eseguire la registrazione con il provider di risorse macchine virtuali SQL senza specificare il tipo di licenza di SQL Server?**

No. Il tipo di licenza di SQL Server non è una proprietà facoltativa quando si esegue la registrazione con il provider di risorse della macchina virtuale SQL. È necessario impostare il tipo di licenza SQL Server come con pagamento in base al consumo o Vantaggio Azure Hybrid quando si esegue la registrazione con il provider di risorse macchine virtuali SQL in tutte le modalità di gestibilità (senza agente, semplificata e completa).

**È possibile aggiornare l'estensione IaaS di SQL Server dalla modalità senza agente alla modalità completa?**

No. L'aggiornamento della modalità di gestibilità a quella completa o semplificata non è disponibile per la modalità senza agente. Si tratta di una limitazione tecnica di Windows Server 2008. Sarà necessario aggiornare prima il sistema operativo a Windows Server 2008 R2 o una versione successiva per potere poi eseguire l'aggiornamento alla modalità di gestione completa. 

**È possibile aggiornare l'estensione IaaS di SQL Server dalla modalità semplificata alla modalità completa?**

Sì. L'aggiornamento della modalità di gestibilità semplificata a quella completa è supportato tramite PowerShell o il portale di Azure. Richiede il riavvio del servizio SQL Server.

**È possibile eseguire il downgrade dell'estensione IaaS di SQL Server dalla modalità completa a quella senza agente o semplificata?**

No. Il downgrade della modalità di gestibilità dell'estensione IaaS di SQL Server non è supportato. Non è possibile eseguire il downgrade né dalla modalità di gestibilità completa alla modalità semplificata o senza agente, né dalla modalità semplificata alla modalità senza agente. 

Per modificare la modalità di gestibilità dalla gestione completa, [annullare la registrazione](#unregister-from-rp) della macchina virtuale SQL Server dal provider di risorse VM SQL eliminando la *risorsa* SQL Server e ripetere la registrazione della macchina virtuale SQL Server con il provider di risorse VM SQL di nuovo in una modalità di gestione diversa.

**È possibile eseguire la registrazione con il provider di risorse macchine virtuali SQL dal portale di Azure?**

No. La registrazione con il provider di risorse macchine virtuali SQL non è disponibile nel portale di Azure. La registrazione con il provider di risorse macchine virtuali SQL è supportata solo con l'interfaccia della riga di comando di Azure o PowerShell. 

**È possibile registrare una macchina virtuale con il provider di risorse macchine virtuali SQL prima di installare SQL Server?**

No. Una macchina virtuale deve avere almeno un'istanza di SQL Server (motore di database) per eseguire correttamente la registrazione con il provider di risorse macchine virtuali SQL. Se non è presente alcuna istanza di SQL Server nella macchina virtuale, la nuova risorsa Microsoft.SqlVirtualMachine avrà uno stato di errore.

**È possibile registrare una macchina virtuale con il provider di risorse macchine virtuali SQL se sono presenti più istanze di SQL Server?**

Sì. Il provider di risorse macchine virtuali SQL registrerà una sola istanza di SQL Server (motore di database). Il provider di risorse macchine virtuali SQL registrerà l'istanza predefinita di SQL Server nel caso di più istanze. Se non è presente alcuna istanza predefinita, è supportata solo la registrazione in modalità semplificata. Per eseguire l'aggiornamento dalla modalità semplificata alla gestione completa, è necessario che sia presente l'istanza predefinita di SQL Server o che la macchina virtuale disponga di una sola istanza denominata SQL Server.

**È possibile registrare un'istanza del cluster di failover di SQL Server con il provider di risorse macchine virtuali SQL?**

Sì. Le istanze del cluster di failover di SQL Server in una macchina virtuale di Azure possono essere registrate con il provider di risorse macchine virtuali SQL in modalità semplificata. Tuttavia, le istanze del cluster di failover di SQL Server non possono essere aggiornate alla modalità di gestione completa.

**È possibile registrare la macchina virtuale con il provider di risorse macchine virtuali SQL se è configurato un gruppo di disponibilità Always On?**

Sì. Non sono presenti restrizioni per la registrazione di un'istanza di SQL Server in una macchina virtuale di Azure con il provider di risorse macchine virtuali SQL se si partecipa a una configurazione del gruppo di disponibilità Always On.

**Qual è il costo per la registrazione con il provider di risorse macchine virtuali SQL o con l'aggiornamento alla modalità di gestibilità completa?**
No. Non sono previste tariffe associate alla registrazione con il provider di risorse macchine virtuali SQL o con una delle tre modalità di gestibilità. La gestione della macchina virtuale SQL Server con il provider di risorse è completamente gratuita. 

**Qual è l'effetto sulle prestazioni dell'uso delle diverse modalità di gestibilità?**
Non vi è alcun effetto quando si usano le modalità di gestione *NoAgent* e *semplificata*. Quando si usa la modalità di gestione *completa* da due servizi installati nel sistema operativo, l'effetto è minimo. Questi servizi sono monitorabili tramite Gestione attività e possono essere visualizzati nella console Servizi Windows incorporata. 

I due nomi di servizio sono:
- `SqlIaaSExtensionQuery` (Nome visualizzato: `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (Nome visualizzato: `Microsoft SQL Server IaaS Agent`)


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)  
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)
