---
title: Configurare le porte a disponibilità elevata per Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Informazioni su come usare le porte a disponibilità elevata per il bilanciamento del carico del traffico interno su tutte le porte
services: load-balancer
documentationcenter: na
author: rdhillon
manager: narayan
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: allensu
ms.openlocfilehash: c0cf1eb62c8e01988c9014478ff72816e45ea64c
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275623"
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurare le porte a disponibilità elevata per un servizio di bilanciamento del carico interno

Questo articolo fornisce una distribuzione di esempio delle porte a disponibilità elevata in un servizio di bilanciamento del carico interno. Per altre informazioni sulle configurazioni specifiche delle appliance virtuali di rete, vedere i siti Web dei provider corrispondenti.

>[!NOTE]
>Azure Load Balancer supporta due tipi diversi: Basic e Standard. Questo articolo illustra Load Balancer Standard. Per altre informazioni su Load Balancer Basic, vedere la [panoramica di Load Balancer](load-balancer-overview.md).

La figura mostra la configurazione dell'esempio di distribuzione descritto in questo articolo:

- Le appliance virtuali di rete vengono distribuite nel pool back-end di un servizio di bilanciamento del carico interno nella configurazione delle porte a disponibilità elevata. 
- La route definita dall'utente applicata nella subnet della rete perimetrale instrada tutto il traffico verso le appliance virtuali di rete impostando l'hop successivo come IP virtuale del servizio di bilanciamento del carico interno. 
- Il servizio di bilanciamento del carico interno distribuisce il traffico in una delle appliance virtuali di rete attive in base all'algoritmo di bilanciamento del carico.
- L'appliance virtuale di rete elabora il traffico e lo inoltra alla destinazione originale nella subnet back-end.
- Il percorso di ritorno può anche usare la stessa route se è configurata una route definita dall'utente corrispondente nella subnet back-end. 

![Distribuzione di esempio delle porte a disponibilità elevata](./media/load-balancer-configure-ha-ports/haports.png)

## <a name="configure-high-availability-ports"></a>Configurare le porte a disponibilità elevata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per configurare le porte a disponibilità elevata, configurare un servizio di bilanciamento del carico interno con le appliance virtuali di rete nel pool back-end. Impostare la configurazione di un probe del servizio di bilanciamento del carico corrispondente per rilevare l'integrità delle appliance virtuali di rete e la regola del servizio di bilanciamento del carico con le porte a disponibilità elevata. La configurazione correlata al servizio di bilanciamento del carico generale viene descritta in [Introduzione](load-balancer-get-started-ilb-arm-portal.md). Questo articolo mostra la configurazione delle porte a disponibilità elevata.

La configurazione comporta essenzialmente l'impostazione dei valori della porta front-end e della porta back-end su **0**. Impostare il valore del protocollo su **Tutti**. Questo articolo descrive come configurare le porte a disponibilità elevata usando il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configurare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con il portale di Azure

Per configurare le porte a disponibilità elevata usando il portale di Azure, selezionare la casella di controllo **Porte a disponibilità elevata**. Se selezionata, la configurazione delle porte e del protocollo correlata viene automaticamente popolata. 

![Configurazione delle porte a disponibilità elevata tramite il portale di Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configurare una regola del servizio di bilanciamento del carico per le porte a disponibilità elevata tramite il modello di Resource Manager

È possibile configurare le porte a disponibilità elevata usando l'API versione 2017-08-01 per Microsoft.Network/loadBalancers nella risorsa Load Balancer. Il frammento di codice JSON seguente mostra le modifiche nella configurazione del servizio di bilanciamento del carico per le porte a disponibilità elevata tramite l'API REST:

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configurare una regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con PowerShell

Usare il comando seguente per creare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata mentre si crea il servizio di bilanciamento del carico interno con PowerShell:

```powershell
lbrule = New-AzLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli"></a>Configurare una regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con l'interfaccia della riga di comando di Azure

Nel passaggio 4 di [Creare un servizio di bilanciamento del carico interno tramite l'interfaccia della riga di comando di Azure](load-balancer-get-started-ilb-arm-cli.md) usare il comando seguente per creare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sulle [porte a disponibilità elevata](load-balancer-ha-ports-overview.md).