---
title: Introduzione a Web application firewall di Azure
description: Questo articolo offre una panoramica di Web application firewall (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "79475788"
---
# <a name="what-is-azure-web-application-firewall"></a>Che cos'è Web Application Firewall di Azure?

Web Application Firewall (WAF) fornisce protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni. Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Gli attacchi SQL injection e quelli tramite scripting intersito sono tra i più comuni.

![Panoramica di WAF](media/overview/wafoverview.png)

La prevenzione di tali attacchi nel codice dell'applicazione è un'operazione complessa. Può richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un web application firewall centralizzato semplifica di molto la gestione della sicurezza. Un WAF inoltre offre agli amministratori delle applicazioni una migliore garanzia di protezione da minacce e intrusioni.

Una soluzione WAF è in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione centralizzata di patch per una vulnerabilità nota, invece di proteggere ogni singola applicazione Web.

## <a name="supported-service"></a>Servizio supportato

WAF può essere distribuito con Gateway applicazione di Azure, con Frontdoor di Azure e con il servizio di rete di distribuzione dei contenuti di Azure di Microsoft. WAF nella rete CDN di Azure è attualmente disponibile in anteprima pubblica.  WAF include funzionalità personalizzate per ogni servizio specifico. Per altre informazioni sulle funzionalità di WAF per ciascun servizio, vedere la panoramica relativa ad ognuno di questi.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Web Application Firewall nel gateway applicazione, vedere [Web Application Firewall nel Gateway applicazione di Azure](./ag/ag-overview.md).
- Per altre informazioni su Web Application Firewall nel servizio Frontdoor di Azure, vedere [Web Application Firewall nel servizio Frontdoor di Azure](./afds/afds-overview.md).
- Per altre informazioni su Web Application Firewall nel servizio di rete CDN di Azure, vedere [Web Application Firewall nel servizio di rete CDN di Azure](./cdn/cdn-overview.md)
