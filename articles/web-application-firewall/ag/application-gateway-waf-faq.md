---
title: Web Application Firewall di Azure nel gateway applicazione-domande frequenti
description: Questo articolo fornisce le risposte alle domande frequenti su Web Application Firewall sul gateway applicazione
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 57081cd948bcee1261415eae31f91b3c61f08c9f
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82842851"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Domande frequenti sul firewall applicazione Web di Azure sul gateway applicazione

Questo articolo risponde a domande comuni su Web Application Firewall (WAF) di Azure sulle funzionalità e sulle funzionalità del gateway applicazione. 

## <a name="what-is-azure-waf"></a>Che cos'è Azure WAF?

Azure WAF è un web application firewall che aiuta a proteggere le applicazioni Web da minacce comuni, ad esempio SQL injection, script intersito e altri exploit Web. È possibile definire un criterio WAF costituito da una combinazione di regole personalizzate e gestite per controllare l'accesso alle applicazioni Web.

È possibile applicare un criterio di Azure WAF alle applicazioni Web ospitate nel gateway applicazione o in Azure front doors.

## <a name="what-features-does-the-waf-sku-support"></a>Quali funzionalità sono supportate dallo SKU WAF?

Lo SKU WAF supporta tutte le funzionalità disponibili nello SKU standard.

## <a name="how-do-i-monitor-waf"></a>Come si monitora il Web application firewall?

Monitorare WAF tramite registrazione diagnostica. Per altre informazioni, vedere [registrazione diagnostica e metriche per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>La modalità di rilevamento blocca il traffico?

No. La modalità di rilevamento registra solo il traffico che attiva una regola WAF.

## <a name="can-i-customize-waf-rules"></a>È possibile personalizzare le regole del Web application firewall (WAF)?

Sì. Per altre informazioni, vedere [Customize WAF Rule Groups and Rules](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Quali regole sono attualmente disponibili per WAF?

WAF supporta attualmente CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3,0](application-gateway-crs-rulegroups-rules.md#owasp30)e [3,1](application-gateway-crs-rulegroups-rules.md#owasp31). Queste regole forniscono una sicurezza di base rispetto alla maggior parte delle 10 vulnerabilità che aprono il progetto di sicurezza dell'applicazione Web (OWASP): 

* Protezione dagli attacchi SQL injection
* Protezione tramite script tra siti
* Protezione da attacchi Web comuni, ad esempio l'inserimento di comandi, il contrabbando di richieste HTTP, la suddivisione della risposta HTTP e l'attacco di inclusione di file remoti
* Protezione dalle violazioni del protocollo HTTP
* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header
* Prevenzione contro robot, crawler e scanner
* Rilevamento di errate configurazioni dell'applicazione comuni (ovvero Apache, IIS e così via)

Per altre informazioni, vedere [OWASP Top-10 vulnerabilità](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

## <a name="does-waf-support-ddos-protection"></a>WAF supporta la protezione DDoS?

Sì. È possibile abilitare la protezione DDoS nella rete virtuale in cui è distribuito il gateway applicazione. Questa impostazione garantisce che il servizio protezione DDoS di Azure protegga anche l'indirizzo IP virtuale (VIP) del gateway applicazione.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Web Application Firewall di Azure](../overview.md).
- Altre informazioni su [Azure front door](../../frontdoor/front-door-overview.md).
