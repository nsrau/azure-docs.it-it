---
title: Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente-interfaccia della riga di comando
description: Informazioni su come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando l'interfaccia della riga di comando di Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: a60a6889217ce6ca8dccd5ebf5ee74b8f67a7757
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518210"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti 

- È necessario un servizio di bilanciamento del carico SKU standard esistente nella sottoscrizione in cui verrà distribuito il set di scalabilità di macchine virtuali.

- È necessaria una rete virtuale di Azure per il set di scalabilità di macchine virtuali.
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede la versione 2.0.28 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuire un set di scalabilità di macchine virtuali con il servizio di bilanciamento del carico esistente

Sostituire i valori tra parentesi quadre con i nomi delle risorse nella configurazione.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

L'esempio seguente distribuisce un set di scalabilità di macchine virtuali con:

- Set di scalabilità di macchine virtuali denominato **myVMSS**
- Azure Load Balancer denominato **myLoadBalancer**
- Pool back-end del servizio di bilanciamento del carico denominato **myBackendPool**
- Rete virtuale di Azure denominata **myVnet**
- **Subnet denominata subnet**
- Gruppo di risorse denominato **myResourceGroup**
- Immagine del server Ubuntu per il set di scalabilità di macchine virtuali

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> Dopo la creazione del set di scalabilità, la porta back-end non può essere modificata per una regola di bilanciamento del carico usata da un probe di integrità del servizio di bilanciamento del carico. Per modificare la porta, è possibile rimuovere il probe di integrità aggiornando il set di scalabilità di macchine virtuali di Azure, aggiornare la porta e quindi configurare di nuovo il probe di integrità.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato distribuito un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.  Per altre informazioni sui set di scalabilità di macchine virtuali e sul servizio di bilanciamento del carico, vedere:

- [Informazioni su Azure Load Balancer](load-balancer-overview.md)
- [Informazioni sui set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/overview.md)
                                