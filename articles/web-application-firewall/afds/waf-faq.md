---
title: Web Application Firewall di Azure nel servizio front-end di Azure-Domande frequenti
description: Questo articolo fornisce le risposte alle domande frequenti su Web Application Firewall in Azure front door
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.devlang: na
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 5c2763112b1aa2d58f5dc57cea72a3d0bdea961e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545670"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-azure-front-door-service"></a>Domande frequenti sul servizio Web Application Firewall di Azure nel servizio front door di Azure

Questo articolo risponde alle domande più comuni su Web Application Firewall (WAF) di Azure sulle funzionalità e sulle funzionalità del servizio front door di Azure. 

## <a name="what-is-azure-waf"></a>Cos'è il web application firewall di Azure?

Il WAF di Azure è un web application firewall che consente di proteggere le applicazioni Web da minacce comuni, quali attacchi SQL injection, script da altri siti e altri exploit Web. L'utente può definire un criterio WAF costituito da una combinazione di regole personalizzate e gestite per controllare l'accesso alle applicazioni Web.

I criteri WAF di Azure possono essere applicati alle applicazioni Web ospitate nel gateway applicazione o in Frontdoor di Azure.

## <a name="what-is-waf-on-azure-front-door"></a>Che cos'è WAF in front door di Azure? 

Il front-end di Azure è una rete per la distribuzione di contenuti e applicazioni distribuita a livello globale e altamente scalabile. Azure WAF, quando integrato con la porta anteriore, arresta gli attacchi Denial of Service e delle applicazioni mirate al perimetro della rete di Azure, vicino alle origini di attacco prima di immettere la rete virtuale, offre protezione senza sacrificare le prestazioni.

## <a name="does-azure-waf-support-https"></a>Azure WAF supporta HTTPS?

La porta anteriore offre l'offload TLS. WAF è integrato in modo nativo con sportello anteriore ed è in grado di ispezionare una richiesta dopo che è stata decrittografata.

## <a name="does-azure-waf-support-ipv6"></a>Azure WAF supporta IPv6?

Sì. È possibile configurare la restrizione IP per IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>Come sono aggiornati i set di regole gestiti?

Il nostro migliore è quello di rimanere al passo con il cambiamento del panorama delle minacce. Una volta aggiornata, una nuova regola viene aggiunta al set di regole predefinito con un nuovo numero di versione.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>Qual è il tempo di propagazione se si modifica il criterio WAF?

La distribuzione di un criterio WAF a livello globale richiede in genere circa 5 minuti e viene spesso completata prima.

## <a name="can-waf-policies-be-different-for-different-regions"></a>I criteri di WAF possono essere diversi per aree diverse?

Quando integrato con la porta anteriore, WAF è una risorsa globale. La stessa configurazione si applica a tutte le posizioni di sportello anteriore.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>Ricerca per categorie limitare l'accesso al back-end solo da sportello anteriore?

È possibile configurare l'elenco di controllo di accesso IP nel back-end per consentire solo gli intervalli di indirizzi IP in uscita della porta anteriore e negare l'accesso diretto da Internet. I tag di servizio sono supportati per l'uso nella rete virtuale. Inoltre, è possibile verificare che il campo dell'intestazione HTTP X-Inoltred-host sia valido per l'applicazione Web.

## <a name="which-azure-waf-options-should-i-choose"></a>Quali opzioni di Azure WAF scegliere?

Sono disponibili due opzioni per l'applicazione di criteri WAF in Azure. WAF con Azure front door è una soluzione di sicurezza perimetrale distribuita a livello globale. WAF con il gateway applicazione è una soluzione a livello di area dedicata. Si consiglia di scegliere una soluzione basata sui requisiti generali di prestazioni e sicurezza. Per altre informazioni, vedere [bilanciamento del carico con la suite di distribuzione delle applicazioni di Azure](../../frontdoor/front-door-lb-with-azure-app-delivery-suite.md).

## <a name="whats-the-recommended-approach-to-enabling-waf-on-front-door"></a>Qual è l'approccio consigliato per l'abilitazione di WAF sulla porta anteriore?

Quando si Abilita WAF in un'applicazione esistente, è comune avere rilevamenti falsi positivi in cui le regole WAF rilevano il traffico legittimo come minaccia. Per ridurre al minimo il rischio di un effetto sugli utenti, si consiglia il processo seguente:

