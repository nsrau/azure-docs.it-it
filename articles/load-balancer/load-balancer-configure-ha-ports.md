---
title: "Configurare le porte a disponibilità elevata per Azure Load Balancer| Microsoft Docs"
description: "Informazioni su come usare le porte a disponibilità elevata per il bilanciamento del carico del traffico interno su tutte le porte"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 36bc3d7a35f41384706cbc7101457d00848639b2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurare le porte a disponibilità elevata per un servizio di bilanciamento del carico interno

Questo articolo fornisce una distribuzione di esempio delle porte a disponibilità elevata in un servizio di bilanciamento del carico interno. Per altre informazioni sulle configurazioni specifiche delle appliance virtuali di rete, vedere i siti Web dei provider corrispondenti.

>[!NOTE]
> La funzionalità Porte a disponibilità elevata è attualmente disponibile come versione di anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La figura mostra la configurazione dell'esempio di distribuzione descritto in questo articolo:

- Le appliance virtuali di rete vengono distribuite nel pool back-end di un servizio di bilanciamento del carico interno nella configurazione delle porte a disponibilità elevata. 
- La route definita dall'utente applicata nella subnet della rete perimetrale instrada tutto il traffico verso le appliance virtuali di rete impostando l'hop successivo come IP virtuale del servizio di bilanciamento del carico interno. 
- Il servizio di bilanciamento del carico interno distribuisce il traffico in una delle appliance virtuali di rete attive in base all'algoritmo di bilanciamento del carico.
- L'appliance virtuale di rete elabora il traffico e lo inoltra alla destinazione originale nella subnet back-end.
- Il percorso di ritorno può anche usare la stessa route se è configurata una route definita dall'utente corrispondente nella subnet back-end. 

![Distribuzione di esempio delle porte a disponibilità elevata](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare all'anteprima della funzionalità Porte a disponibilità elevata nel servizio Load Balancer Standard, registrare la sottoscrizione per poter accedere usando l'interfaccia della riga di comando di Azure 2.0 o PowerShell. Registrare la sottoscrizione per [l'anteprima di Load Balancer Standard](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>La registrazione dell'anteprima di Load Balancer Standard potrebbe richiedere fino a un'ora.

## <a name="configure-high-availability-ports"></a>Configurare le porte a disponibilità elevata

Per configurare le porte a disponibilità elevata, configurare un servizio di bilanciamento del carico interno con le appliance virtuali di rete nel pool back-end. Impostare la configurazione di un probe del servizio di bilanciamento del carico corrispondente per rilevare l'integrità delle appliance virtuali di rete e la regola del servizio di bilanciamento del carico con le porte a disponibilità elevata. La configurazione correlata al servizio di bilanciamento del carico generale viene descritta in [Introduzione](load-balancer-get-started-ilb-arm-portal.md). Questo articolo mostra la configurazione delle porte a disponibilità elevata.

La configurazione comporta essenzialmente l'impostazione dei valori della porta front-end e della porta back-end su **0**. Impostare il valore del protocollo su **Tutti**. Questo articolo descrive come configurare le porte a disponibilità elevata usando il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure 2.0.

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
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Configurare una regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con l'interfaccia della riga di comando di Azure 2.0

Nel passaggio 4 di [Creare un servizio di bilanciamento del carico interno tramite l'interfaccia della riga di comando di Azure](load-balancer-get-started-ilb-arm-cli.md) usare il comando seguente per creare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sulle [porte a disponibilità elevata](load-balancer-ha-ports-overview.md).
