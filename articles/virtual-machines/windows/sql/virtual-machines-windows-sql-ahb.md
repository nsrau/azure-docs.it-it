---
title: Modificare il modello di licenza per una macchina virtuale SQL Server in Azure
description: Informazioni su come cambiare le licenze per una macchina virtuale SQL Server in Azure con pagamento in base al consumo per bring your own License usando il Vantaggio Azure Hybrid.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5d740224adb15dc2d772689d77ab1c510532c901
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203993"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Modificare il modello di licenza per una macchina virtuale SQL Server in Azure
Questo articolo descrive come modificare il modello di licenza per un SQL Server macchina virtuale (VM) in Azure usando il nuovo provider di risorse VM SQL, **Microsoft. SqlVirtualMachine**.

Esistono due modelli di licenza per una macchina virtuale che ospita SQL Server: con pagamento in base al consumo e Vantaggio Azure Hybrid. È possibile modificare il modello di licenza della macchina virtuale SQL Server usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

Il modello con pagamento in base al consumo indica che il costo al secondo dell'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.
[Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza SQL Server con una macchina virtuale che esegue SQL Server. 

Vantaggio Azure Hybrid consente l'uso di licenze SQL Server con Software Assurance ("licenza qualificata") in macchine virtuali di Azure. Con Vantaggio Azure Hybrid, ai clienti non viene addebitato l'uso di una licenza di SQL Server in una macchina virtuale. Ma devono comunque pagare il costo del calcolo cloud sottostante, ovvero la tariffa di base, l'archiviazione e i backup. Devono anche pagare l'I/O associato all'uso dei servizi (come applicabile).

Secondo le condizioni del prodotto Microsoft: "I clienti devono indicare che usano il database SQL di Azure (Istanza gestita, Pool elastico e Database singolo), Azure Data Factory, SQL Server Integration Services o SQL Server macchine virtuali in Vantaggio Azure Hybrid per SQL Server durante la configurazione carichi di lavoro in Azure. "

Per indicare l'uso di Vantaggio Azure Hybrid per SQL Server in una macchina virtuale di Azure ed essere conformi, sono disponibili tre opzioni:

- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server Bring your own License da Azure Marketplace. Questa opzione è disponibile solo per i clienti che dispongono di un Enterprise Agreement.
- Esegui il provisioning di una macchina virtuale usando un'immagine di SQL Server con pagamento in base al consumo da Azure Marketplace e attiva Vantaggio Azure Hybrid.
- Installare autonomamente SQL Server in una macchina virtuale di Azure, [registrare manualmente la vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md)e attivare vantaggio Azure Hybrid.

Il tipo di licenza di SQL Server viene impostato quando viene effettuato il provisioning della macchina virtuale. È possibile modificarlo in qualsiasi momento in seguito. Il cambio tra modelli di licenza non comporta tempi di inattività, non riavvia la macchina virtuale, non aggiunge alcun costo aggiuntivo ed è immediatamente efficace. Di fatto, l'attivazione di Vantaggio Azure Hybrid *riduce* i costi.

## <a name="prerequisites"></a>Prerequisiti

L'uso del provider di risorse VM SQL richiede l'SQL Server estensione IaaS. Di conseguenza, sono necessari gli elementi seguenti:
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Una [macchina virtuale SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il [provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md).


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Modificare la licenza per le macchine virtuali già registrate con il provider di risorse 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di licenza direttamente dal portale: 

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire la [risorsa macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) per la SQL Server VM. 
1. Selezionare **Configura** in **Impostazioni**. 
1. Selezionare l'opzione **vantaggio Azure Hybrid** e selezionare la casella di controllo per confermare di disporre di una licenza SQL Server con Software Assurance. 
1. Selezionare **applica** nella parte inferiore della pagina **Configura** . 

![Vantaggio Azure Hybrid nel portale](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per modificare il modello di licenza, è possibile usare l'interfaccia della riga di comando di Azure.  

Il frammento di codice seguente passa il modello di licenza con pagamento in base al consumo a bring your own License (o usando Vantaggio Azure Hybrid):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Il frammento di codice seguente passa il modello Bring-your-own-License al modello con pagamento in base al consumo: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
È possibile usare PowerShell per modificare il modello di licenza.

Il frammento di codice seguente passa il modello di licenza con pagamento in base al consumo a bring your own License (o usando Vantaggio Azure Hybrid):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Il frammento di codice seguente passa il modello Bring-your-own-License al modello con pagamento in base al consumo:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Modificare la licenza per le macchine virtuali non registrate con il provider di risorse

Se è stato effettuato il provisioning di una macchina virtuale SQL Server dalle immagini di Azure Marketplace con pagamento in base al consumo, il tipo di licenza SQL Server sarà con pagamento in base al consumo. Se è stato effettuato il provisioning di una macchina virtuale SQL Server usando un'immagine Bring your own License da Azure Marketplace, il tipo di licenza sarà vantaggio Ahu. Tutte le macchine virtuali SQL Server sottoposte a provisioning dalle immagini predefinite (con pagamento in base al consumo) o bring your own License di Azure Marketplace verranno automaticamente registrate con il provider di risorse VM SQL, in modo che possano modificare il [tipo di licenza](#change-the-license-for-vms-already-registered-with-the-resource-provider).

L'utente è idoneo per l'installazione automatica di SQL Server in una macchina virtuale di Azure tramite Vantaggio Azure Hybrid. È necessario [registrare queste VM con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md) impostando la licenza di SQL Server come vantaggio Azure Hybrid per indicare l'utilizzo vantaggio Azure Hybrid in base alle condizioni del prodotto Microsoft.

È possibile modificare il tipo di licenza di una macchina virtuale SQL Server come con pagamento in base al consumo o Vantaggio Azure Hybrid solo se la VM SQL Server è registrata con il provider di risorse della macchina virtuale SQL. Tutte le macchine virtuali SQL Server devono essere registrate con il provider di risorse per la conformità delle licenze.

## <a name="remarks"></a>Note

- I clienti di Azure Cloud Solution Provider (CSP) possono usare il Vantaggio Azure Hybrid distribuendo prima di tutto una macchina virtuale con pagamento in base al consumo e quindi convertirla in Bring your own License se hanno Software Assurance attivo.
- Se si elimina la risorsa SQL Server VM, si tornerà all'impostazione di licenza hardcoded dell'immagine. 
- La possibilità di modificare il modello di licenza è una funzionalità del provider di risorse della macchina virtuale SQL. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale SQL Server con il provider di risorse. Tuttavia, i clienti che eseguono l'installazione automatica di SQL Server dovranno [registrare manualmente la macchina virtuale SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Per aggiungere una macchina virtuale SQL Server a un set di disponibilità è necessario ricreare la macchina virtuale. Di conseguenza, tutte le macchine virtuali aggiunte a un set di disponibilità tornano al tipo di licenza con pagamento in base al consumo predefinito. Vantaggio Azure Hybrid sarà necessario abilitarla di nuovo. 


## <a name="limitations"></a>Limitazioni

- La modifica del modello di licenza è disponibile solo per i clienti che dispongono di Software Assurance.
- La modifica del modello di licenza è supportata solo per le edizioni standard ed Enterprise di SQL Server. Le modifiche alle licenze per Express, Web e Developer non sono supportate. 
- La modifica del modello di licenza è supportata solo per le macchine virtuali distribuite tramite il modello di Azure Resource Manager. Le macchine virtuali distribuite con il modello classico non sono supportate. È possibile eseguire la migrazione della macchina virtuale dal modello classico al modello di Gestione risorse e registrarla con il provider di risorse della macchina virtuale SQL. Dopo che la macchina virtuale è stata registrata con il provider di risorse VM SQL, le modifiche al modello di licenza saranno disponibili nella macchina virtuale.
- La modifica del modello di licenza è abilitata solo per le installazioni di cloud pubblico.
- La modifica del modello di licenza è supportata solo nelle macchine virtuali con una singola scheda di interfaccia di rete. Nelle macchine virtuali che dispongono di più schede di interfaccia di rete, è necessario prima di tutto rimuovere una delle schede di interfaccia di rete (usando il portale di Azure) prima di eseguire la procedura. In caso contrario, si otterrà un errore simile al seguente: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Sebbene sia possibile aggiungere nuovamente la scheda di interfaccia di rete alla macchina virtuale dopo aver modificato il modello di licenza, le operazioni eseguite tramite la pagina di configurazione SQL Server nel portale di Azure, come l'applicazione automatica di patch e il backup, non saranno più considerate supportate.

## <a name="known-errors"></a>Errori noti

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>La risorsa ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group >' nel gruppo di risorse\<' Resource-Group >' non è stata trovata.
Questo errore si verifica quando si tenta di modificare il modello di licenza in una macchina virtuale SQL Server che non è stata registrata con il provider di risorse VM SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

È necessario registrare il provider di risorse nella [sottoscrizione](virtual-machines-windows-sql-register-with-resource-provider.md#register-the-sql-vm-resource-provider-with-a-subscription)e quindi [registrare la macchina virtuale SQL Server con il provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Non è possibile convalidare l'argomento per il parametro 'Sku'
Questo errore può verificarsi quando si tenta di modificare il modello di licenza SQL Server VM utilizzando Azure PowerShell versioni successive alla 4,0:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Per correggere l'errore, rimuovere il commento dalle righe nel frammento di codice di PowerShell indicato in precedenza quando si cambia il modello di licenza:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Usare il codice seguente per verificare la versione di Azure PowerShell:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


