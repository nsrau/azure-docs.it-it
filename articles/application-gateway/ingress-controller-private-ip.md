---
title: Usare l'indirizzo IP privato per il routing interno per un endpoint in ingressoUse private IP address for internal routing for an ingress endpoint
description: In questo articolo vengono fornite informazioni su come utilizzare gli indirizzi IP privati per il routing interno e quindi l'esposizione dell'endpoint Ingress all'interno di un cluster al resto della rete virtuale.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73795498"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Usare l'IP privato per il routing interno per un endpoint IngressUse private IP for internal routing for an Ingress endpoint 

Questa funzionalità consente di esporre l'endpoint in ingresso all'interno di `Virtual Network` utilizzando un indirizzo IP privato.

## <a name="pre-requisites"></a>Prerequisiti  
Gateway applicazione con una [configurazione IP privata](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Esistono due modi per configurare il controller per l'utilizzo di IP privato per l'ingresso,

## <a name="assign-to-a-particular-ingress"></a>Assegnare a un particolare ingresso
Per esporre un particolare ingresso su [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) IP privato, utilizzare l'annotazione in Ingresso.To expose a particular ingress over Private IP, use annotation in Ingress.

### <a name="usage"></a>Uso
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Per i gateway applicazione senza un IP privato, gli `appgw.ingress.kubernetes.io/use-private-ip: "true"` ingressioni annotati con verranno ignorati. Questo verrà indicato nell'evento in ingresso e nel registro pod AGIC.

* Errore come indicato nell'evento in ingresso

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Errore come indicato nei registri AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Assegna globalmente
Nel caso in cui, requisito è quello di limitare `appgw.usePrivateIP: true` `helm` tutti gli ingressioni da esporre su IP privato, utilizzare in config.

### <a name="usage"></a>Uso
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

In questo modo il controller in ingresso filtercherà le configurazioni degli indirizzi IP per un indirizzo IP privato durante la configurazione dei listener front-end nel gateway applicazione.
AGIC sarà panico `usePrivateIP: true` e crash se e nessun IP privato è assegnato.

> [!NOTE]
> Lo SKU del gateway applicazione v2 richiede un indirizzo IP pubblico. Se è necessario che il gateway [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) applicazione sia privato, collegare un alla subnet del gateway applicazione per limitare il traffico.
