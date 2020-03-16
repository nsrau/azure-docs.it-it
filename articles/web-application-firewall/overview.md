---
title: Introduzione a Web application firewall di Azure
description: Questo articolo offre una panoramica di Web application firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851181"
---
# <a name="what-is-azure-web-application-firewall"></a>Che cos'è Web Application Firewall di Azure?

Web Application Firewall (WAF) fornisce protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Gli attacchi SQL injection e quelli tramite scripting intersito sono tra i più comuni.

![Panoramica di WAF](media/overview/wafoverview.png)

La prevenzione di tali attacchi nel codice dell'applicazione è un'operazione complessa. Può richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un web application firewall centralizzato semplifica di molto la gestione della sicurezza. Un WAF inoltre offre agli amministratori delle applicazioni una migliore garanzia di protezione da minacce e intrusioni.

Una soluzione WAF è in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione centralizzata di patch per una vulnerabilità nota, invece di proteggere ogni singola applicazione Web.

## <a name="supported-services"></a>Servizi supportati

WAF può essere distribuito con [Gateway applicazione di Azure](../application-gateway/overview.md) e con il servizio [Frontdoor di Azure](../frontdoor/front-door-overview.md). Entrambi i servizi sono servizi di bilanciamento del carico di livello 7 (HTTP/S), ma Gateway applicazione è un servizio a livello di area e Frontdoor è un servizio globale. WAF include funzionalità personalizzate per ogni servizio specifico.

Per altre informazioni, vedere la panoramica di WAF per ogni servizio.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Web Application Firewall nel gateway applicazione, vedere [Web Application Firewall nel Gateway applicazione di Azure](./ag/ag-overview.md).
- Per altre informazioni su Web Application Firewall nel servizio Frontdoor di Azure, vedere [Web Application Firewall nel servizio Frontdoor di Azure](./afds/afds-overview.md).
