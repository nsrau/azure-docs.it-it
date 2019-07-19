---
title: Gestione dei domini di errore nei set di scalabilità di macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come scegliere il numero corretto dei domini di errore durante la creazione di un set di scalabilità della macchina virtuale.
services: virtual-machine-scale-sets
documentationcenter: ''
author: rajsqr
manager: drewm
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2018
ms.author: drewm
ms.openlocfilehash: f97c7e6971fb9c58a3f08959c00c84e64e160916
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871946"
---
# <a name="choosing-the-right-number-of-fault-domains-for-virtual-machine-scale-set"></a>Scelta del numero corretto dei domini di errore per set di scalabilità di macchine virtuali
I set di scalabilità di macchine virtuali vengono creati con cinque domini di errore per impostazione predefinita in aree di Azure senza zone. Per le aree che supportano la distribuzione di zona dei set di scalabilità di macchine virtuali, il valore predefinito del numero di domini di errore è 1 per ciascuna zona. FD = 1 questo caso implica che le istanze di macchine virtuali che appartengono al set di scalabilità verranno distribuite tra molti rack nel modo più efficiente possibile.

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
