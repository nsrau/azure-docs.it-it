---
title: Creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Informazioni su come creare un servizio di bilanciamento del carico pubblico standard con un front-end di indirizzo IP pubblico con ridondanza della zona usando l'interfaccia della riga di comando di Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 1a430f5c6349741e5d04626158dc89d42169a15b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-frontend-using-azure-cli"></a>Creare un servizio di bilanciamento del carico pubblico standard con un front-end con ridondanza della zona usando l'interfaccia della riga di comando di Azure

Questo articolo illustra la creazione di un [servizio di bilanciamento del carico standard](https://aka.ms/azureloadbalancerstandard) pubblico con un front-end con ridondanza della zona tramite un indirizzo standard IP pubblico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="register-for-availability-zones-preview"></a>Registrarsi all'anteprima di Zone di disponibilità

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.17 o successiva.  Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

> [!NOTE]
> Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Verificare di aver installato la versione più recente dell'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e di aver eseguito l'accesso a un account di Azure con [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse usando il comando seguente:

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-public-ip-standard"></a>Creare un IP pubblico Standard

Creare un IP pubblico Standard usando il comando seguente:

```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku Standard
```

## <a name="create-a-load-balancer"></a>Creare un servizio di bilanciamento del carico

Creare un servizio di bilanciamento del carico pubblico Standard con l'IP pubblico Standard creato nel passaggio precedente usando il comando seguente:

```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --public-ip-address myPublicIP --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>Creare un probe di bilanciamento del carico sulla porta 80

Creare un probe di integrità del servizio di bilanciamento del carico usando il comando seguente:

```azurecli-interactive
az network lb probe create --resource-group myResourceGroup --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>Creare una regola di bilanciamento del carico per la porta 80

Creare una regola del servizio di bilanciamento del carico usando il comando seguente:

```azurecli-interactive
az network lb rule create --resource-group myResourceGroup --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [creare un IP pubblico in una zona di disponibilità](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)



