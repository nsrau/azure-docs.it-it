---
title: Controllo di manutenzione per macchine virtuali di Azure con PowerShell
description: Informazioni su come controllare quando viene applicata la manutenzione alle VM di Azure usando il controllo di manutenzione e PowerShell.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: e7a5f9ba865ab555bde3125f40ee8675709bef40
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932710"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Anteprima: controllare gli aggiornamenti con il controllo di manutenzione e Azure PowerShell

Gestire gli aggiornamenti della piattaforma, che non richiedono un riavvio, usando il controllo di manutenzione. Azure aggiorna spesso l'infrastruttura per migliorare l'affidabilità, le prestazioni, la sicurezza o avviare nuove funzionalità. La maggior parte degli aggiornamenti è trasparente per gli utenti. Alcuni carichi di lavoro sensibili, ad esempio giochi, flussi multimediali e transazioni finanziarie, non possono tollerare persino pochi secondi di blocco o disconnessione di una macchina virtuale per la manutenzione. Il controllo di manutenzione offre la possibilità di attendere gli aggiornamenti della piattaforma e di applicarli in una finestra in sequenza di 35 giorni. 

Il controllo della manutenzione consente di decidere quando applicare gli aggiornamenti alle VM isolate.

Con il controllo di manutenzione, è possibile:
- Aggiornamenti batch in un unico pacchetto di aggiornamento.
- Attendere fino a 35 giorni per l'applicazione degli aggiornamenti. 
- Automatizzare gli aggiornamenti della piattaforma per la finestra di manutenzione con funzioni di Azure.
- Le configurazioni di manutenzione funzionano tra le sottoscrizioni e i gruppi di risorse. 

> [!IMPORTANT]
> Il controllo della manutenzione è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Limitazioni

- Le macchine virtuali devono trovarsi in un [host dedicato](./linux/dedicated-hosts.md)o essere create con una [dimensione di macchina virtuale isolata](./linux/isolation.md).
- Dopo 35 giorni, verrà automaticamente applicato un aggiornamento e i vincoli di disponibilità non verranno rispettati.
- L'utente deve disporre dell'accesso al **proprietario delle risorse** .


## <a name="enable-the-powershell-module"></a>Abilitare il modulo PowerShell

Assicurarsi che `PowerShellGet` sia aggiornato.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

I cmdlet di PowerShell AZ. Maintenance sono in anteprima, quindi è necessario installare il modulo con il parametro `AllowPrerelease` in Cloud Shell o nell'installazione di PowerShell locale.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Se si sta installando localmente, assicurarsi di aprire il prompt di PowerShell come amministratore.

Potrebbe anche essere richiesto di confermare che si vuole eseguire l'installazione da un *repository non attendibile*. Digitare `Y` o selezionare **Sì per** installare il modulo.



## <a name="create-a-maintenance-configuration"></a>Creare una configurazione di manutenzione

Creare un gruppo di risorse come contenitore per la configurazione. In questo esempio viene creato un gruppo di risorse denominato *myMaintenanceRG* in *eastus*. Se si dispone già di un gruppo di risorse che si vuole usare, è possibile ignorare questa parte e sostituire il nome del gruppo di risorse con il resto degli esempi.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Usare [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) per creare una configurazione di manutenzione. In questo esempio viene creata una configurazione di manutenzione denominata *config* con ambito nell'host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

L'utilizzo di `-MaintenanceScope host` garantisce che la configurazione di manutenzione venga utilizzata per controllare gli aggiornamenti all'host.

Se si tenta di creare una configurazione con lo stesso nome, ma in un percorso diverso, si otterrà un errore. I nomi di configurazione devono essere univoci per la sottoscrizione.

È possibile eseguire una query per le configurazioni di manutenzione disponibili usando [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Assegnare la configurazione

Usare [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) per assegnare la configurazione alla macchina virtuale isolata o all'host dedicato di Azure.

### <a name="isolated-vm"></a>VM isolata

Applicare la configurazione a una macchina virtuale usando l'ID della configurazione. Specificare `-ResourceType VirtualMachines` e fornire il nome della macchina virtuale per `-ResourceName`e il gruppo di risorse della macchina virtuale per `-ResourceGroupName`. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicate-host"></a>Host dedicato

Per applicare una configurazione a un host dedicato, è necessario includere anche `-ResourceType hosts`, `-ResourceParentName` con il nome del gruppo host e `-ResourceParentType hostGroups`. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Verificare la presenza di aggiornamenti in sospeso

Usare [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) per verificare se sono presenti aggiornamenti in sospeso. Usare `-subscription` per specificare la sottoscrizione di Azure della VM se è diversa da quella a cui si è connessi. 

### <a name="isolated-vm"></a>VM isolata

Verificare la presenza di aggiornamenti in sospeso per una macchina virtuale isolata. In questo esempio l'output viene formattato come tabella per migliorare la leggibilità.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```

### <a name="dedicated-host"></a>Host dedicato

Per verificare la presenza di aggiornamenti in sospeso per un host dedicato. In questo esempio l'output viene formattato come tabella per migliorare la leggibilità. Sostituire i valori per le risorse con quelli personalizzati.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```

## <a name="apply-updates"></a>Applicazione degli aggiornamenti

Usare [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) per applicare gli aggiornamenti in sospeso.

### <a name="isolated-vm"></a>VM isolata

Creare una richiesta per applicare gli aggiornamenti a una macchina virtuale isolata.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

### <a name="dedicated-host"></a>Host dedicato

Applicare gli aggiornamenti a un host dedicato.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="remove-a-maintenance-configuration"></a>Rimuovere una configurazione di manutenzione

Usare [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) per eliminare una configurazione di manutenzione.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [manutenzione e aggiornamenti](maintenance-and-updates.md).
