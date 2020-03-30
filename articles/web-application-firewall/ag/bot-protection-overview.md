---
title: Panoramica della protezione del bot di WAF in Azure Application Gateway
titleSuffix: Azure Web Application Firewall
description: In questo articolo viene fornita una panoramica di Web Application Firewall (WAF) sulla protezione bot del gateway applicazione
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77027095"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Panoramica della protezione del bot di Firewall applicazione Web di Azure in Azure Application GatewayAzure Web Application Firewall on Azure Application Gateway bot protection overview

Circa il 20% di tutto il traffico Internet proviene da bot cattivi. Fanno cose come scraping, scansione, e la ricerca di vulnerabilità nella vostra applicazione web. Quando questi bot vengono arrestati in Web Application Firewall (WAF), non possono attaccare l'utente. Inoltre, non possono utilizzare le risorse e i servizi, ad esempio i back-end e altre infrastrutture sottostanti.

È possibile abilitare un set di regole di protezione bot gestito per il WAF per bloccare o registrare le richieste provenienti da indirizzi IP dannosi noti. Gli indirizzi IP hanno origine dal feed di Microsoft Threat Intelligence. La soluzione Microsoft Threat Intelligence, basata su Intelligent Security Graph, viene usata da più servizi, tra cui Centro sicurezza di Azure.

> [!IMPORTANT]
> Il set di regole di protezione del bot è attualmente in anteprima pubblica e viene fornito con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per informazioni dettagliate, vedere [Le Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure.See the Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) for details.

## <a name="use-with-owasp-rulesets"></a>Utilizzo con set di regole OWASP

È possibile utilizzare il set di regole di protezione bot insieme a uno qualsiasi dei set di regole OWASP (2.2.9, 3.0 e 3.1). È possibile utilizzare un solo set di regole OWASP alla volta. Il set di regole di protezione bot contiene una regola aggiuntiva che viene visualizzata nel proprio set di regole. Si intitola **Microsoft_BotManagerRuleSet_0.1**ed è possibile abilitarlo o disabilitarlo come le altre regole OWASP.

![Set di regole botBot ruleset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Aggiornamento del set di regole

L'elenco di regole di mitigazione bot degli indirizzi IP non validi noti viene aggiornato più volte al giorno dal feed di Microsoft Threat Intelligence per rimanere sincronizzato con i bot. Le applicazioni Web sono continuamente protette anche quando cambiano i vettori di attacco bot.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la protezione dei bot per Il firewall applicazione Web nel gateway applicazione di Azure (anteprima)Configure bot protection for Web Application Firewall on Azure Application Gateway (Preview)](bot-protection.md)
