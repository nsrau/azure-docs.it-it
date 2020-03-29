---
title: Gestire i domini di errore nei set di scalabilità delle macchine virtuali di AzureManage fault domains in Azure virtual machine scale sets
description: Informazioni su come scegliere il numero corretto dei domini di errore durante la creazione di un set di scalabilità della macchina virtuale.
author: rajsqr
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: 297837354cea4bb5ccdcc03261810dcffd144243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275734"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Scelta del numero corretto dei domini di errore per set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali vengono creati con cinque domini di errore per impostazione predefinita in aree di Azure senza zone. Per le aree che supportano la distribuzione zonale dei set di scalabilità di macchine virtuali e questa opzione è selezionata, il valore predefinito del conteggio dei domini di errore è 1 per ognuna delle zone. FD = 1 questo caso implica che le istanze di macchine virtuali che appartengono al set di scalabilità verranno distribuite tra molti rack nel modo più efficiente possibile.

È inoltre possibile considerare di allineare il numero di domini di errore del set di scalabilità con il numero di domini di errore di Managed Disks. Questo allineamento consente di evitare la perdita di quorum se un intero dominio di errore di Managed Disks si arresta. Il numero di domini di errore può essere minore o uguale al numero di domini di errore di Managed Disks disponibili in ognuna delle aree. Consultare questo [documento](../virtual-machines/windows/manage-availability.md) per conoscere il numero di domini di errore di Managed Disks per area.

## <a name="rest-api"></a>API REST
È possibile impostare la proprietà `properties.platformFaultDomainCount` su 1, 2 o 3 (se non specificato, il valore predefinito è 5). La documentazione per questa API REST è disponibile [qui](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile impostare il parametro `--platform-fault-domain-count` su 1, 2 o 3 (se non specificato, il valore predefinito è 5). La documentazione per l'interfaccia della riga di comando di Azure è disponibile [qui](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az-vmss-create).

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --platform-fault-domain-count 3\
  --generate-ssh-keys
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [funzionalità di disponibilità e ridondanza](../virtual-machines/windows/availability.md) per gli ambienti di Azure.
