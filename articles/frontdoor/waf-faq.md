---
title: Azure web application firewall-domande frequenti
description: Questa pagina fornisce le risposte alle domande frequenti sul servizio Azure front door
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: f7d205920704ce8aedf3b2e3c07bd429b3c64964
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375336"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Domande frequenti su Azure web application firewall

Questo articolo risponde alle domande più comuni sulle funzionalità e sulle funzionalità di Azure web application firewall (WAF). 

## <a name="what-is-azure-waf"></a>Che cos'è Azure WAF?

Azure WAF è un web application firewall che aiuta a proteggere le applicazioni Web da minacce comuni, ad esempio SQL injection, script intersito e altri exploit Web. È possibile definire un criterio WAF costituito da una combinazione di regole personalizzate e gestite per controllare l'accesso alle applicazioni Web.

Un criterio di Azure WAF può essere applicato alle applicazioni Web ospitate nel gateway applicazione o nei servizi front door di Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>Che cos'è WAF per il servizio front door di Azure? 

Il front-end di Azure è una rete per la distribuzione di contenuti e applicazioni distribuita a livello globale e altamente scalabile. Azure WAF, quando integrato con la porta anteriore, arresta gli attacchi Denial of Service e delle applicazioni mirate al perimetro della rete di Azure, vicino alle origini di attacco prima di immettere la rete virtuale, offre protezione senza sacrificare le prestazioni.

## <a name="does-azure-waf-support-https"></a>Azure WAF supporta HTTPS?

Il servizio front door offre l'offload SSL. WAF è integrato in modo nativo con sportello anteriore ed è in grado di ispezionare una richiesta dopo che è stata decrittografata.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF supporta IPv6?

Sì. È possibile configurare la restrizione IP per IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Come sono aggiornati i set di regole gestiti?

Il nostro migliore è quello di rimanere al passo con il cambiamento del panorama delle minacce. Una volta aggiornata, una nuova regola viene aggiunta al set di regole predefinito con un nuovo numero di versione.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual è il tempo di propagazione se si modifica il criterio WAF?

La distribuzione di un criterio WAF a livello globale richiede in genere circa 5 minuti e viene spesso completata prima.

## <a name="can-waf-policies-be-different-for-different-regions"></a>I criteri di WAF possono essere diversi per aree diverse?

Quando integrato con il servizio front door, WAF è una risorsa globale. La stessa configurazione si applica a tutte le posizioni di sportello anteriore.
 

## <a name="which-azure-waf-options-should-i-choose"></a>Quali opzioni di Azure WAF scegliere?

Sono disponibili due opzioni per l'applicazione di criteri WAF in Azure. WAF con Azure front door è una soluzione di sicurezza perimetrale distribuita a livello globale. WAF con il gateway applicazione è una soluzione a livello di area dedicata. Si consiglia di scegliere una soluzione basata sui requisiti generali di prestazioni e sicurezza. Per altre informazioni, vedere [bilanciamento del carico con la suite di distribuzione delle applicazioni di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Sono supportate le stesse funzionalità WAF in tutte le piattaforme integrate?

Attualmente, le regole ModSec CRS 2.2.9 e CRS 3,0 sono supportate solo con WAF nel gateway applicazione. Le regole di limitazione della frequenza, del filtro geografico e del set di regole predefinite gestite di Azure sono supportate solo con WAF all'interno di Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>La protezione DDoS è integrata con la porta anteriore? 

Distribuito a livello globale ai bordi della rete di Azure, l'area anteriore di Azure può assorbire e isolare geograficamente gli attacchi di volume elevato. È possibile creare criteri WAF personalizzati per bloccare e classificare automaticamente gli attacchi http (s) limite che hanno firme note. Altre informazioni, è possibile abilitare protezione DDoS standard nel VNet in cui vengono distribuiti i back-end. I clienti di protezione DDoS di Azure standard ricevono vantaggi aggiuntivi, tra cui la protezione dei costi, la garanzia del contratto di contratto e l'accesso agli esperti del team di risposta rapida DDoS per assistenza immediata durante un attacco. 

È consigliabile bloccare i backend nell'ambiente di produzione per ridurre la superficie di attacco DDoS. Vedere [ricerca per categorie bloccare l'accesso al back-end solo per lo sportello anteriore di Azure?](https://docs.microsoft.com/en-us/azure/frontdoor/front-door-faq#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door)


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](waf-overview.md).
- Altre informazioni su [Azure front door](front-door-overview.md).
