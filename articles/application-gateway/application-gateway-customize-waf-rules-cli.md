---
title: Personalizzare le regole di web application firewall nel Gateway applicazione di Azure - CLI di Azure
description: Questo articolo descrive come personalizzare le regole del web application firewall nel gateway applicazione con l'interfaccia della riga di comando di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: 5e364c597b8c524e95297f279003462f2d16abe1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60832898"
---
# <a name="customize-web-application-firewall-rules-through-the-azure-cli"></a>Personalizzare le regole del web application firewall con l'interfaccia della riga di comando di Azure

Il Web application firewall del gateway applicazione di Azure (WAF) fornisce la protezione per le Applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per altre informazioni su regole e gruppi di regole specifici, vedere l'[Elenco di regole e gruppi di regole CRS del Web application firewall](application-gateway-crs-rulegroups-rules.md).

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

Nell'elenco seguente contiene le condizioni che causano il firewall WAF bloccare la richiesta in modalità di prevenzione (in modalità di rilevamento vengono registrati come eccezioni). Questi non può essere configurati o disabilitati:

* Impossibile analizzare il corpo della richiesta comporta la richiesta viene bloccata, a meno che non ispezione del corpo è disattivata (XML, JSON, i dati del modulo)
* Lunghezza dei dati del corpo (con nessun file) della richiesta è supera al limite configurato
* Request body (inclusi i file) è superiore al limite
* Si è verificato un errore interno nel motore di Web Application firewall

CRS 3.x specifico:

* Connessioni in entrata soglia superata punteggio delle anomalie

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
