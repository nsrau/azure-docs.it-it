---
title: Personalizzare le regole usando l'interfaccia della riga di comando-Web Application Firewall di Azure
description: Questo articolo fornisce informazioni su come personalizzare le regole del Web Application Firewall nel gateway applicazione con l'interfaccia della riga di comando di Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 8e8aaa9458619bc937c5bb11c450f3197b92f451
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048523"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-cli"></a>Personalizzare le regole del Web Application Firewall usando l'interfaccia della riga di comando di Azure

Il firewall applicazione Web del gateway applicazione Azure (WAF) fornisce la protezione per le applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per ulteriori informazioni sulle regole e sui gruppi di regole specifici, vedere l' [elenco delle regole e dei gruppi di regole CRS del Web Application Firewall](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

Gli esempi di codice seguenti illustrano come visualizzare le regole e i gruppi di regole configurabili.

### <a name="view-rule-groups"></a>Visualizzare i gruppi di regole

L'esempio seguente mostra come visualizzare i gruppi di regole:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --type OWASP
```

Di seguito è riportata una parte di risposta dell'esempio precedente:

```json
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

L'esempio seguente mostra come visualizzare le regole in un gruppo di regole specificato:

```azurecli-interactive
az network application-gateway waf-config list-rule-sets --group "REQUEST-910-IP-REPUTATION"
```

Di seguito è riportata una parte di risposta dell'esempio precedente:

```json
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

L'esempio seguente disabilita le regole `910018` e `910017` in un gateway applicazione:

```azurecli-interactive
az network application-gateway waf-config set --resource-group AdatumAppGatewayRG --gateway-name AdatumAppGateway --enabled true --rule-set-version 3.0 --disabled-rules 910018 910017
```

## <a name="mandatory-rules"></a>Regole obbligatorie

L'elenco seguente contiene le condizioni che determinano il blocco della richiesta da parte di WAF in modalità di prevenzione (in modalità di rilevamento vengono registrate come eccezioni). Non possono essere configurati o disabilitati:

* Se non si analizza il corpo della richiesta, la richiesta viene bloccata, a meno che l'ispezione del corpo non sia spenta (XML, JSON, dati del modulo)
* La lunghezza dei dati del corpo della richiesta senza file è superiore al limite configurato
* Il corpo della richiesta (inclusi i file) è più grande del limite
* Si è verificato un errore interno nel motore WAF

Specifico CRS 3. x:

* Il Punteggio di anomalie in ingresso ha superato la soglia

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
