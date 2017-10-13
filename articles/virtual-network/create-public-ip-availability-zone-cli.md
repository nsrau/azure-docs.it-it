---
title: Creare un indirizzo IP pubblico di zona con l'interfaccia della riga di comando di Azure | Microsoft Docs
description: "Creare un indirizzo IP pubblico in una zona di disponibilità con l'interfaccia della riga di comando di Azure."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Creare un indirizzo IP pubblico in una zona di disponibilità con l'interfaccia della riga di comando di Azure

È possibile distribuire un indirizzo IP pubblico in una zona di disponibilità di Azure (anteprima). Una zona di disponibilità è una zona fisicamente separata in un'area di Azure. È possibile passare agli argomenti seguenti:

> * Creare un indirizzo IP pubblico in una zona di disponibilità
> * Identificare le risorse correlate create nella zona di disponibilità

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione successiva alla versione 2.0.17. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Le zone di disponibilità sono in versione di anteprima e sono pronte per scenari di sviluppo e test. È disponibile il supporto per alcune risorse, aree e famiglie di dimensioni di macchina virtuale di Azure selezionate. Per altre informazioni su come iniziare e con quali risorse, aree e famiglie di dimensioni di macchina virtuale di Azure è possibile provare le zone di disponibilità, vedere [Panoramica delle zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Per assistenza è possibile usare il forum di [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) oppure [aprire un ticket di supporto di Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Creare un indirizzo IP pubblico di zona

Creare un indirizzo IP pubblico in una zona di disponibilità usando il comando seguente:


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups). La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.

## <a name="get-zone-information-about-a-public-ip-address"></a>Ottenere informazioni sulla zona di un indirizzo IP pubblico

Ottenere le informazioni sulla zona di un indirizzo IP pubblico usando il comando seguente:

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Passaggi successivi

- Leggere altre informazioni sulle [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview)
- Leggere altre informazioni sugli [indirizzi IP pubblici](../virtual-network/virtual-network-public-ip-address.md) 
