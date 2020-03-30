---
title: Personalizzare le regole usando PowerShellCustomize rules using PowerShell
titleSuffix: Azure Web Application Firewall
description: In questo articolo vengono fornite informazioni su come personalizzare le regole di Web Application Firewall nel gateway applicazione con PowerShell.This article provides information on how to customize Web Application Firewall rules in Application Gateway with PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74048516"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Personalizzare le regole di Web Application Firewall tramite PowerShellCustomize Web Application Firewall rules using PowerShell

Il firewall dell'applicazione Web del gateway applicazione di Azure (WAF) fornisce protezione per le applicazioni Web. Queste protezioni vengono fornite dal Set di regole principali (CRS) di Open Web Application Security Project (OWASP). Alcune regole possono generare falsi positivi e bloccare il traffico reale. Per questo motivo, il gateway applicazione offre la possibilità di personalizzare regole e gruppi di regole. Per ulteriori informazioni sui gruppi di regole e sulle regole specifiche, vedere [List of Web Application Firewall CRS Rule groups and rules](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Visualizzare le regole e i gruppi di regole

Gli esempi di codice seguenti illustrano come visualizzare le regole e i gruppi di regole configurabili in un gateway applicazione abilitato per il web application firewall (WAF).

### <a name="view-rule-groups"></a>Visualizzare i gruppi di regole

L'esempio seguente mostra come visualizzare i gruppi di regole:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Di seguito è riportata una parte di risposta dell'esempio precedente:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Disabilitare le regole

L'esempio seguente disabilita le regole `911011` e `911012` in un gateway applicazione:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Regole obbligatorie

L'elenco seguente contiene le condizioni che causano il WAF bloccare la richiesta in modalità prevenzione (in modalità di rilevamento vengono registrati come eccezioni). Questi non possono essere configurati o disabilitati:

* La mancata analisi del corpo della richiesta comporta il blocco della richiesta, a meno che l'ispezione del corpo non sia disattivata (XML, JSON, dati del modulo)
* La lunghezza dei dati del corpo della richiesta (senza file) è maggiore del limite configuratoRequest body data length is larger than the configured limit
* Il corpo della richiesta (inclusi i file) è maggiore del limite
* Si è verificato un errore interno nel motore WAF

Specifico di CRS 3.x:

* Punteggio anomaly in entrata superato soglia

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato le regole disattivate, viene descritto come visualizzare i log WAF. Per altre informazioni, vedere [Diagnostica del gateway applicazione](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
