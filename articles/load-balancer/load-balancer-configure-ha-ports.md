---
title: "Configurare le porte a disponibilità elevata per Azure Load Balancer| Microsoft Docs"
description: "Informazioni su come utilizzare le porte di disponibilità elevata per il traffico interno per tutte le porte di bilanciamento del carico"
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurare le porte di disponibilità elevata per un servizio di bilanciamento del carico interno

Questo articolo fornisce un esempio di distribuzione di porte di disponibilità elevata in un bilanciamento del carico interno. Per ulteriori informazioni sulle configurazioni specifiche di dispositivi di rete virtuale (NVAs), vedere i siti Web del provider corrispondente.

>[!NOTE]
> La funzionalità di porte di elevata disponibilità è attualmente in anteprima. Durante l'anteprima, la funzionalità non potrebbe essere lo stesso livello di disponibilità e affidabilità come versione di funzionalità che sono in genere la disponibilità. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La figura mostra la configurazione dell'esempio di distribuzione descritta in questo articolo seguente:

- Il NVAs vengono distribuiti nel pool back-end di un servizio di bilanciamento del carico interno dietro la configurazione di porte di disponibilità elevata. 
- La route definite dall'utente (UDR) applicato in uso le route della subnet rete Perimetrale tutto il traffico per il NVAs effettuando l'hop successivo come interno IP virtuale del servizio di bilanciamento di carico. 
- Servizio di bilanciamento del carico interno distribuisce il traffico su uno del NVAs attivi in base all'algoritmo di bilanciamento del carico.
- Il NVA elabora il traffico e lo inoltra alla destinazione originale nella subnet back-end.
- Il percorso restituito può richiedere la route stesso se è configurato un UDR corrispondente nella subnet back-end. 

![Esempio di distribuzione alta disponibilità porte](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare all'anteprima della funzionalità di elevata disponibilità porte di bilanciamento del carico di Azure Standard, registrare la sottoscrizione per ottenere l'accesso tramite l'interfaccia CLI di Azure 2.0 o PowerShell. Registrare la sottoscrizione per [Standard anteprima di bilanciamento del carico](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>Registrazione di anteprima di bilanciamento del carico Standard può richiedere fino a un'ora.

## <a name="configure-high-availability-ports"></a>Configurare le porte di disponibilità elevata

Per configurare porte di elevata disponibilità, impostare un bilanciamento del carico interno con il NVAs nel pool di back-end. Impostare una configurazione di probe dello stato di bilanciamento del carico corrispondente per rilevare l'integrità NVA e la regola di bilanciamento del carico con porte a disponibilità elevata. Viene descritta la configurazione relativa a bilanciamento carico generale in [iniziare](load-balancer-get-started-ilb-arm-portal.md). In questo articolo evidenzia la configurazione di porte di disponibilità elevata.

La configurazione essenzialmente comporta l'impostazione della porta front-end e il valore della porta back-end per **0**. Impostare il valore del protocollo **tutti**. In questo articolo viene descritto come configurare le porte di elevata disponibilità tramite il portale di Azure, PowerShell e 2.0 di CLI di Azure.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configurare una regola di bilanciamento del carico di elevata disponibilità porte con il portale di Azure

Per configurare le porte di elevata disponibilità tramite il portale di Azure, selezionare il **a disponibilità elevata porte** casella di controllo. Se selezionata, la configurazione delle porte e del protocollo correlata viene automaticamente popolata. 

![Configurazione di porte di elevata disponibilità tramite il portale di Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configurare una regola di bilanciamento del carico di elevata disponibilità porte tramite il modello di gestione risorse

È possibile configurare le porte di disponibilità elevata utilizzando la versione dell'API 2017-08-01 per Microsoft.Network/loadBalancers nella risorsa di bilanciamento del carico. Il frammento JSON seguente vengono illustrate le modifiche nella configurazione del servizio di bilanciamento del carico per le porte di disponibilità elevata tramite l'API REST:

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

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configurare una regola di bilanciamento del carico di elevata disponibilità porte con PowerShell

Utilizzare il comando seguente per creare la regola di bilanciamento del carico di elevata disponibilità porte durante la creazione di bilanciamento del carico interno con PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Configurare una regola di bilanciamento del carico di elevata disponibilità porte con Azure CLI 2.0

Nel passaggio 4 della [creare un set di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md), utilizzare il comando seguente per creare la regola di bilanciamento del carico di elevata disponibilità porte:

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni, vedere [ad alta disponibilità porte](load-balancer-ha-ports-overview.md).
