---
title: Personalizzare le regole del web application firewall nel gateway applicazione di Azure - Interfaccia della riga di comando di Azure 2.0 | Microsoft Docs
description: Questa pagina descrive come personalizzare le regole del web application firewall nel gateway applicazione con l'interfaccia della riga di comando di Azure 2.0.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: cfc01caf5c8ca11f790a6640ec57e3d14c1b7767
ms.contentlocale: it-it
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-azure-cli-20"></a>Personalizzare le regole del web application firewall con l'interfaccia della riga di comando di Azure 2.0

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-customize-waf-rules-cli.md)

Il Web application firewall del gateway applicazione fornisce la protezione per le Applicazioni Web. Queste protezioni vengono fornite dai set di regole CRS dell'OWASP. Alcune regole possono generare falsi positivi e bloccare il traffico reale.  Per questo motivo il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole in un gateway applicazione abilitato per il web application firewall. Per altre informazioni su regole e gruppi di regole specifici, visitare la pagina relativa a [regole e gruppi di regole CRS del Web application firewall](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

Gli esempi seguenti illustrano come visualizzare le regole e i gruppi di regole configurabili in un gateway applicazione abilitato per il web application firewall (WAF).

### <a name="view-rule-groups"></a>Visualizzare i gruppi di regole

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Di seguito è riportata una parte di risposta dell'esempio precedente.

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  },
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_2.2.9",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
   "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "crs_20_protocol_violations",
        "rules": null
      },
      ...
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "2.2.9",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

### <a name="view-rules-in-a-rule-group"></a>Visualizzare le regole in un gruppo di regole

L'esempio seguente illustra come visualizzare le regole in un gruppo di regole specificato.

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Di seguito è riportata una parte di risposta dell'esempio precedente.

```
[
  {
    "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/applicationGatewayAvailableWafRuleSets/",
    "location": null,
    "name": "OWASP_3.0",
    "provisioningState": "Succeeded",
    "resourceGroup": "",
    "ruleGroups": [
      {
        "description": "",
        "ruleGroupName": "REQUEST-910-IP-REPUTATION",
        "rules": [
          {
            "description": "Rule 910011",
            "ruleId": 910011
          },
          ...
        ]
      }
    ],
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "tags": null,
    "type": "Microsoft.Network/applicationGatewayAvailableWafRuleSets"
  }
]
```

## <a name="disable-rules"></a>Disabilitare le regole

L'esempio seguente disabilita le regole `910018` e `910017` in un gateway applicazione.

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="next-steps"></a>Passaggi successivi

Una volta configurate le regole disabilitate, informazioni su come visualizzare i log del WAF sono disponibili nella sezione relativa alla [diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
