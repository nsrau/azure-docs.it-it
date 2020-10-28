---
title: 'Web application firewall di Azure nel gateway applicazione: domande frequenti'
description: Questo articolo offre risposte alle domande frequenti relative al web application firewall nel gateway applicazione
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 1fbe930780ff026be748bc42104f821ee9e5c443
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785103"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Domande frequenti sul web application firewall di Azure nel gateway applicazione

Questo articolo risponde a domande comuni sul web application firewall (WAF) di Azure nelle funzionalità del gateway applicazione. 

## <a name="what-is-azure-waf"></a>Cos'è il web application firewall di Azure?

Il WAF di Azure è un web application firewall che consente di proteggere le applicazioni Web da minacce comuni, quali attacchi SQL injection, script da altri siti e altri exploit Web. L'utente può definire un criterio WAF costituito da una combinazione di regole personalizzate e gestite per controllare l'accesso alle applicazioni Web.

I criteri WAF di Azure possono essere applicati alle applicazioni Web ospitate nel gateway applicazione o in Frontdoor di Azure.

## <a name="what-features-does-the-waf-sku-support"></a>Quali funzionalità sono supportate dallo SKU WAF?

Lo SKU WAF supporta tutte le funzionalità disponibili con lo SKU Standard.

## <a name="how-do-i-monitor-waf"></a>Come si monitora il Web application firewall?

Monitorare il WAF tramite la registrazione diagnostica. Per altre informazioni, vedere [Registrazione diagnostica e metriche per il gateway applicazione](../../application-gateway/application-gateway-diagnostics.md).

## <a name="does-detection-mode-block-traffic"></a>La modalità di rilevamento blocca il traffico?

No. La modalità di rilevamento registra solo il traffico che ha attivato una regola WAF.

## <a name="can-i-customize-waf-rules"></a>È possibile personalizzare le regole del Web application firewall (WAF)?

Sì. Per altre informazioni, vedere [Personalizzare le regole e i gruppi di regole del web application firewall](application-gateway-customize-waf-rules-portal.md).

## <a name="what-rules-are-currently-available-for-waf"></a>Quali regole sono attualmente disponibili per il WAF?

Il WAF supporta attualmente CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) e [3.1](application-gateway-crs-rulegroups-rules.md#owasp31). Queste regole offrono una sicurezza di base rispetto alle 10 vulnerabilità più note identificate da Open Web Application Security Project (OWASP): 

* Protezione dagli attacchi SQL injection
* Protezione da attacchi tramite script da altri siti
* Protezione da attacchi Web comuni, come attacchi command injection, HTTP Request Smuggling, HTTP Response Splitting e Remote File Inclusion
* Protezione dalle violazioni del protocollo HTTP
* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header
* Prevenzione contro robot, crawler e scanner
* Rilevamento di errori di configurazione dell'applicazione comuni, ad esempio, Apache, IIS e così via.

Per altre informazioni, vedere [Le 10 vulnerabilità più comuni di OWASP](https://owasp.org/www-project-top-ten/).

## <a name="does-waf-support-ddos-protection"></a>WAF supporta la protezione DDoS?

Sì. È possibile abilitare Protezione DDos nella rete virtuale in cui viene distribuito il gateway applicazione. Questa impostazione assicura che anche l'indirizzo IP virtuale del gateway applicazione sia protetto dal servizio Protezione DDos di Azure.

### <a name="does-waf-store-customer-data"></a>WAF archivia i dati dei clienti?

No, WAF non archivia i dati dei clienti.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](../overview.md).
- Altre informazioni su [Frontdoor di Azure](../../frontdoor/front-door-overview.md).
