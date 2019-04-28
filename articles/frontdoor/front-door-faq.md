---
title: Servizio porta d'ingresso Azure - domande frequenti per l'ingresso principale | Microsoft Docs
description: Questa pagina vengono fornite le risposte alle domande frequenti sul servizio di ingresso principale di Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736667"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Domande frequenti per il servizio di ingresso principale di Azure

Questo articolo risponde a domande comuni sulle funzionalità e le funzionalità del servizio di ingresso principale di Azure. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Azure porta d'ingresso del servizio UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Supporto tecnico Microsoft:** Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida**, selezionare **Guida e supporto** e quindi selezionare **Nuova richiesta di supporto**.

## <a name="general"></a>Generale

### <a name="what-is-azure-front-door-service"></a>Informazioni sul servizio Frontdoor di Azure

Porta principale di Azure è un Application Delivery Network (ADN) come servizio, che offre varie funzionalità livello 7 con bilanciamento del carico per le applicazioni. Fornisce l'accelerazione sito dinamico (DSA) insieme a bilanciamento del carico globale con quasi in tempo reale failover. È un servizio altamente disponibile e scalabile, che viene gestito completamente da Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Quali funzionalità supporta il servizio di ingresso principale di Azure?

Servizio di ingresso principale di Azure supporta l'accelerazione sito dinamico (DSA), l'offload SSL e SSL end-to-end, Web Application Firewall, affinità di sessione basata su cookie, routing basato su percorso url, gratuiti certificati e gestione di dominio più e ad altri utenti. Per un elenco completo delle funzionalità supportate, vedere [Panoramica del servizio di ingresso principale Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Che cos'è la differenza tra il servizio di ingresso principale di Azure e Gateway applicazione di Azure?

Sebbene sia porta principale e il Gateway applicazione sono di livello 7 (HTTP/HTTPS) di bilanciamento del carico, la differenza principale è che porta d'ingresso è un servizio globale, mentre il Gateway applicazione è un servizio a livello di area. Durante l'ingresso principale può bilanciare il carico tra le unità di cluster/unità/timbro scala diversa tra aree, il Gateway applicazione consente di bilanciare il carico tra le via VM/contenitori che si trova all'interno di unità di scala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando dovremmo si distribuisce un Gateway applicazione dietro porta d'ingresso?

Gli scenari principali motivi per cui uno deve usare il Gateway applicazione dietro porta d'ingresso sono:

- Porta d'ingresso può eseguire solo a livello globale ma se si desidera bilanciare il carico ulteriormente interni alla propria rete virtuale (VNET), quindi devono usare il Gateway applicazione di bilanciamento del carico basato sul percorso.
- Poiché l'ingresso principale non funziona a livello della macchina virtuale/contenitore, pertanto, ma non può fare lo svuotamento della connessione. Tuttavia, il Gateway applicazione consente di eseguire lo svuotamento della connessione. 
- Con un Gateway applicazione dietro AFD, uno può raggiungere 100% l'offload SSL e l'invio di solo le richieste HTTP interni alla propria rete virtuale (VNET).
- Porta principale e il Gateway applicazione supporta l'affinità di sessione. Durante l'ingresso principale può indirizzare il traffico successivo proveniente da una sessione utente allo stesso cluster o back-end in una determinata area, il Gateway applicazione direct possibile impostare l'affinità del traffico allo stesso server all'interno del cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Possiamo distribuire dietro l'ingresso principale di Azure Load Balancer?

Porta principale di Azure richiede un indirizzo VIP pubblico o un nome DNS disponibile pubblicamente per instradare il traffico. La distribuzione di un bilanciamento del carico di Azure protetti da porta principale è un comune caso d'uso.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Quali protocolli supporta il servizio di ingresso principale di Azure?

Servizio di ingresso principale di Azure supporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Come servizio di ingresso principale di Azure supporta HTTP/2?

Supporto del protocollo HTTP/2 è disponibile ai client solo la connessione al servizio di ingresso principale di Azure. La comunicazione ai back-end nel pool di back-end è su HTTP/1.1. Supporto HTTP/2 è abilitato per impostazione predefinita.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quali risorse sono supportate oggi nell'ambito del pool back-end?

Pool back-end possono essere composte di archiviazione, App Web, le istanze di Kubernetes o qualsiasi altro nome host personalizzato che dispone di connettività pubblica. Servizio di ingresso principale di Azure richiede che il back-end vengono definiti tramite un indirizzo IP pubblico o un nome host DNS risolvibile pubblicamente. I membri del pool back-end possono essere tra più aree, aree, o anche all'esterno di Azure, purché dispongano di connettività pubblica con.

### <a name="what-regions-is-the-service-available-in"></a>In quale aree è disponibile il servizio?

Servizio di ingresso principale di Azure è un servizio globale e non è associato a qualsiasi area di Azure specifico. L'unica posizione, che è necessario specificare durante la creazione di una porta d'ingresso è la posizione del gruppo di risorse, che essenzialmente è che specifica in cui verranno archiviati i metadati per il gruppo di risorse. Risorsa di ingresso principale stessa viene creata come una risorsa globale e la configurazione viene distribuita a livello globale per tutti i POP (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quali sono le località POP per il servizio di ingresso principale di Azure?

Servizio di ingresso principale di Azure ha lo stesso elenco di località POP (Point of Presence) della rete CDN di Azure da Microsoft. Per l'elenco completo dei nostri POP, è sufficiente consultare [località POP della rete CDN di Azure da Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>È una distribuzione dedicata per l'applicazione di servizio di ingresso principale di Azure o è condivisa tra clienti?

Porta principale di Azure è un servizio multi-tenant distribuito a livello globale. Pertanto, l'infrastruttura per la porta d'ingresso è condivisa tra tutti i clienti. Tuttavia, tramite la creazione di una porta Front è definire la configurazione specifica necessaria per l'applicazione e 

### <a name="is-http-https-redirection-supported"></a>È supportato il reindirizzamento HTTP->HTTPS?

Porta d'ingresso attualmente non supporta il reindirizzamento degli URL.

### <a name="in-what-order-are-routing-rules-processed"></a>In quale ordine vengono elaborate le regole di routing?

Le route per l'ingresso principale non sono ordinate e una route specifica è selezionata in base alla corrispondenza migliore. Altre informazioni sulle [come porta principale corrisponde richieste a una regola di routing](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Come bloccare l'accesso per il back-end solo porta d'ingresso nel servizio di Azure?

È possibile configurare ACL di IP per il back-end per accettare il traffico solo dal servizio di ingresso principale di Azure. È possibile limitare l'applicazione accetti le connessioni in ingresso solo dallo spazio di IP back-end porta d'ingresso del servizio di Azure. Microsoft sta lavorando per l'integrazione con [intervalli di IP di Azure e i tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519) ma per ora, è possibile consultare gli intervalli IP come indicato di seguito:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Lo spazio di IP back-end può cambiare in un secondo momento, tuttavia, si garantirà che prima che questo accada, che si sarebbe stato integrato con [intervalli IP di Azure e i tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519). È consigliabile sottoscrivere [intervalli IP di Azure e i tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519) di eventuali modifiche o aggiornamenti. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>L'indirizzo IP anycast è possibile modificare in base alla durata del mio porta d'ingresso?

L'indirizzo IP anycast front-end per l'ingresso principale in genere non deve cambiare e potrebbe rimanere statico per la durata di ingresso principale. Tuttavia, esistono **offre alcuna garanzia** per lo stesso. Hanno dipendenze dirette sull'indirizzo IP.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Il servizio di ingresso principale di Azure supporta gli indirizzi IP statici o dedicato?

No, servizio di ingresso principale di Azure attualmente non supporta front-end dedicato o statici anycast gli indirizzi IP. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Il servizio di ingresso principale di Azure supporta le intestazioni x-forwarded-for?

Sì, servizio di ingresso principale di Azure supporta le intestazioni X-Forwarded-For, Host inoltrati X e X-Forwarded-Proto. Per X-Forwarded-For se era presente l'intestazione quindi porta d'ingresso aggiunge l'indirizzo IP socket client ad esso. In caso contrario, aggiunge un'intestazione con l'indirizzo IP socket client come valore. Per X-Forwarded-Proto e X-inoltrati-Host, il valore viene sovrascritto.

Altre informazioni sul [porta d'ingresso supportate le intestazioni HTTP](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo occorre per distribuire un servizio di ingresso principale di Azure? La porta d'ingresso ancora funziona quando viene aggiornata?

Una nuova porta d'ingresso durante la creazione o eventuali aggiornamenti a una porta di ingresso esistente richiede circa 3 a 5 minuti per la distribuzione globale. Ciò significa che circa 3 a 5 minuti, la configurazione della porta d'ingresso verrà distribuita a livello globale in tutti i POP.

Nota - Custom gli aggiornamenti del certificato SSL richiede circa 30 minuti per essere distribuito a livello globale.

## <a name="configuration"></a>Configurazione

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Traffico riesce a bilanciare il carico di ingresso principale di Azure o route all'interno di una rete virtuale?

Azure porta principale (AFD) richiede un indirizzo IP pubblico o il nome DNS pubblicamente risolvibile per instradare il traffico. Pertanto, la risposta è nessuna AFD non può instradare direttamente all'interno di una rete virtuale, ma con un Gateway applicazione o un Azure Load Balancer tra consentirà di risolvere questo scenario.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quali sono i diversi timeout e i limiti per il servizio di ingresso principale di Azure?

Scopri tutte le documentata [timeout e limiti per il servizio di ingresso principale di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestazioni

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Come servizio di ingresso principale di Azure supporta la disponibilità elevata e scalabilità?

Porta principale di Azure è una piattaforma multi-tenant distribuita a livello globale con volumi elevati di capacità per soddisfare le esigenze dell'applicazione della scalabilità. Porta d'ingresso recapitati dal perimetro della rete globale di Microsoft, fornisce funzionalità Bilanciamento del carico globale che consente di eseguire il failover l'intera applicazione o persino singoli microservizi tra aree o in diversi cloud.

## <a name="ssl-configuration"></a>Configurazione SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Quali versioni TLS sono supportate dal servizio di ingresso principale di Azure?

Porta d'ingresso supporta TLS versione 1.0, 1.1 e 1.2. TLS 1.3 non è ancora supportata.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Quali certificati sono supportati nel servizio di ingresso principale di Azure?

Per abilitare il protocollo HTTPS per distribuire in modo sicuro contenuti in un dominio personalizzato della porta d'ingresso, è possibile scegliere di usare un certificato gestito dal servizio di ingresso principale di Azure oppure usare il proprio certificato.
L'ingresso principale gestito esegue il provisioning opzione un certificato SSL standard tramite Digicert e archiviata in primo piano Key Vault della porta. Se si sceglie di usare il proprio certificato, quindi è possibile eseguire l'onboarding di un certificato rilasciato da un'autorità di certificazione supportato e può essere un SSL standard, il certificato di convalida estesa o anche un certificato con caratteri jolly. I certificati autofirmati non sono supportati. Scopri [come abilitare HTTPS per un dominio personalizzato](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quali sono i pacchetti di crittografia attualmente supportati dal servizio di ingresso principale di Azure?

Di seguito sono i pacchetti di crittografia attualmente supportati dal servizio di ingresso principale di Azure:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Servizio di ingresso principale di Azure supporta inoltre la riesecuzione della crittografia del traffico nel back-end?

Sì, servizio di ingresso principale di Azure supporta l'offload SSL e SSL end-to end, che crittografa nuovamente il traffico al back-end. Infatti, poiché le connessioni a back-end avere luogo tramite indirizzo IP pubblico, si consiglia di configurare l'ingresso principale per l'uso di HTTPS come protocollo di inoltro.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>È possibile configurare i criteri SSL per gestire le versioni del protocollo SSL?

No, attualmente non supporta ingresso principale per negare a specifiche versioni TLS né è possibile impostare le versioni TLS minimo. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>È possibile configurare la porta di ingresso per supportare solo pacchetti di crittografia specifici?

No, non è supportata la configurazione di ingresso principale per i pacchetti di crittografia specifici. 

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Quali tipi di log e metriche sono disponibili con il servizio di ingresso principale di Azure?

Per informazioni su log e altre funzionalità di diagnostica, vedere [il monitoraggio di log e metriche per porta d'ingresso](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Che cos'è il criterio di conservazione per i log di diagnostica?

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a log di Monitoraggio di Azure. Per altre informazioni, vedere [diagnostica del servizio Azure porta d'ingresso](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Come ottenere i log di controllo per il servizio di ingresso principale di Azure?

I log di controllo sono disponibili per il servizio di ingresso principale di Azure. Nel portale, fare clic su **Log attività** nel pannello del menu dell'ingresso principale per il log di controllo di accesso. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>È possibile impostare avvisi con il servizio di ingresso principale di Azure?

Sì, servizio di ingresso principale di Azure supporta gli avvisi. Gli avvisi vengono configurati nelle metriche. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).