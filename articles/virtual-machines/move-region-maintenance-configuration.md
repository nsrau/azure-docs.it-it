---
title: Spostare una configurazione di manutenzione in un'altra area di AzureMove a maintenance configuration to another Azure region
description: Informazioni su come spostare una configurazione di manutenzione di una macchina virtuale in un'altra area di AzureLearn how to move a VM maintenance configuration to another Azure region
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304459"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Spostare una configurazione di Controllo manutenzione in un'altra area

Seguire questo articolo per spostare una configurazione di controllo di manutenzione in un'area di Azure diversa. È possibile spostare una configurazione per diversi motivi. Ad esempio, per sfruttare i vantaggi di una nuova area, per distribuire funzionalità o servizi disponibili in un'area specifica, per soddisfare i requisiti di policy e governance interni o in risposta alla pianificazione della capacità.

Il controllo di manutenzione, con configurazioni di manutenzione personalizzate, consente di controllare la modalità di applicazione degli aggiornamenti della piattaforma alle macchine virtuali Windows e Linux e agli host dedicati di Azure.Maintenance control, with customized maintenance configurations, allows you to control how platform updates are applied to [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) and [Linux](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) VMs, and to Azure Dedicated Hosts. Esistono un paio di scenari per spostare il controllo di manutenzione tra aree:There are a couple of scenarios for moving maintenance control across regions:

- Per spostare la configurazione del controllo di manutenzione, ma non le risorse associate alla configurazione, seguire le istruzioni in questo articolo.
- Per spostare le risorse associate a una configurazione di manutenzione, ma non la configurazione stessa, seguire [queste istruzioni](move-region-maintenance-configuration-resources.md).
- Per spostare sia la configurazione di manutenzione che le risorse ad essa associate, seguire innanzitutto le istruzioni in questo articolo. Quindi, seguire [queste istruzioni](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare a spostare una configurazione di controllo di manutenzione:

- Le configurazioni di manutenzione sono associate alle macchine virtuali di Azure o agli host dedicati di Azure.Maintenance configurations are associated with Azure VMs or Azure Dedicated Hosts. Assicurarsi che le risorse VM/host esistano nella nuova area prima di iniziare.
- Identify: 
    - Configurazioni di controllo della manutenzione esistenti.
    - Gruppi di risorse in cui si trovano attualmente le configurazioni esistenti. 
    - Gruppi di risorse a cui verranno aggiunte le configurazioni dopo lo spostamento nella nuova area. 
    - Le risorse associate alla configurazione di manutenzione che si desidera spostare.
    - Verificare che le risorse nella nuova area siano le stesse associate alle configurazioni di manutenzione correnti. Le configurazioni possono avere gli stessi nomi nella nuova area come nella vecchia, ma questo non è obbligatorio.

## <a name="prepare-and-move"></a>Preparare e spostare 

1. Recuperare tutte le configurazioni di manutenzione in ogni sottoscrizione. Eseguire il comando CLI [az maintenance configuration list](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) per eseguire questa operazione, sostituendo $subId con l'ID sottoscrizione.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Esaminare la tabella restituita dei record di configurazione all'interno della sottoscrizione. Di seguito è riportato un esempio. L'elenco conterrà i valori per l'ambiente specifico.

    **Nome** | **Percorso** | **Gruppo di risorse**
    --- | --- | ---
    Salta manutenzione | eastus2 | configuration-resource-group
    IgniteDemoConfig (informazioni in questo modo) | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | configurazione test
    

3. Salvare l'elenco per riferimento. Quando sposti le configurazioni, ti aiuta a verificare che tutto sia stato spostato.
4. Come riferimento, eseguire il mapping di ogni gruppo di configurazioni/risorse al nuovo gruppo di risorse nella nuova area.
5. Creare nuove configurazioni di manutenzione nella nuova area usando [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)o [l'interfaccia della riga di comando.](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)
6. Associare le configurazioni alle risorse della nuova area, utilizzando [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)o [CLI.](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)


## <a name="verify-the-move"></a>Verificare lo spostamento

Dopo aver spostato le configurazioni, confrontare le configurazioni e le risorse nella nuova area con l'elenco di tabelle preparato.


## <a name="clean-up-source-resources"></a>Pulire le risorse di origineClean up source resources

Dopo lo spostamento, è consigliabile eliminare le configurazioni di manutenzione spostate nell'area di origine, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)o [CLI.](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration)


## <a name="next-steps"></a>Passaggi successivi

Seguire [queste istruzioni](move-region-maintenance-configuration-resources.md) se è necessario spostare le risorse associate alle configurazioni di manutenzione. 
