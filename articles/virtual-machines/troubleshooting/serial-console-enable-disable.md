---
title: Abilitare e disabilitare la console seriale di Azure | Microsoft Docs
description: Come abilitare e disabilitare il servizio console seriale di Azure
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
ms.openlocfilehash: fa400d875a8f39d54d10820c603e12e97f0cd854
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452226"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>Abilitare e disabilitare la console seriale di Azure

Analogamente a qualsiasi altra risorsa, la console seriale di Azure può essere abilitata e disabilitata. La console seriale è abilitata per impostazione predefinita per tutte le sottoscrizioni in Azure globale. Attualmente, disabilitando la console seriale, il servizio viene disabilitato per l'intera sottoscrizione. Per disabilitare o riabilitare la console seriale per una sottoscrizione, è necessario disporre dell'accesso a livello di collaboratore o superiore nella sottoscrizione.

È anche possibile disabilitare la console seriale per una singola macchina virtuale o un'istanza del set di scalabilità di macchine virtuali disabilitando la diagnostica di avvio. È necessario l'accesso a livello di collaboratore o superiore sia nel set di scalabilità di macchine virtuali/VM che nell'account di archiviazione della diagnostica di avvio.

## <a name="vm-level-disable"></a>Disabilitazione a livello di macchina virtuale
La console seriale può essere disabilitata per una VM specifica o un set di scalabilità di macchine virtuali disabilitando l'impostazione di diagnostica di avvio. Disattivare la diagnostica di avvio dalla portale di Azure per disabilitare la console seriale per la VM o il set di scalabilità di macchine virtuali. Se si usa la console seriale in un set di scalabilità di macchine virtuali, assicurarsi di aggiornare le istanze del set di scalabilità di macchine virtuali al modello più recente.


## <a name="subscription-level-enabledisable"></a>Abilita/Disabilita a livello di sottoscrizione

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile disabilitare e riabilitare console seriale per un'intera sottoscrizione usando i comandi seguenti nell'interfaccia della riga di comando di Azure. è possibile usare il pulsante "prova" per avviare un'istanza del Azure Cloud Shell in cui è possibile eseguire i comandi:

Per disabilitare la console seriale per una sottoscrizione, usare i comandi seguenti:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Per abilitare la console seriale per una sottoscrizione, usare i comandi seguenti:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

Per ottenere lo stato corrente abilitato/disabilitato della console seriale per una sottoscrizione, usare i comandi seguenti:
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

> [!NOTE]
> Prima di eseguire questo comando, assicurarsi di trovarsi nel cloud appropriato (cloud pubblico di Azure, cloud per il governo degli Stati Uniti di Azure). È possibile verificare con `az cloud list` e impostare il cloud con `az cloud set -n <Name of cloud>`.

### <a name="powershell"></a>PowerShell

Console seriale possono anche essere abilitati e disabilitati tramite PowerShell.

Per disabilitare la console seriale per una sottoscrizione, usare i comandi seguenti:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

Per abilitare la console seriale per una sottoscrizione, usare i comandi seguenti:
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [console seriale di Azure per macchine virtuali Linux](./serial-console-linux.md)
* Altre informazioni sulla [console seriale di Azure per le macchine virtuali Windows](./serial-console-windows.md)
* Informazioni sulle [Opzioni di risparmio energia nella console seriale di Azure](./serial-console-power-options.md)