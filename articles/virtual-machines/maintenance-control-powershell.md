---
title: Controllo di manutenzione per le macchine virtuali di Azure con PowerShellMaintenance control for Azure virtual machines using PowerShell
description: Informazioni su come controllare quando viene applicata la manutenzione alle macchine virtuali di Azure usando Controllo manutenzione e PowerShell.Learn how to control when maintenance is applied to your Azure VMs using Maintenance Control and PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060125"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Anteprima: Controllare gli aggiornamenti con Il controllo di manutenzione e Azure PowerShell

Gestire gli aggiornamenti della piattaforma, che non richiedono un riavvio, utilizzando il controllo di manutenzione. Azure aggiorna spesso la propria infrastruttura per migliorare l'affidabilità, le prestazioni, la sicurezza o lanciare nuove funzionalità. La maggior parte degli aggiornamenti è trasparente per gli utenti. Alcuni carichi di lavoro sensibili, come giochi, streaming multimediale e transazioni finanziarie, non possono tollerare nemmeno pochi secondi di blocco o disconnessione di una macchina virtuale per la manutenzione. Il controllo della manutenzione offre la possibilità di attendere gli aggiornamenti della piattaforma e applicarli all'interno di una finestra continua di 35 giorni. 

Il controllo di manutenzione consente di decidere quando applicare gli aggiornamenti alle macchine virtuali isolate.

Con il controllo della manutenzione, è possibile:
- Aggiornamenti batch in un unico pacchetto di aggiornamento.
- Attendi fino a 35 giorni per applicare gli aggiornamenti. 
- Automatizzare gli aggiornamenti della piattaforma per la finestra di manutenzione usando Funzioni di Azure.Automate platform updates for your maintenance window using Azure Functions.
- Le configurazioni di manutenzione funzionano tra sottoscrizioni e gruppi di risorse. 

> [!IMPORTANT]
> Il controllo di manutenzione è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 

## <a name="limitations"></a>Limitazioni

- Le macchine virtuali devono trovarsi in un [host dedicato](./linux/dedicated-hosts.md)o essere create utilizzando una dimensione di macchina [virtuale isolata.](./linux/isolation.md)
- Dopo 35 giorni, verrà applicato automaticamente un aggiornamento.
- L'utente deve disporre **dell'accesso Collaboratore risorse.**


## <a name="enable-the-powershell-module"></a>Abilitare il modulo di PowerShellEnable the PowerShell module

Assicurati `PowerShellGet` che sia aggiornato.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

I cmdlet di PowerShell Az.Maintenance sono in anteprima, pertanto è necessario installare il modulo con il `AllowPrerelease` parametro in Cloud Shell o nell'installazione di PowerShell locale.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Se si esegue l'installazione in locale, assicurarsi di aprire il prompt di PowerShell come amministratore.

Potrebbe anche essere richiesto di confermare che si desidera installare da un *repository non attendibile*. Digitare `Y` o selezionare **Sì a tutti** per installare il modulo.



## <a name="create-a-maintenance-configuration"></a>Creare una configurazione di manutenzioneCreate a maintenance configuration

Creare un gruppo di risorse come contenitore per la configurazione. In questo esempio, un gruppo di risorse denominato *myMaintenanceRG* viene creato in *eastus*. Se si dispone già di un gruppo di risorse che si vuole usare, è possibile ignorare questa parte e sostituire il nome del gruppo di risorse con il proprio proprietario negli altri esempi.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Utilizzare [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) per creare una configurazione di manutenzione. In questo esempio viene creata una configurazione di manutenzione denominata *myConfig* con ambito all'host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

L'utilizzo `-MaintenanceScope host` garantisce che la configurazione di manutenzione venga utilizzata per controllare gli aggiornamenti all'host.

Se si tenta di creare una configurazione con lo stesso nome, ma in una posizione diversa, verrà visualizzato un errore. I nomi di configurazione devono essere univoci per la sottoscrizione.

È possibile eseguire una query per le configurazioni di manutenzione disponibili utilizzando [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>Assegnare la configurazione

Usare New-AzConfigurationAssignment per assegnare la configurazione alla macchina virtuale isolata o all'host dedicato di Azure.Use [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) to assign the configuration to your isolated VM or Azure Dedicated Host.

### <a name="isolated-vm"></a>VM isolata

Applicare la configurazione a una macchina virtuale usando l'ID della configurazione. Specificare `-ResourceType VirtualMachines` e fornire il `-ResourceName`nome della macchina virtuale per `-ResourceGroupName`e il gruppo di risorse della macchina virtuale per . 

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

### <a name="dedicated-host"></a>Host dedicato

Per applicare una configurazione a un host dedicato, è inoltre necessario includere `-ResourceType hosts`, `-ResourceParentName` con il nome del gruppo host e `-ResourceParentType hostGroups`. 


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

## <a name="check-for-pending-updates"></a>Verificare la disponibilità di aggiornamenti in sospeso

Utilizzare [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) per verificare se sono presenti aggiornamenti in sospeso. Usare `-subscription` per specificare la sottoscrizione di Azure della macchina virtuale se è diversa da quella a cui si è connessi.

Se non sono presenti aggiornamenti da visualizzare, questo comando non restituirà alcun valore. In caso contrario, restituirà un oggetto PSApplyUpdate:Otherwise, it will return a PSApplyUpdate object:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>VM isolata

Verificare la disponibilità di aggiornamenti in sospeso per una macchina virtuale isolata. In questo esempio, l'output viene formattato come tabella per la leggibilità.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Host dedicato

Per verificare la disponibilità di aggiornamenti in sospeso per un host dedicato. In questo esempio, l'output viene formattato come tabella per la leggibilità. Sostituire i valori per le risorse con i propri.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Applicare gli aggiornamenti

Utilizzare [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) per applicare gli aggiornamenti in sospeso.

### <a name="isolated-vm"></a>VM isolata

Creare una richiesta per applicare gli aggiornamenti a una macchina virtuale isolata.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

In caso di esito `PSApplyUpdate` positivo, questo comando restituirà un oggetto. È possibile utilizzare l'attributo Name nel `Get-AzApplyUpdate` comando per controllare lo stato dell'aggiornamento. Consultate [Controllare lo stato dell'aggiornamento.](#check-update-status)

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

## <a name="check-update-status"></a>Controllare lo stato dell'aggiornamento
Utilizzare [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) per verificare lo stato di un aggiornamento. I comandi illustrati di seguito mostrano `default` lo `-ApplyUpdateName` stato dell'aggiornamento più recente utilizzando per il parametro. È possibile sostituire il nome dell'aggiornamento (restituito dal comando [New-AzApplyUpdate)](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) per ottenere lo stato di un aggiornamento specifico.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime sarà l'ora in cui l'aggiornamento è stato completato, avviato dall'utente o dalla piattaforma nel caso in cui non sia stata utilizzata la finestra di auto-manutenzione. Se non è mai stato applicato un aggiornamento tramite il controllo di manutenzione, verrà visualizzato il valore predefinito.

### <a name="isolated-vm"></a>VM isolata

Verificare la disponibilità di aggiornamenti per una macchina virtuale specifica.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Host dedicato

Verificare la disponibilità di aggiornamenti per un host dedicato.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Rimuovere una configurazione di manutenzione

Utilizzare [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) per eliminare una configurazione di manutenzione.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere [Manutenzione e aggiornamenti](maintenance-and-updates.md).
