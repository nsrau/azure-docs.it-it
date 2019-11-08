---
title: Usare un indirizzo IP privato per il routing interno per un endpoint in ingresso
description: Questo articolo fornisce informazioni su come usare gli indirizzi IP privati per il routing interno e quindi esporre l'endpoint in ingresso all'interno di un cluster al resto del VNet.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 570f28ce559ff1c1180ffaacb781b9120b1890a2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795498"
---
# <a name="use-private-ip-for-internal-routing-for-an-ingress-endpoint"></a>Usare IP privato per il routing interno per un endpoint in ingresso 

Questa funzionalità consente di esporre l'endpoint in ingresso all'interno del `Virtual Network` usando un indirizzo IP privato.

## <a name="pre-requisites"></a>Prerequisiti  
Gateway applicazione con una [configurazione IP privata](https://docs.microsoft.com/azure/application-gateway/configure-application-gateway-with-private-frontend-ip)

Esistono due modi per configurare il controller per l'uso dell'indirizzo IP privato per il traffico in ingresso,

## <a name="assign-to-a-particular-ingress"></a>Assegna a un determinato ingresso
Per esporre un determinato ingresso sull'IP privato, usare l'annotazione [`appgw.ingress.kubernetes.io/use-private-ip`](./ingress-controller-annotations.md#use-private-ip) in ingresso.

### <a name="usage"></a>Utilizzo
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

Per i gateway applicazione senza un indirizzo IP privato, le Ingres annotate con `appgw.ingress.kubernetes.io/use-private-ip: "true"` verranno ignorate. Questa operazione sarà indicata nell'evento in ingresso e nel registro Pod AGIC.

* Errore come indicato nell'evento in ingresso

    ```bash
    Events:
    Type     Reason       Age               From                                                                     Message
    ----     ------       ----              ----                                                                     -------
    Warning  NoPrivateIP  2m (x17 over 2m)  azure/application-gateway, prod-ingress-azure-5c9b6fcd4-bctcb  Ingress default/hello-world-ingress requires Application Gateway 
    applicationgateway3026 has a private IP address
    ```

* Errore come indicato nei log di AGIC

    ```bash
    E0730 18:57:37.914749       1 prune.go:65] Ingress default/hello-world-ingress requires Application Gateway applicationgateway3026 has a private IP address
    ```


## <a name="assign-globally"></a>Assegna a livello globale
Nel caso in cui il requisito sia quello di limitare l'esposizione di tutti i file in ingresso tramite IP privato, usare `appgw.usePrivateIP: true` in `helm` config.

### <a name="usage"></a>Utilizzo
```yaml
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>
    usePrivateIP: true
```

In questo modo il controller di ingresso filtra le configurazioni degli indirizzi IP per un indirizzo IP privato quando si configurano i listener front-end nel gateway applicazione.
Se `usePrivateIP: true` e non viene assegnato un indirizzo IP privato, AGIC si blocca e si arresta in modo anomalo.

> [!NOTE]
> Lo SKU del gateway applicazione V2 richiede un indirizzo IP pubblico. Se è necessario che il gateway applicazione sia privato, alleghi un [`Network Security Group`](https://docs.microsoft.com/azure/virtual-network/security-overview) alla subnet del gateway applicazione per limitare il traffico.
