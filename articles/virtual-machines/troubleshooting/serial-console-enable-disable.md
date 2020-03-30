---
title: Abilitare e disabilitare la console seriale di Azure Documenti Microsoft
description: Come abilitare e disabilitare il servizio Console seriale di AzureHow to enable and disable the Azure Serial Console service
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451294"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Abilitare e disabilitare la console seriale di AzureEnable and disable the Azure Serial Console

Come qualsiasi altra risorsa, la console seriale di Azure può essere abilitata e disabilitata. La console seriale è abilitata per impostazione predefinita per tutte le sottoscrizioni in Azure globale. Attualmente, la disabilitazione della console seriale disabiliterà il servizio per l'intera sottoscrizione. La disabilitazione o la riattivazione della Console seriale per una sottoscrizione richiede l'accesso a livello di collaboratore o versione successiva nella sottoscrizione.

È anche possibile disabilitare la console seriale per una singola macchina virtuale o un'istanza del set di scalabilità di macchine virtuali disabilitando la diagnostica di avvio. Sarà necessario l'accesso a livello di collaboratore o superiore sia nel set di scalabilità della macchina virtuale/macchina virtuale che nell'account di archiviazione di diagnostica di avvio.

## <a name="vm-level-disable"></a>Disabilitazione a livello di macchina virtuale
La console seriale può essere disabilitata per una macchina virtuale o un set di scalabilità di macchine virtuali specifico disabilitando l'impostazione di diagnostica di avvio. Disattivare la diagnostica di avvio dal portale di Azure per disabilitare la console seriale per la macchina virtuale o il set di scalabilità della macchina virtuale. Se si usa la console seriale in un set di scalabilità di macchine virtuali, assicurarsi di aggiornare le istanze del set di scalabilità delle macchine virtuali al modello più recente.


## <a name="subscription-level-enabledisable"></a>Abilitazione/disabilitazione a livello di sottoscrizione

> [!NOTE]
> Assicurarsi di essere nel cloud corretto (Azure Public Cloud, Azure US Government Cloud) prima di eseguire questo comando. È possibile `az cloud list` controllare e impostare il cloud con `az cloud set -n <Name of cloud>`.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

La console seriale può essere disabilitata e riabilitata per un'intera sottoscrizione usando i comandi seguenti nell'interfaccia della riga di comando di Azure (è possibile usare il pulsante "Prova" per avviare un'istanza di Azure Cloud Shell in cui è possibile eseguire i comandi):

Per disabilitare la console seriale per una sottoscrizione, utilizzare i comandi seguenti:To disable serial console for a subscription, use the following commands:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Per abilitare la console seriale per una sottoscrizione, utilizzare i comandi seguenti:To enable serial console for a subscription, use the following commands:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Per ottenere lo stato corrente abilitato/disabilitato della console seriale per una sottoscrizione, utilizzare i comandi seguenti:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

La console seriale può anche essere abilitata e disabilitata tramite PowerShell.Serial console can also be enabled and disabled using PowerShell.

Per disabilitare la console seriale per una sottoscrizione, utilizzare i comandi seguenti:To disable serial console for a subscription, use the following commands:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Per abilitare la console seriale per una sottoscrizione, utilizzare i comandi seguenti:To enable serial console for a subscription, use the following commands:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla console seriale di Azure per le macchine virtuali LinuxLearn more about the [Azure Serial Console for Linux VMs](./serial-console-linux.md)
* Altre informazioni sulla console seriale di Azure per le macchine virtuali WindowsLearn more about [the Azure Serial Console for Windows VMs](./serial-console-windows.md)
* Informazioni sulle opzioni di risparmio energia [all'interno della console seriale](./serial-console-power-options.md) di AzureLearn about power management options within the Azure Serial Console