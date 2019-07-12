---
title: Come modificare modello di licenza per una VM di SQL Server in Azure
description: Informazioni su come modificare le licenze per una macchina virtuale SQL in Azure da 'pagamento a consumo' a 'bring-your-own-license' con il vantaggio Azure Hybrid.
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 78ad784a45d2b0063932791daedc9b1ec1aafd72
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786770"
---
# <a name="how-to-change-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Come modificare modello di licenza per una macchina virtuale di SQL Server in Azure
Questo articolo descrive come cambiare il modello di licenza per una macchina virtuale di SQL Server in Azure usando il nuovo provider di risorse della macchina virtuale di SQL **Microsoft.SqlVirtualMachine**.

Esistono due modelli di licenza per una macchina virtuale (VM) che ospita SQL Server - pagamento a consumo e Azure ibrido vantaggio (vantaggio Azure Hybrid). E a questo punto, tramite il portale di Azure, della riga di comando di Azure o PowerShell è possibile modificare modello di licenza di VM di SQL Server. 

Il **pagamento a consumo** modello (PAYG) significa che il costo al secondo dell'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.
Il [vantaggio Hybrid Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza di SQL Server con una macchina virtuale che esegue SQL Server. 

Vantaggio Microsoft Azure Hybrid per SQL Server consente di usare licenze di SQL Server con Software Assurance ("licenza completo") in macchine virtuali di Azure. Con vantaggio Azure Hybrid per SQL Server, i clienti non verranno addebitati per l'utilizzo della licenza di SQL Server nella macchina virtuale, ma devono comunque pagare il costo del calcolo cloud sottostanti (vale a dire, la tariffa di base), archiviazione e backup, nonché i/o associati loro u Se i servizi (se applicabile).

In base alle condizioni per i prodotti Microsoft "devono indicare ai clienti che utilizzano Database SQL di Azure (istanza gestita di Azure, Pool elastici e Database singolo), Data Factory di Azure, SQL Server Integration Services o macchine virtuali SQL Server in Azure Hybrid Vantaggio per SQL Server quando si configurano i carichi di lavoro di Azure."

Per indicare l'uso del vantaggio Azure Hybrid per SQL Server in macchine Virtuali di Azure ed essere conformi, sono disponibili tre opzioni:

1. Effettuare il provisioning di una macchina virtuale usando un'immagine BYOL di SQL Server da Azure marketplace, disponibile solo per i clienti con contratto Enterprise Agreement.
1. Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server in base al consumo da Azure marketplace e attivare vantaggio Azure Hybrid.
1. Self-installare manualmente SQL Server in una VM di Azure [registrare le VM di SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md) e attivare vantaggio Azure Hybrid.

Tipo di licenza di SQL Server viene impostato quando la macchina virtuale viene eseguito il provisioning e può essere modificata in qualsiasi momento in un secondo momento. Passaggio tra modelli di licenza comporta **senza tempi di inattività**, riavviare la VM, aggiunge **senza costi aggiuntivi** (in effetti, l'attivazione vantaggio Azure Hybrid *riduce* costo) ed è **validità immediata**. 

## <a name="prerequisites"></a>Prerequisiti

Per usare il provider di risorse della macchina virtuale di SQL è necessaria l'estensione IaaS SQL. Per procedere quindi con l'utilizzo del provider di risorse della macchina virtuale di SQL, è necessario quanto segue:
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- [Assurance software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Oggetto [VM di SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il [provider di risorse di VM di SQL](virtual-machines-windows-sql-register-with-resource-provider.md) installato. 


## <a name="change-license-for-vms-already-registered-with-resource-provider"></a>Modifica licenza per le macchine virtuali è già registrato con provider di risorse 

# <a name="azure-portaltabazure-portal"></a>[Portale di Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di licenza direttamente dal portale. 

1. Aprire il [portale di Azure](https://portal.azure.com) e avviare la [risorse delle macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) per VM di SQL Server. 
1. Selezionare **configurare** sotto **impostazioni**. 
1. Selezionare il **vantaggio Azure Hybrid** opzione e selezionare la casella di controllo per confermare che si abbia una licenza di SQL Server con Software Assurance. 
1. Selezionare **Apply** in fondo il **configura** pagina. 

![Vantaggio Azure Hybrid nel portale](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

È possibile usare l'interfaccia della riga di comando di Azure per modificare il modello di licenza.  

Il seguente frammento di codice passa il modello di licenza con pagamento a consumo a BYOL (o tramite il vantaggio Azure Hybrid):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Il seguente frammento di codice passa il modello bring-your-own-license in base al consumo: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
È possibile usare PowerShell per modificare il modello di licenza.

Il seguente frammento di codice passa il modello di licenza con pagamento a consumo a BYOL (o tramite il vantaggio Azure Hybrid):

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

Il seguente frammento di codice attiva il modello BYOL di pagamento a consumo:

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

## <a name="change-license-for-vms-not-registered-with-resource-provider"></a>Modifica di una licenza per le macchine virtuali non registrate con provider di risorse

Se si esegue il provisioning di una VM di SQL Server da immagini del Marketplace di Azure in base al consumo, il tipo di licenza SQL sarà in base al consumo. Se si esegue il provisioning di una VM di SQL Server usando un'immagine BYOL da Azure Marketplace, il tipo di licenza sarà AHUB. Tutte le VM SQL Server fornita da predefinito (PAYG) o se le immagini del Marketplace Azure BYOL verranno registrate automaticamente con il provider di risorse di VM di SQL, affinché possano cambiare il [tipo di licenza](#change-license-for-vms-already-registered-with-resource-provider)

Sono idonei solo per installare automaticamente SQL Server in macchine Virtuali di Azure tramite il vantaggio Azure Hybrid e devi [registrare queste macchine virtuali con il provider di risorse di VM di SQL](virtual-machines-windows-sql-register-with-resource-provider.md) impostando la licenza di SQL Server come vantaggio Azure Hybrid per indicare l'uso del vantaggio Azure Hybrid in base a Condizioni per i prodotti Microsoft.

È possibile modificare il tipo di licenza di una VM di SQL Server in base al consumo o vantaggio Azure Hybrid solo se la VM di SQL è registrata con il provider di risorse di VM di SQL; e tutte le macchine virtuali SQL deve essere registrate con RP della macchina virtuale di SQL per la conformità della licenza.

## <a name="remarks"></a>Note

 - Azure Cloud Solution Partner (CSP) possono usare il vantaggio Azure Hybrid tramite la distribuzione prima di tutto una macchina virtuale con pagamento a consumo e convertendolo in bring-your-own-license se dispongono di sottoscrizione software Assurance attiva.
 - Se si elimina la risorsa di macchina virtuale SQL Server, tornerà all'impostazione a livello di codice di licenza dell'immagine. 
  - La possibilità di modificare il modello di licenza è una funzionalità del provider di risorse di VM di SQL. Distribuire automaticamente un'immagine del marketplace tramite il portale di Azure registra una VM SQL Server con il provider di risorse. Tuttavia, i clienti che sono self-installazione di SQL Server saranno necessario manualmente [registrare le VM di SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Aggiunta di una macchina virtuale di SQL Server a un set di disponibilità, è necessario ricreare la macchina virtuale. Come tali, eventuali macchine virtuali aggiunte a un livello set tornerà al tipo di licenza con pagamento a consumo predefinito e vantaggio Azure Hybrid dovrà essere abilitato nuovamente. 


## <a name="limitations"></a>Limitazioni

 - Modifica il modello di licenza è disponibile solo per i clienti con software assurance.
 - Modifica il modello di licenza è supportata solo per l'edizione standard ed enterprise di SQL Server. Modifiche alle licenze per Express, Web e per gli sviluppatori non sono supportati. 
 - Modifica il modello di licenza è supportata solo per le macchine virtuali distribuite tramite il modello di Resource Manager. Non sono supportate le macchine virtuali distribuite tramite il modello di distribuzione classica. 
 - Modifica il modello di licenza è abilitata solo per le installazioni di Cloud pubblico.
 - Modifica il modello di licenza è supportata solo su macchine virtuali con una singola scheda di rete (interfaccia di rete). Nelle macchine virtuali con più NIC, è necessario prima di tutto rimuovere uno di interfaccia di rete (usando il portale di Azure) prima di eseguire la procedura. In caso contrario, si verifica un errore simile al seguente: `The virtual machine '\<vmname\>' has more than one NIC associated.` Anche se è possibile aggiungere la scheda di rete nella macchina virtuale dopo aver modificato la modalità gestione licenze, le operazioni eseguite tramite la pagina di configurazione di SQL nel portale di Azure, ad esempio l'applicazione automatica delle patch e backup, non verranno considerate supportato.


## <a name="known-errors"></a>Errori noti

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La risorsa ' Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group >' nel gruppo di risorse '\<resource-group >' non è stato trovato. Impossibile trovare la proprietà 'sqlServerLicenseType' tohoto objektu. Verificare che la proprietà è presente e può essere impostata.
Questo errore si verifica quando si prova a modificare il modello di licenza in una VM di SQL Server che non è stato registrato con il provider di risorse di VM di SQL. È necessario registrare il provider di risorse per il [sottoscrizione](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-vm-resource-provider-with-subscription)e quindi registrare la macchina virtuale di SQL Server con il codice SQL [provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Non è possibile convalidare l'argomento per il parametro 'Sku'
Questo errore può verificarsi quando si prova a modificare il modello di licenza di VM di SQL Server quando si usa Azure PowerShell 4.0 >: `Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

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


