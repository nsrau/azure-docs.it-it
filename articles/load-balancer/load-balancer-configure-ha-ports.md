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
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 7256548b988812c64ca9a9f8a84fec377646635d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-high-availability-ports-for-internal-load-balancer"></a>Come configurare le porte a disponibilità elevata per il servizio di bilanciamento del carico interno

Questo articolo fornisce una distribuzione di esempio delle porte a disponibilità elevata in un servizio di bilanciamento del carico interno. Per le configurazioni specifiche delle appliance virtuali di rete, vedere i siti Web dei relativi provider.

>[!NOTE]
> La funzionalità Porte a disponibilità elevata è attualmente disponibile in anteprima. Durante l'anteprima, la funzionalità potrebbe non avere lo stesso livello di disponibilità e affidabilità delle funzionalità presenti nella versione con disponibilità generale. Per altre informazioni, vedere [Condizioni Supplementari di Microsoft Azure le Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La figura 1 illustra la configurazione seguente dell'esempio di distribuzione descritto in questo articolo:
- Le appliance virtuali di rete vengono distribuite nel pool back-end di un servizio di bilanciamento del carico interno dietro la configurazione delle porte a disponibilità elevata. 
- La route definita dall'utente applicata alla subnet della rete perimetrale instrada tutto il traffico a <?> impostando l'hop successivo come IP virtuale del servizio di bilanciamento del carico interno. 
- Il servizio di bilanciamento del carico interno distribuisce il traffico a una delle appliance virtuali di rete attive in base all'algoritmo di bilanciamento del carico.
- L'appliance virtuale di rete elabora il traffico e lo inoltra alla destinazione originale nella subnet back-end.
- Il percorso di ritorno può anche usare la stessa route se viene configurata una route definita dall'utente corrispondente nella subnet back-end. 

![Distribuzione di esempio di porte a disponibilità elevata](./media/load-balancer-configure-ha-ports/haports.png)

Figura 1: Appliance virtuali di rete distribuite dietro un servizio di bilanciamento del carico interno con porte a disponibilità elevata 

## <a name="preview-sign-up"></a>Iscrizione all'anteprima

Per partecipare all'anteprima della funzionalità Porte a disponibilità elevata in Load Balancer Standard SKU, registrare la propria sottoscrizione per ottenere l'accesso tramite PowerShell o l'interfaccia della riga di comando di Azure 2.0.

- Iscrizione tramite PowerShell

   ```powershell
   Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Iscrizione tramite l'interfaccia della riga di comando di Azure 2.0

    ```cli
  az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network  
    ```

## <a name="configuring-ha-ports"></a>Configurazione di porte a disponibilità elevata

La configurazione delle porte a disponibilità elevata comporta l'impostazione di un servizio di bilanciamento del carico interno, con le appliance virtuali di rete nel pool back-end, la configurazione di un probe di integrità per il servizio di bilanciamento del carico corrispondente per rilevare l'integrità delle appliance virtuali di rete e la regola del servizio di bilanciamento del carico con le porte a disponibilità elevata. La configurazione correlata al servizio di bilanciamento del carico generale viene illustrata in [Introduzione](load-balancer-get-started-ilb-arm-portal.md). Questo articolo tratta la configurazione delle porte a disponibilità elevata.

La configurazione comporta essenzialmente l'impostazione del valore della porta front-end e della porta back-end su **0** e del valore del protocollo su **All**. Questo articolo illustra come configurare le porte a disponibilità elevata usando il portale di Azure, PowerShell e l'interfaccia della riga di comando di Azure 2.0.

### <a name="configure-ha-ports-load-balancer-rule-with-the-azure-portal"></a>Configurare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con il portale di Azure

Per questa configurazione il portale di Azure include la casella di controllo **Porte a disponibilità elevata**. Se selezionata, la configurazione delle porte e del protocollo correlata viene automaticamente popolata. 

![Configurazione delle porte a disponibilità elevata tramite il portale di Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)

Figura 2: Configurazione delle porte a disponibilità elevata tramite il portale

### <a name="configure-ha-ports-load-balancer-rule-with-powershell"></a>Configurare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con PowerShell

Usare il comando seguente per creare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata mentre si crea il Servizio di bilanciamento del carico interno con PowerShell:

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-ha-ports-load-balancer-rule-with-azure-cli-20"></a>Configurare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata con l'interfaccia della riga di comando di Azure 2.0

Nel passaggio 4 di [Creazione di un set di bilanciamento del carico interno](load-balancer-get-started-ilb-arm-cli.md), usare il comando seguente per creare la regola del servizio di bilanciamento del carico per le porte a disponibilità elevata.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Passaggi successivi

- [Altre informazioni sulle porte a disponibilità elevata](load-balancer-ha-ports-overview.md)
