---
title: Firewall applicazione Web di Azure - Domande frequenti
description: In questo articolo vengono fornite le risposte alle domande frequenti su Web Application Firewall in Azure Front Door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: victorh
ms.openlocfilehash: c40210f3a6f0fb10be1f20deef87b2acfa076a4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460637"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Domande frequenti su Firewall applicazione Web di Azure in Azure Front Door Service

Questo articolo risponde alle domande comuni sulle caratteristiche e le funzionalità di WaF (Web Application Firewall) di Azure.This article answers common questions about Azure web application firewall (WAF) features and functionality. 

## <a name="what-is-azure-waf"></a>Che cos'è Azure WAF?

Azure WAF è un firewall per applicazioni Web che consente di proteggere le applicazioni Web da minacce comuni quali SQL injection, cross-site scripting e altri exploit Web. È possibile definire un criterio WAF costituito da una combinazione di regole personalizzate e gestite per controllare l'accesso alle applicazioni Web.

Un criterio WAF di Azure può essere applicato alle applicazioni Web ospitate nel gateway applicazione o nelle porte frontali di Azure.An Azure WAF policy can be applied to web applications hosted on Application Gateway or Azure Front Doors.

## <a name="what-is-waf-on-azure-front-door"></a>Che cos'è WAF on Azure Front Door? 

Azure Front Door è una rete di distribuzione di contenuti e applicazioni altamente scalabile e distribuita a livello globale. Azure WAF, quando è integrato con Front Door, blocca gli attacchi di applicazioni di tipo Denial-of-Service e mirati al perimetro della rete di Azure, vicino alle origini di attacco prima che entrino nella rete virtuale, offra protezione senza sacrificare le prestazioni.

## <a name="does-azure-waf-support-https"></a>Azure WAF supporta HTTPS?

Front Door offre la ripartizione del carico di lavoro SSL. WAF è nativamente integrato con Front Door e può ispezionare una richiesta dopo che è stata decrittografata.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF supporta IPv6?

Sì. È possibile configurare la restrizione IP per IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>In che modo sono aggiornati i set di regole gestite?

Facciamo del nostro meglio per stare al passo con il mutevole panorama delle minacce. Una volta aggiornata, una nuova regola viene aggiunta al set di regole predefinito con un nuovo numero di versione.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual è il tempo di propagazione se apposto una modifica alla mia politica WAF?

L'implementazione di una politica WAF a livello globale richiede in genere circa 5 minuti e spesso viene completata prima.

## <a name="can-waf-policies-be-different-for-different-regions"></a>Le politiche WAF possono essere diverse per le diverse regioni?

Quando è integrato con Front Door, WAF è una risorsa globale. La stessa configurazione si applica a tutte le posizioni della porta anteriore.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Come posso limitare l'accesso al mio back-end solo da Front Door?

È possibile configurare l'elenco di controllo di accesso IP nel back-end per consentire solo gli intervalli di indirizzi IP in uscita Front Door e negare qualsiasi accesso diretto da Internet. I tag di servizio sono supportati per l'uso nella rete virtuale. Inoltre, è possibile verificare che il campo di intestazione HTTP X-Forwarded-Host sia valido per l'applicazione Web.

## <a name="which-azure-waf-options-should-i-choose"></a>Quali opzioni WAF di Azure è consigliabile scegliere?

Sono disponibili due opzioni per l'applicazione di criteri WAF in Azure.There are two options when applying WAF policies in Azure. WAF with Azure Front Door è una soluzione di sicurezza perimetrale distribuita a livello globale. WAF with Application Gateway è una soluzione regionale e dedicata. È consigliabile scegliere una soluzione in base alle prestazioni complessive e ai requisiti di sicurezza. Per altre informazioni, vedere Bilanciamento del carico con la suite di distribuzione delle applicazioni di Azure.For more information, see [Load-balancing with Azure's application delivery suite](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Supportate le stesse funzionalità WAF in tutte le piattaforme integrate?

Attualmente, le regole ModSec CRS 2.2.9, CRS 3.0 e CRS 3.1 sono supportate solo con WAF nel gateway applicazione. Le regole relative alla limitazione della frequenza, al filtro geografico e al set di regole predefinito gestito da Azure sono supportate solo con WAF in Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>La protezione DDoS è integrata con Front Door? 

Distribuito a livello globale ai bordi della rete di Azure, Azure Front Door è in grado di assorbire e isolare geograficamente attacchi di grandi volumi. È possibile creare criteri WAF personalizzati per bloccare e valutare automaticamente gli attacchi http con limiti con firme note. Ulteriori ulteriori, è possibile abilitare DDoS Protection Standard sulla rete virtuale in cui vengono distribuiti i back-end. I clienti di Azure DDoS Protection Standard ricevono ulteriori vantaggi, tra cui protezione dei costi, garanzia del servizio di archiviazione e accesso agli esperti del team di risposta rapida Di DDoS per assistenza immediata durante un attacco.

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Perché le richieste aggiuntive al di sopra della soglia configurata per la regola del limite di frequenza vengono passate al server back-end?

Una regola di limite di frequenza può limitare il traffico anormalmente elevato da qualsiasi indirizzo IP del client. È possibile configurare una soglia sul numero di richieste Web consentite da un indirizzo IP client per un periodo di durata di uno o cinque minuti. Per un controllo granulare della frequenza, la limitazione della frequenza può essere combinata con condizioni di corrispondenza aggiuntive, ad esempio la corrispondenza dei parametri HTTP(S). 

Le richieste provenienti dallo stesso client arrivano spesso allo stesso server Front Door. In tal caso, vedrai le richieste aggiuntive al di sopra della soglia vengono bloccate immediatamente. 

Tuttavia, è possibile che le richieste provenienti dallo stesso client arrivino a un server Front Door diverso che non ha ancora aggiornato il contatore dei limiti di frequenza. Ad esempio, il client può aprire una nuova connessione per ogni richiesta e la soglia è bassa. In questo caso, la prima richiesta al nuovo server Front Door supererebbe il controllo del limite di frequenza. Una soglia limite di frequenza è in genere impostata su un valore elevato per difendersi dagli attacchi Denial of Service da qualsiasi indirizzo IP del client. Per una soglia molto bassa, è possibile che vengano visualizzate richieste aggiuntive al di sopra della soglia.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Firewall applicazione Web di Azure](../overview.md).
- Ulteriori informazioni su [Azure Front Door](../../frontdoor/front-door-overview.md).
