---
title: Panoramica sulla protezione dai bot di WAF nel gateway applicazione di Azure
titleSuffix: Azure Web Application Firewall
description: Questo articolo offre una panoramica della protezione dai bot di web application firewall (WAF) nel gateway applicazione
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714900"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Panoramica della protezione dai bot di web application firewall di Azure nel gateway applicazione di Azure

Circa il 20% di tutto il traffico Internet proviene da bot dannosi che eseguono operazioni come lo scorporo, l'analisi e la ricerca di vulnerabilità nell'applicazione Web. Quando i bot vengono fermati nel web application firewall (WAF), non possono attaccare il sistema. Non possono usare nemmeno le risorse e i servizi, ad esempio i backend e le altre infrastrutture sottostanti.

Per WAF è possibile abilitare un set di regole gestito di protezione dai bot per bloccare o registrare le richieste provenienti da indirizzi IP noti per essere dannosi. Gli indirizzi IP hanno origine dal feed di Microsoft Threat Intelligence. La soluzione Microsoft Threat Intelligence, basata su Intelligent Security Graph, viene usata da più servizi, tra cui Centro sicurezza di Azure.

> [!IMPORTANT]
> Il set di regole di protezione dai bot è attualmente disponibile in anteprima pubblica e con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per i dettagli, vedere  [Condizioni supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="use-with-owasp-rulesets"></a>Uso con i set di regole OWASP

È possibile usare il set di regole di protezione dai bot insieme a uno dei set di regole OWASP (2.2.9, 3.0 e 3.1). È possibile usare un solo set di regole OWASP alla volta. Il set di regole di protezione dai bot contiene una regola aggiuntiva che viene visualizzata nel proprio set di regole. Il titolo è **Microsoft_BotManagerRuleSet_0.1** ed è possibile abilitarla o disabilitarla come le altre regole OWASP.

![Set di regole pe i bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aggiornamento del set di regole

L'elenco dei set di regole di mitigazione dei bot di indirizzi IP noti per essere dannosi si aggiorna più volte al giorno dal feed di intelligence sulle minacce di Microsoft per essere sempre sincronizzato con i bot. Le applicazioni Web sono costantemente protette anche quando i vettori di attacco dei bot cambiano.

## <a name="log-example"></a>Esempio di log

Ecco una voce di log di esempio per la protezione dai bot:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la protezione dai bot per il web application firewall nel gateway applicazione di Azure (Anteprima)](bot-protection.md)
