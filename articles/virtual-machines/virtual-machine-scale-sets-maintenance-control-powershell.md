---
title: Controllo di manutenzione per gli aggiornamenti delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure
description: Informazioni su come controllare quando vengono implementati gli aggiornamenti automatici delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure usando il controllo di manutenzione e PowerShell.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 62b2fd0554c218b733501805779745049d3b8c92
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532642"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>Anteprima: controllo manutenzione per gli aggiornamenti delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure con PowerShell

Il controllo di manutenzione consente di decidere quando applicare gli aggiornamenti automatici delle immagini del sistema operativo guest ai set di scalabilità di macchine virtuali. In questo argomento vengono illustrate le opzioni di Azure PowerShell per il controllo di manutenzione. Per altre informazioni sull'uso del controllo di manutenzione, vedere [controllo di manutenzione per i set di scalabilità di macchine virtuali di Azure](virtual-machine-scale-sets-maintenance-control.md).

> [!IMPORTANT]
> Il controllo di manutenzione per gli aggiornamenti delle immagini del sistema operativo nei set di scalabilità di macchine virtuali di Azure è attualmente in anteprima pubblica
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="enable-the-powershell-module"></a>Abilitare il modulo PowerShell

Assicurarsi che `PowerShellGet` sia aggiornato.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

Installare il `Az.Maintenance` modulo PowerShell.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

Se si sta installando localmente, assicurarsi di aprire il prompt di PowerShell come amministratore.

Potrebbe anche essere richiesto di confermare che si vuole eseguire l'installazione da un *repository non attendibile*. Digitare `Y` o selezionare **Sì per** installare il modulo.

## <a name="connect-to-an-azure-account"></a>Connettersi a un account Azure

Connettersi all'account Azure desiderato usando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) e [set-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>Creare una configurazione di manutenzione

Creare un gruppo di risorse come contenitore per la configurazione. In questo esempio viene creato un gruppo di risorse denominato *myMaintenanceRG* in *eastus2*. Se si dispone già di un gruppo di risorse che si vuole usare, è possibile ignorare questa parte. È sufficiente sostituire il nome del gruppo di risorse con il proprio nel resto degli esempi.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

Usare [New-AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) per creare una configurazione di manutenzione. Questo esempio Mostra come creare una configurazione di manutenzione denominata *config* con ambito nell'immagine del sistema operativo. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> La **durata** della manutenzione deve essere maggiore di *5 ore* . La **ricorrenza** della manutenzione deve essere impostata su *Day*.

L'uso di `-MaintenanceScope OSImage` assicura che la configurazione di manutenzione venga usata per controllare gli aggiornamenti al sistema operativo guest.

Se si tenta di creare una configurazione con lo stesso nome, ma in un percorso diverso, si riceverà un errore. I nomi di configurazione devono essere univoci per il gruppo di risorse.

È possibile eseguire una query per le configurazioni di manutenzione disponibili usando [Get-AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>Associare il set di scalabilità di macchine virtuali alla configurazione di manutenzione

Un set di scalabilità di macchine virtuali può essere associato a qualsiasi configurazione di manutenzione indipendentemente dall'area e dalla sottoscrizione della configurazione di manutenzione. Se si sceglie la configurazione di manutenzione, i nuovi aggiornamenti delle immagini del sistema operativo per il set di scalabilità verranno automaticamente pianificati nella successiva finestra di manutenzione disponibile.

Usare [New-AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) per associare il set di scalabilità di macchine virtuali alla configurazione di manutenzione.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>Abilita aggiornamento automatico del sistema operativo

È possibile abilitare gli aggiornamenti automatici del sistema operativo per ogni set di scalabilità di macchine virtuali che userà il controllo di manutenzione. Per l'abilitazione degli aggiornamenti automatici del sistema operativo nel set di scalabilità di macchine virtuali, vedere la documentazione relativa ai [set di scalabilità di macchine virtuali di Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) . 


## <a name="next-steps"></a>Passaggi successivi

Informazioni sulla manutenzione e sugli aggiornamenti per le macchine virtuali in esecuzione in Azure.

> [!div class="nextstepaction"]
> [Manutenzione e aggiornamenti](maintenance-and-updates.md)
