---
title: Firewall applicazione web di Azure - domande frequenti
description: Questa pagina vengono fornite le risposte alle domande frequenti sul servizio di ingresso principale di Azure
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: kumud;tyao
ms.openlocfilehash: 05d01851d0a3dc9df6c396e862ce93defd957c70
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788922"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Domande frequenti per il firewall applicazione web di Azure

Questo articolo risponde a domande comuni su Azure web application firewall (WAF) caratteristiche e funzionalità. 

## <a name="what-is-azure-waf"></a>Che cos'è Azure Web Application firewall?

Web Application firewall di Azure è un web application firewall che consente di proteggere le applicazioni web dalle minacce più comuni, ad esempio SQL injection, script intersito e altri exploit web. È possibile definire un criterio di Web Application firewall costituito da una combinazione di regole personalizzate e gestite per controllare l'accesso alle applicazioni web.

I criteri di Azure WAF è applicabile per le applicazioni web ospitate in servizi il Gateway applicazione o di ingresso principale di Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>What ' s Web Application firewall per il servizio di ingresso principale di Azure? 

Porta d'ingresso Azure è un'applicazione altamente scalabile e distribuito a livello globale e una rete CDN. Azure Web Application firewall, quando integrato con porta principale, arresta denial of service e attacchi di applicazione al perimetro della rete Azure di destinazione, vicino origini attacco prima che possa accedere alla rete virtuale, offre protezione senza sacrificare le prestazioni.

## <a name="how-will-i-be-charged-for-azure-waf-for-front-door"></a>Come viene addebitato per Web Application firewall di Azure per la porta d'ingresso?
Durante l'anteprima pubblica dell'utilizzo di Web Application firewall all'ingresso principale sono gratuite. Si noti che porta d'ingresso Addebito aggiuntivo. Ai prezzi del servizio di ingresso principale [qui](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="does-azure-waf-support-https"></a>Web Application firewall di Azure supporta HTTPS?

Il servizio di ingresso principale offre l'offload SSL. Web Application firewall in modo nativo è integrato con porta d'ingresso e ispezionabili una richiesta dopo la decrittografia.

## <a name="does-azure-waf-support-ipv6"></a>Web Application firewall di Azure supporta IPv6?

Sì. È possibile configurare restrizioni IP per IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Il livello di aggiornamento sono i set di regole gestite?

Microsoft si impegnerà a tenere il passo con la modifica panorama delle minacce. Una volta che viene aggiornata una nuova regola, viene aggiunto alla regola Set predefiniti con un nuovo numero di versione.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Che cos'è il tempo di propagazione se apporta una modifica al criterio Web Application firewall?

Distribuire un criterio di Web Application firewall a livello globale in genere richiede circa 5 minuti e spesso viene completata prima.

## <a name="can-waf-policies-be-different-for-different-regions"></a>I criteri di Web Application firewall possono essere diversi per regioni diverse?

Se è integrato con il servizio di ingresso principale, Web Application firewall è una risorsa globale. Stessa configurazione viene applicata a tutte le posizioni di ingresso principale.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Come è possibile limitare l'accesso al mio back-end devono essere solo da porta principale?

È possibile configurare l'elenco di controllo di accesso IP nel back-end per consentire solo porta principale in uscita intervalli di indirizzi IP e negare l'accesso diretto da Internet. I tag di servizio sono supportati per l'utilizzo della rete virtuale. Inoltre, è possibile verificare che il campo dell'intestazione HTTP X-inoltrati-Host sia valido per l'applicazione web.




## <a name="which-azure-waf-options-should-i-choose"></a>Quali opzioni di Web Application firewall di Azure scegliere?

Sono disponibili due opzioni quando si applicano i criteri di Web Application firewall in Azure. Web Application firewall con l'ingresso principale di Azure è una soluzione di sicurezza edge multimodello e distribuito. Web Application firewall con il Gateway applicazione è una soluzione a livello di area e dedicata. È consigliabile che scegliere una soluzione in base ai requisiti di prestazioni e la sicurezza complessivi. Per altre informazioni, vedere [bilanciamento del carico con la suite di recapito dell'applicazione di Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Stesse funzionalità di WAF sono supportate in tutte le piattaforme integrate?

Attualmente, ModSec CRS 2.2.9 e regole CRS 3.0 sono supportate solo con Web Application firewall nel Gateway applicazione. Limitazione di velocità, filtro geografico, Azure gestito predefinito del Set di regole regole e sono supportate solamente con Web Application firewall all'ingresso principale di Azure.

## <a name="is-ddos-protection-integrated-with-front-door"></a>Protezione DDoS è integrata con porta d'ingresso? 

Distribuito a livello globale i bordi di rete di Azure, Azure porta d'ingresso può absorb e geograficamente isolare gli attacchi di volume elevato. È possibile creare criteri personalizzati di Web Application firewall che blocca automaticamente e frequenza di ridurre gli attacchi HTTP (s) che sono note le firme. Inoltre, è possibile abilitare protezione DDoS Standard nella rete virtuale in cui vengono distribuiti i sistemi back-end. I clienti di protezione DDoS Standard di Azure riceveranno vantaggi aggiuntivi fra cui costi protezione, contratti di servizio e l'accesso a esperti dal Team di risposta rapidi DDoS per assistenza immediata durante un attacco. 

## <a name="next-steps"></a>Passaggi successivi

- Scopri [firewall applicazione web di Azure](waf-overview.md).
- Altre informazioni sulle [porta d'ingresso Azure](front-door-overview.md).
