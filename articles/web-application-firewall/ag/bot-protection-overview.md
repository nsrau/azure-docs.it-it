---
title: Panoramica sulla protezione di WAF su applicazione Azure gateway bot
titleSuffix: Azure Web Application Firewall
description: Questo articolo fornisce una panoramica di web application firewall (WAF) sulla protezione bot del gateway applicazione
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77027095"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Panoramica sulla protezione del bot del gateway applicazione Web di Azure in applicazione Azure gateway

Circa il 20% di tutto il traffico Internet deriva da bot cattivi. Consentono di eseguire operazioni come la frammentazione, l'analisi e la ricerca di vulnerabilità nell'applicazione Web. Quando questi bot vengono arrestati in Web Application Firewall (WAF), non possono attaccare l'utente. Non possono inoltre usare le risorse e i servizi, ad esempio i backend e altre infrastrutture sottostanti.

È possibile abilitare un set di regole di protezione bot gestito per il WAF in modo da bloccare o registrare le richieste provenienti da indirizzi IP dannosi noti. Gli indirizzi IP hanno origine dal feed di Microsoft Threat Intelligence. La soluzione Microsoft Threat Intelligence, basata su Intelligent Security Graph, viene usata da più servizi, tra cui Centro sicurezza di Azure.

> [!IMPORTANT]
> Il set di regole di bot Protection è attualmente disponibile in anteprima pubblica e viene fornito con un contratto di servizio in anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per informazioni dettagliate, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 

## <a name="use-with-owasp-rulesets"></a>Usare con i RuleSet OWASP

È possibile usare il set di regole di protezione bot insieme a tutti i RuleSet OWASP (2.2.9, 3,0 e 3,1). È possibile usare un solo set di regole OWASP in un determinato momento. Il set di regole di protezione bot contiene una regola aggiuntiva che viene visualizzata in un proprio set di regole. Il titolo è **Microsoft_BotManagerRuleSet_0 1**ed è possibile abilitarlo o disabilitarlo come le altre regole di OWASP.

![Set di regole bot](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aggiornamento RuleSet

L'elenco dei set di regole di mitigazione bot degli indirizzi IP non validi noti viene aggiornato più volte al giorno dal feed di intelligence per le minacce di Microsoft per restare sincronizzato con i bot. Le applicazioni Web sono costantemente protette anche quando i vettori di attacco bot cambiano.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la protezione di bot per il Web Application Firewall nel gateway applicazione Azure (anteprima)](bot-protection.md)
