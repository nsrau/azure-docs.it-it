---
title: Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente-interfaccia della riga di comando
description: Informazioni su come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80349743"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come configurare un set di scalabilità di macchine virtuali con un Azure Load Balancer esistente. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure.
- Un servizio di bilanciamento del carico SKU standard esistente nella sottoscrizione in cui verrà distribuito il set di scalabilità di macchine virtuali.
- Una rete virtuale di Azure per il set di scalabilità di macchine virtuali.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di usare l'interfaccia della riga di comando in locale, per questo articolo è necessario che sia installata una versione dell'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Accedere all'interfaccia della riga di comando di Azure

Accedere ad Azure.

```azurecli-interactive
az login
```

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
- [Che cosa sono i set di scalabilità delle macchine virtuali?](../virtual-machine-scale-sets/overview.md)
                                