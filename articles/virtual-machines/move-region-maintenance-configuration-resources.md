---
title: Spostare le risorse associate a una configurazione di manutenzione in un'altra area
description: Informazioni su come spostare le risorse associate a una configurazione di manutenzione della macchina virtuale in un'altra area di AzureLearn how to move resources associated with a VM maintenance configuration to another Azure region
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 3e271e2467b495e79a93ce5eab5edee36e65e619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304446"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>Spostare le risorse in una configurazione di Controllo di manutenzione in un'altra areaMove resources in a Maintenance Control configuration to another region

Seguire questo articolo per spostare le risorse associate a una configurazione di controllo di manutenzione in un'area di Azure diversa. È possibile spostare una configurazione per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area, per distribuire funzionalità o servizi disponibili in un'area specifica, per soddisfare i requisiti di policy e governance interni o in risposta alla pianificazione della capacità.

Il controllo di manutenzione, con configurazioni di manutenzione personalizzate, consente di controllare la modalità di applicazione degli aggiornamenti della piattaforma alle macchine virtuali Windows e Linux e agli host dedicati di Azure.Maintenance control, with customized maintenance configurations, allows you to control how platform updates are applied to [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) and [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VMs, and to Azure Dedicated Hosts. Esistono un paio di scenari per spostare il controllo di manutenzione tra aree:There are a couple of scenarios for moving maintenance control across regions:

- Per spostare le risorse associate a una configurazione di manutenzione, ma non la configurazione stessa, seguire questo articolo.
- Per spostare la configurazione del controllo di manutenzione, ma non le risorse associate alla configurazione, seguire [queste istruzioni](move-region-maintenance-configuration.md).
- Per spostare sia la configurazione di manutenzione che le risorse ad essa associate, seguire [prima queste istruzioni.](move-region-maintenance-configuration.md) Quindi, seguire le istruzioni in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a spostare le risorse associate a una configurazione di Controllo manutenzione:

- Assicurarsi che le risorse da spostare esistano nella nuova area prima di iniziare.
- Verificare le configurazioni di Controllo di manutenzione associate alle macchine virtuali di Azure e agli host dedicati di Azure che si desidera spostare. Controllare ogni risorsa singolarmente. Attualmente non è possibile recuperare le configurazioni per più risorse.
- Quando si recuperano le configurazioni per una risorsa:When retrieving configurations for a resource:
    - Assicurarsi di usare l'ID sottoscrizione per l'account, non un ID host dedicato di Azure.Make sure you use the subscription ID for the account, not an Azure Dedicated Host ID.
    - CLI: Il parametro --output table viene utilizzato solo per la leggibilità e può essere eliminato o modificato.
    - PowerShell: il parametro Format-Table Name viene utilizzato solo per la leggibilità e può essere eliminato o modificato.
    - Se si usa PowerShell, viene visualizzato un errore se si tenta di elencare le configurazioni per una risorsa a cui non sono associate configurazioni. L'errore sarà simile al: "Operazione non riuscita con stato: 'Non trovato'. Dettagli: 404 Errore client: Non trovato per url".

    
## <a name="prepare-to-move"></a>Preparati a muoverti

1. Prima di iniziare, definire queste variabili. Abbiamo fornito un esempio per ciascuno.

    **Variabile** | **Dettagli** | **Esempio**
    --- | ---
    $subId | ID per sottoscrizione contenente le configurazioni di manutenzione | "il nostro-subscription-ID"
    $rsrcGroupName | Nome del gruppo di risorse (VM di Azure)Resource group name (Azure VM) | "VMResourceGroup"
    $vmName | Nome risorsa VM |  "myVM"
    $adhRsrcGroupName |  Gruppo di risorse (host dedicati) | "HostResourceGroup"
    $adh | Nome host dedicato | "myHost"
    $adhParentName | Nome risorsa padre | "Gruppo Host"
    
2. Per recuperare le configurazioni di manutenzione utilizzando il comando [PowerShell Get-AConfigurationAssignment:](https://docs.microsoft.com/powershell/module/az.maintenance/Get-AzConfigurationAssignment?view=azps-3.5.0)

    - Per gli host dedicati di Azure, eseguire:For Azure Dedicated Hosts, run:
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Per le macchine virtuali di Azure, eseguire:For Azure VMs, run:

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. Per recuperare le configurazioni di manutenzione utilizzando il comando CLI [az maintenance assignment:](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/assignment?view=azure-cli-latest)

    - Per gli host dedicati di Azure:For Azure Dedicated Hosts:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Per le macchine virtuali di Azure:For Azure VMs:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>Spostamento 

1. [Seguire queste istruzioni](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) per spostare le macchine virtuali di Azure nella nuova area.
2. Dopo lo spostamento delle risorse, riapplicare le configurazioni di manutenzione alle risorse nella nuova area in base alle esigenze, a seconda che le configurazioni di manutenzione siano state spostate o meno. È possibile applicare una configurazione di manutenzione a una risorsa usando [PowerShell](../virtual-machines/maintenance-control-powershell.md) o [l'interfaccia della riga di comando.](../virtual-machines/maintenance-control-cli.md)


## <a name="verify-the-move"></a>Verificare lo spostamento

Verificare le risorse nella nuova area e verificare le configurazioni associate per le risorse nella nuova area. 

## <a name="clean-up-source-resources"></a>Pulire le risorse di origineClean up source resources

Dopo lo spostamento, è consigliabile eliminare le risorse spostate nell'area di origine.


## <a name="next-steps"></a>Passaggi successivi

Seguire [queste istruzioni](move-region-maintenance-configuration.md) se è necessario spostare le configurazioni di manutenzione. 