* Abilitare WAF in modalità di [ **rilevamento**](./waf-front-door-create-portal.md#change-mode) per assicurarsi che WAF non blocchi le richieste mentre si sta lavorando al processo.
  > [!IMPORTANT]
  > Questo processo descrive come abilitare WAF in una soluzione nuova o esistente quando la priorità è ridurre al minimo il disturbo per gli utenti dell'applicazione. In caso di attacco o minaccia imminente, è consigliabile distribuire immediatamente il WAF in modalità di **prevenzione** e usare il processo di ottimizzazione per monitorare e ottimizzare il WAF nel tempo. Questo potrebbe causare il blocco del traffico legittimo, motivo per cui si consiglia di eseguire questa operazione solo quando si è in condizione di minaccia.
* Seguire le [linee guida per l'ottimizzazione del WAF](./waf-front-door-tuning.md). Per questo processo è necessario abilitare la registrazione diagnostica, esaminare regolarmente i registri e aggiungere le esclusioni delle regole e altre mitigazioni.
* Ripetere l'intero processo, controllando regolarmente i log, fino a quando non si è certi che non è stato bloccato alcun traffico legittimo. L'intero processo può richiedere alcune settimane. Idealmente dovrebbe essere visibile un minor numero di rilevamenti falsi positivi dopo ogni modifica apportata all'ottimizzazione.
* Infine, abilitare WAF in **modalità di prevenzione**.
* Anche dopo aver eseguito il WAF in produzione, è necessario tenere il monitoraggio dei log per identificare eventuali altri rilevamenti falsi positivi. La revisione regolare dei log consente inoltre di identificare eventuali tentativi di attacco reali bloccati.

## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>Sono supportate le stesse funzionalità WAF in tutte le piattaforme integrate?

Attualmente, le regole ModSec CRS 2.2.9, CRS 3,0 e CRS 3,1 sono supportate solo con WAF nel gateway applicazione. Le regole di limitazione della frequenza, del filtro geografico e del set di regole predefinite gestite di Azure sono supportate solo con WAF in Azure front door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>La protezione DDoS è integrata con la porta anteriore? 

Distribuito a livello globale ai bordi della rete di Azure, l'area anteriore di Azure può assorbire e isolare geograficamente gli attacchi di volume elevato. È possibile creare criteri WAF personalizzati per bloccare e classificare automaticamente gli attacchi http (s) limite che hanno firme note. Altre informazioni, è possibile abilitare protezione DDoS standard nel VNet in cui vengono distribuiti i back-end. I clienti di protezione DDoS di Azure standard ricevono vantaggi aggiuntivi, tra cui la protezione dei costi, la garanzia del contratto di contratto e l'accesso agli esperti del team di risposta rapida DDoS per assistenza immediata durante un attacco. Per altre informazioni, vedere [protezione DDoS sulla porta anteriore](../../frontdoor/front-door-ddos.md).

## <a name="why-do-additional-requests-above-the-threshold-configured-for-my-rate-limit-rule-get-passed-to-my-backend-server"></a>Perché le richieste aggiuntive oltre la soglia configurata per la regola limite di velocità vengono passate al server back-end?

Una regola per il limite di velocità può limitare il traffico eccessivamente elevato da qualsiasi indirizzo IP del client. È possibile configurare una soglia per il numero di richieste Web consentite da un indirizzo IP client durante una durata di un minuto o cinque minuti. Per il controllo della frequenza granulare, la limitazione della frequenza può essere combinata con condizioni di corrispondenza aggiuntive, ad esempio la corrispondenza dei parametri HTTP (S). 

Le richieste provenienti dallo stesso client spesso arrivano nello stesso server front-end. In tal caso, si vedranno richieste aggiuntive oltre la soglia che vengono bloccate immediatamente. 

Tuttavia, è possibile che le richieste provenienti dallo stesso client possano arrivare a un altro server front-end che non ha ancora aggiornato il contatore dei limiti di velocità. Ad esempio, il client può aprire una nuova connessione per ogni richiesta e la soglia è bassa. In questo caso, la prima richiesta al nuovo server front-end passerà il controllo del limite di velocità. Una soglia di limite di velocità viene in genere impostata come elevata per la difesa da attacchi di tipo Denial of Service da qualsiasi indirizzo IP del client. Per una soglia molto bassa, è possibile che vengano visualizzate richieste aggiuntive oltre la soglia.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [web application firewall di Azure](../overview.md).
- Altre informazioni su [Frontdoor di Azure](../../frontdoor/front-door-overview.md).