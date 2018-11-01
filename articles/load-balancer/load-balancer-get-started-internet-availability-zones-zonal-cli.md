---
title: Creare un servizio Load Balancer Standard pubblico con un front-end di zona usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare un servizio Load Balancer Standard pubblico con un front-end di zona usando l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 3a0fc37b8e2865163ae6c55813d145a568d796e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414484"
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Creare un servizio Load Balancer Standard pubblico con un front-end di zona usando l'interfaccia della riga di comando di Azure

Questo articolo illustra la creazione di un servizio [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end di zona. Un front-end di zona consente la gestione di qualsiasi flusso in ingresso o in uscita da una singola zona in un'area. È possibile creare un servizio di bilanciamento del carico con un front-end di zona tramite un indirizzo IP pubblico Standard di zona nella configurazione front-end. Per comprendere il funzionamento delle zone di disponibilità con il servizio Load Balancer Standard, vedere [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md). 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, assicurarsi di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e di avere eseguito l'accesso a un account di Azure con [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

> [!NOTE]
> Il supporto per le zone di disponibilità è disponibile per determinate risorse, aree e famiglie di dimensioni di macchine virtuali di Azure. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente:

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>Creare un indirizzo IP pubblico Standard

Creare un indirizzo IP pubblico Standard di zona usando il comando seguente:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare un servizio di bilanciamento del carico pubblico Standard con l'IP pubblico Standard creato nel passaggio precedente usando il comando seguente:

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Creare un probe di bilanciamento del carico sulla porta 80

Creare un probe di integrità del servizio di bilanciamento del carico usando il comando seguente:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Creare una regola di bilanciamento del carico per la porta 80

Creare una regola del servizio di bilanciamento del carico usando il comando seguente:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su [Load Balancer Standard e zone di disponibilità](load-balancer-standard-availability-zones.md).



