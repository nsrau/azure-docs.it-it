---
title: Come modificare il modello di licenza per una macchina virtuale SQL Server in Azure
description: Informazioni su come cambiare le licenze per una macchina virtuale SQL in Azure da' con pagamento in base al consumo ' a' bring your own License ' usando il Vantaggio Azure Hybrid.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 37457d8ce1189f9282f4763633e944e3c2d639c9
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816729"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Come modificare il modello di licenza per una macchina virtuale SQL Server in Azure
Questo articolo descrive come cambiare il modello di licenza per una macchina virtuale di SQL Server in Azure usando il nuovo provider di risorse della macchina virtuale di SQL **Microsoft.SqlVirtualMachine**.

Esistono due modelli di licenza per una macchina virtuale (VM) che ospita SQL Server con pagamento in base al consumo e Vantaggio Azure Hybrid (vantaggio Azure Hybrid). A questo punto, usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell è possibile modificare il modello di licenza della VM SQL Server. 

Il modello con **pagamento in base** al consumo (PAYG) indica che il costo al secondo dell'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.
Il [vantaggio Azure Hybrid (vantaggio Azure Hybrid)](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza SQL Server con una macchina virtuale che esegue SQL Server. 

Microsoft Azure vantaggio ibrido per SQL Server consente l'uso di licenze SQL Server con Software Assurance ("licenza qualificata") in macchine virtuali di Azure. Con Vantaggio Azure Hybrid per SQL Server, ai clienti non verrà addebitato l'utilizzo della licenza di SQL Server nella macchina virtuale, ma devono comunque pagare il costo del calcolo cloud sottostante (ovvero la tariffa di base), l'archiviazione e i backup, nonché l'I/O associato alla loro u Se dei servizi (come applicabile).

Secondo le condizioni del prodotto Microsoft, i clienti devono indicare che usano il database SQL di Azure (Istanza gestita, Pool elastico e Database singolo), Azure Data Factory, SQL Server Integration Services o SQL Server macchine virtuali in Azure Hybrid Vantaggi per SQL Server durante la configurazione dei carichi di lavoro in Azure. "

Per indicare l'uso della Vantaggio Azure Hybrid per SQL Server nella macchina virtuale di Azure ed essere conformi, sono disponibili tre opzioni:

1. Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server BYOL da Azure Marketplace, disponibile solo per i clienti con Enterprise Agreement.
1. Eseguire il provisioning di una macchina virtuale usando un'immagine di SQL Server PAYG da Azure Marketplace e attivare vantaggio Azure Hybrid.
1. Installare autonomamente SQL Server in una macchina virtuale di Azure, [registrare manualmente la vm SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) e attivare vantaggio Azure Hybrid.

Il tipo di licenza di SQL Server viene impostato quando viene effettuato il provisioning della macchina virtuale e può essere modificato in un secondo momento. Il cambio tra modelli di licenza non comporta **tempi**di inattività, non riavvia la macchina virtuale, non aggiunge **alcun costo aggiuntivo** (in effetti, l'attivazione di vantaggio Azure Hybrid *riduce* i costi) ed è **immediatamente efficace**. 

## <a name="prerequisites"></a>Prerequisiti

Per usare il provider di risorse della macchina virtuale di SQL è necessaria l'estensione IaaS SQL. Per procedere quindi con l'utilizzo del provider di risorse della macchina virtuale di SQL, è necessario quanto segue:
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Una [macchina virtuale SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il [provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md) installato. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Modificare la licenza per le macchine virtuali già registrate con il provider di risorse 

# <a name="azure-portaltabazure-portal"></a>[Portale di Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di gestione delle licenze direttamente dal portale. 

1. Aprire il [portale di Azure](https://portal.azure.com) e avviare la [risorsa macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) per la SQL Server VM. 
1. Selezionare **Configura** in **Impostazioni**. 
1. Selezionare l'opzione **vantaggio Azure Hybrid** e selezionare la casella di controllo per confermare di disporre di una licenza SQL Server con Software Assurance. 
1. Selezionare **applica** nella parte inferiore della pagina **Configura** . 

![VANTAGGIO Azure Hybrid nel portale](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[Interfaccia della riga di comando AZ](#tab/bash)

È possibile usare l'interfaccia della riga di comando di Azure per modificare il modello di licenza.  

Il frammento di codice seguente passa il modello di licenza con pagamento in base al consumo a BYOL (o usando Vantaggio Azure Hybrid):

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

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
È possibile usare PowerShell per modificare il modello di licenza.

Il frammento di codice seguente passa il modello di licenza con pagamento in base al consumo a BYOL (o usando Vantaggio Azure Hybrid):

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

Il frammento di codice seguente passa il modello di BYOL a pagamento in base al consumo:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Modificare la licenza per le macchine virtuali non registrate con il provider di risorse

Se è stato effettuato il provisioning di una macchina virtuale SQL Server dalle immagini di PAYG Azure Marketplace, il tipo di licenza SQL sarà PAYG. Se è stato effettuato il provisioning di una macchina virtuale SQL Server usando un'immagine BYOL da Azure Marketplace, il tipo di licenza sarà vantaggio Ahu. Tutte le macchine virtuali SQL Server sottoposte a provisioning da immagini predefinite (PAYG) o BYOL di Azure Marketplace verranno registrate automaticamente con il provider di risorse VM SQL, in modo che possano modificare il [tipo di licenza](#change-license-for-vms-already-registered-with-resource-provider)

Si è idonei per l'installazione automatica di SQL Server in una macchina virtuale di Azure tramite Vantaggio Azure Hybrid ed è necessario [registrare queste VM con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md) impostando la licenza di SQL Server come vantaggio Azure Hybrid per indicare l'utilizzo di vantaggio Azure Hybrid in base alle condizioni del prodotto Microsoft.

È possibile modificare solo il tipo di licenza di una macchina virtuale SQL Server come PAYG o vantaggio Azure Hybrid se la VM SQL è registrata con il provider di risorse VM SQL; e tutte le VM SQL devono essere registrate con la macchina virtuale SQL RP per la conformità delle licenze.

## <a name="remarks"></a>Note

 - I clienti di Azure Cloud Solution Partner (CSP) possono usare i Vantaggio Azure Hybrid distribuendo prima una macchina virtuale con pagamento in base al consumo e quindi convertendo il software in Bring your own License se hanno una SA attiva.
 - Se si elimina la risorsa SQL Server VM, si tornerà all'impostazione di licenza hardcoded dell'immagine. 
  - La possibilità di modificare il modello di licenza è una funzionalità del provider di risorse della macchina virtuale SQL. La distribuzione di un'immagine del Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale SQL Server con il provider di risorse. Tuttavia, i clienti che eseguono l'installazione automatica SQL Server dovranno registrare manualmente [la macchina virtuale SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Per aggiungere una macchina virtuale SQL Server a un set di disponibilità è necessario ricreare la macchina virtuale. Di conseguenza, tutte le macchine virtuali aggiunte a un set di disponibilità restituiranno il tipo di licenza predefinito con pagamento in base al consumo e vantaggio Azure Hybrid sarà necessario abilitarlo di nuovo. 


## <a name="limitations"></a>Limitazioni

 - La modifica del modello di gestione delle licenze è disponibile solo per i clienti con Software Assurance.
 - La modifica del modello di gestione licenze è supportata solo per le edizioni standard ed Enterprise di SQL Server. Le modifiche alle licenze per Express, Web e Developer non sono supportate. 
 - La modifica del modello di gestione licenze è supportata solo per le macchine virtuali distribuite con il modello di Gestione risorse. Le macchine virtuali distribuite con il modello classico non sono supportate. È possibile eseguire la migrazione della macchina virtuale dal modello di distribuzione classica a Resource Manager (ARM) e registrarla con il provider di risorse VM SQL. Dopo che la VM è stata registrata con il provider di risorse VM SQL, le modifiche al modello di licenza saranno disponibili nella macchina virtuale. 
 - La modifica del modello di gestione licenze è abilitata solo per le installazioni di cloud pubblico.
 - La modifica del modello di gestione delle licenze è supportata solo nelle macchine virtuali con una singola scheda di interfaccia di rete. Nelle macchine virtuali che dispongono di più schede di interfaccia di rete, è necessario prima di tutto rimuovere una delle schede di interfaccia di rete (usando il portale di Azure) prima di eseguire la procedura. In caso contrario, verrà eseguito un errore simile al seguente: `The virtual machine '\<vmname\>' has more than one NIC associated.`Sebbene sia possibile aggiungere nuovamente la scheda di interfaccia di rete alla macchina virtuale dopo aver modificato la modalità di gestione delle licenze, le operazioni eseguite tramite la pagina di configurazione SQL nel portale di Azure, ad esempio l'applicazione automatica di patch e il backup, non saranno più considerate supportate.


## <a name="known-errors"></a>Errori noti

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La risorsa ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group >' nel gruppo di risorse\<' Resource-Group >' non è stata trovata. Impossibile trovare la proprietà' sqlServerLicenseType ' in questo oggetto. Verificare che la proprietà esista e che sia possibile impostarla.
Questo errore si verifica quando si tenta di modificare il modello di licenza in una macchina virtuale SQL Server che non è stata registrata con il provider di risorse della macchina virtuale SQL. È necessario registrare il provider di risorse nella [sottoscrizione](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)e quindi registrare la macchina virtuale SQL Server con il [provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md)SQL. 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Non è possibile convalidare l'argomento per il parametro 'Sku'
Questo errore può verificarsi quando si tenta di modificare il modello di licenza della macchina virtuale SQL Server quando si usa Azure PowerShell > 4,0:`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Per risolvere questo errore, rimuovere il commento di queste righe nel frammento di codice PowerShell indicato in precedenza quando si cambia il modello di licenza:

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

Per altre informazioni, vedere i seguenti articoli: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


