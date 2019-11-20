---
title: Servizio front door di Azure-Domande frequenti
description: Questa pagina fornisce le risposte alle domande frequenti sul servizio Azure front door
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
ms.openlocfilehash: c8f95a1de85fd2eb00b0525fca8e62ade87dd57f
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184646"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Domande frequenti sul servizio Azure front door

Questo articolo fornisce le risposte alle domande comuni sulle funzionalità e sulle funzionalità del servizio front door di Azure. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Servizio front door di Azure UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Supporto tecnico Microsoft:** Per creare una nuova richiesta di supporto, nella portale di Azure della scheda **Guida** selezionare il pulsante Guida e **supporto** e quindi selezionare **nuova richiesta di supporto**.

## <a name="general"></a>General

### <a name="what-is-azure-front-door-service"></a>Informazioni sul servizio Frontdoor di Azure

Il servizio front-end di Azure è un servizio di Application Delivery Network (ADN), che offre diverse funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Fornisce accelerazione sito dinamico (DSA) insieme al bilanciamento del carico globale con failover quasi in tempo reale. Si tratta di un servizio a disponibilità elevata e scalabile, completamente gestito da Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Quali funzionalità sono supportate dal servizio front door di Azure?

Il servizio front-end di Azure supporta accelerazione sito dinamico (DSA), offload SSL e SSL end-to-end, Web Application Firewall, affinità di sessione basata su cookie, routing basato su percorso URL, certificati gratuiti e più gestione del dominio e altro ancora. Per un elenco completo delle funzionalità supportate, vedere [Panoramica del servizio front door di Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Qual è la differenza tra il servizio front-end di Azure e applicazione Azure gateway?

Anche se la porta anteriore e il gateway applicazione sono servizi di bilanciamento del carico di livello 7 (HTTP/HTTPS), la differenza principale è che la porta anteriore è un servizio globale, mentre il gateway applicazione è un servizio a livello di area. Mentre la porta anteriore è in grado di bilanciare il carico tra le diverse unità di scala/cluster/unità di timbri tra le aree, il gateway applicazione consente di bilanciare il carico tra le macchine virtuali/contenitori e così via, all'interno dell'unità di scala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando è necessario distribuire un gateway applicazione dietro la porta anteriore?

Gli scenari principali per cui è consigliabile usare il gateway applicazione dietro la porta anteriore sono:

- Lo sportello anteriore può eseguire il bilanciamento del carico basato sul percorso solo a livello globale, ma se si vuole bilanciare il carico del traffico ancora più all'interno della rete virtuale (VNET), è necessario usare il gateway applicazione.
- Poiché la porta anteriore non funziona a livello di macchina virtuale o contenitore, non è possibile eseguire lo svuotamento della connessione. Tuttavia, il gateway applicazione consente di eseguire lo svuotamento della connessione. 
- Con un gateway applicazione dietro a AFD, è possibile ottenere il 100% di offload SSL e instradare solo le richieste HTTP all'interno della rete virtuale (VNET).
- La porta anteriore e il gateway applicazione supportano entrambi l'affinità di sessione. Mentre la porta anteriore può indirizzare il traffico successivo da una sessione utente allo stesso cluster o back-end in una determinata area, il gateway applicazione può indirizzare il traffico creare affinità tra al server all'interno del cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>È possibile distribuire Azure Load Balancer dietro la porta anteriore?

Il servizio front door di Azure necessita di un indirizzo VIP pubblico o di un nome DNS disponibile pubblicamente per indirizzare il traffico a. La distribuzione di un Azure Load Balancer dietro la porta anteriore è un caso d'uso comune.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Quali protocolli supportano il servizio front door di Azure?

Il servizio front door di Azure supporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>In che modo il servizio Azure front door supporta HTTP/2?

Il supporto del protocollo HTTP/2 è disponibile solo per i client che si connettono al servizio front door di Azure. La comunicazione ai back-end nel pool back-end è su HTTP/1.1. Il supporto HTTP/2 è abilitato per impostazione predefinita.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quali risorse sono supportate oggi nell'ambito del pool back-end?

I pool back-end possono essere costituiti da archiviazione, app Web, istanze Kubernetes o qualsiasi altro nome host personalizzato con connettività pubblica. Il servizio front door di Azure richiede che i backend siano definiti tramite un indirizzo IP pubblico o un nome host DNS risolvibile pubblicamente. I membri dei pool back-end possono trovarsi in zone, aree o anche all'esterno di Azure, purché dispongano di connettività pubblica.

### <a name="what-regions-is-the-service-available-in"></a>In quale aree è disponibile il servizio?

Il servizio front door di Azure è un servizio globale e non è associato ad alcuna area di Azure specifica. L'unica posizione che è necessario specificare durante la creazione di una porta anteriore è la posizione del gruppo di risorse, che in sostanza specifica la posizione in cui verranno archiviati i metadati per il gruppo di risorse. La risorsa della porta anteriore stessa viene creata come risorsa globale e la configurazione viene distribuita a livello globale in tutti i pop (punto di presenza). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quali sono i percorsi POP per il servizio front door di Azure?

Il servizio front-end di Azure presenta lo stesso elenco di località POP (Point of Presence) della rete CDN di Azure di Microsoft. Per l'elenco completo dei pop, vedere le [località pop della rete CDN di Azure di Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Il servizio front-end di Azure è una distribuzione dedicata per l'applicazione o è condivisa tra i clienti?

Il servizio Azure front door è un servizio multi-tenant distribuito a livello globale. Quindi, l'infrastruttura per la porta anteriore è condivisa tra tutti i clienti. Tuttavia, creando un profilo di porta anteriore, si definisce la configurazione specifica richiesta per l'applicazione e non vengono apportate modifiche alla porta anteriore che influiscano su altre configurazioni di porte anteriori.

### <a name="is-http-https-redirection-supported"></a>È supportato il reindirizzamento HTTP->HTTPS?

Sì. In realtà, il servizio front door di Azure supporta il reindirizzamento di host, percorsi e stringhe di query, nonché parte del reindirizzamento URL. Altre informazioni sul [Reindirizzamento URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In quale ordine vengono elaborate le regole di routing?

Le route per la porta anteriore non sono ordinate e viene selezionata una route specifica in base alla migliore corrispondenza. Altre informazioni sul [modo in cui lo sportello anteriore corrisponde alle richieste a una regola di routing](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Ricerca per categorie bloccare l'accesso al back-end solo alla porta di ingresso di Azure?

Per bloccare l'applicazione in modo che accetti il traffico solo da un sportello anteriore specifico, è necessario configurare gli ACL IP per il back-end e quindi limitare il set di valori accettati per l'intestazione ' X-Inoltred-host ' inviata da Azure front door. Questi passaggi sono descritti di seguito:

- Configurare ACLing IP per i back-end in modo che accettino il traffico dallo spazio degli indirizzi IP back-end di Azure front door e dai servizi di infrastruttura di Azure. Si sta lavorando per l'integrazione con gli [intervalli IP di Azure e i tag di servizio,](https://www.microsoft.com/download/details.aspx?id=56519) ma per ora è possibile fare riferimento agli intervalli IP come indicato di seguito:
 
    - Spazio IP back-end **IPv4** dello sportello anteriore: `147.243.0.0/16`
    - Spazio IP back-end **IPv6** dello sportello anteriore: `2a01:111:2050::/44`
    - Servizi di [infrastruttura di base](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) di Azure tramite indirizzi IP host virtualizzati: `168.63.129.16` e `169.254.169.254`

    > [!WARNING]
    > Lo spazio IP back-end della porta anteriore potrebbe cambiare in un secondo momento, tuttavia, prima che ciò accada, avremmo integrato gli [intervalli IP di Azure e i tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519). È consigliabile sottoscrivere gli [intervalli IP e i tag di servizio di Azure](https://www.microsoft.com/download/details.aspx?id=56519) per eventuali modifiche o aggiornamenti.

-   Filtrare in base ai valori per l'intestazione in ingresso '**X-inoltred-host**' inviata dalla porta anteriore. Gli unici valori consentiti per l'intestazione devono essere tutti gli host front-end definiti nella configurazione della porta anteriore. In realtà, in modo più specifico, solo i nomi host per cui si vuole accettare il traffico, in questo particolare back-end.
    - Esempio: si può dire che la configurazione della porta anteriore dispone dei seguenti host front-end _`contoso.azurefd.net`_ (A), _`www.contoso.com`_ (B), _ (C) e _`notifications.contoso.com`_ (D). Si supponga di avere due backend X e Y. 
    - Il back-end X deve solo prendere il traffico dai nomi host A e B. il back-end Y può prendere il traffico da A, C e D.
    - Quindi, nel back-end X è necessario accettare solo il traffico con l'intestazione "**X-inoltred-host**" impostata su _`contoso.azurefd.net`_ o _`www.contoso.com`_ . Per tutte le altre operazioni, il back-end X deve rifiutare il traffico.
    - Analogamente, nel back-end Y è necessario accettare solo il traffico con l'intestazione "**X-inoltred-host**" impostata su _`contoso.azurefd.net`_ , _`api.contoso.com`_ o _`notifications.contoso.com`_ . Per tutte le altre operazioni, il back-end Y deve rifiutare il traffico.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>È possibile modificare l'IP anycast per la durata della mia porta anteriore?

L'IP anycast front-end per la porta anteriore in genere non cambia e può rimanere statico per la durata della porta anteriore. Non sono tuttavia previste **garanzie** per lo stesso. Non eseguire alcuna dipendenza diretta sull'IP.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Il servizio front door di Azure supporta IP statici o dedicati?

No, il servizio front-end di Azure attualmente non supporta gli IP anycast anycast statici o dedicati. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Il servizio front door di Azure supporta le intestazioni x-inoltred-for?

Sì, il servizio front door di Azure supporta le intestazioni x-Inoltred-for, X-Inoltred-host e X-Inoltred-proto. Per X-Inoltred-per se l'intestazione è già presente, l'indirizzo IP del socket client viene aggiunto al connettore. In caso contrario, aggiunge l'intestazione con l'indirizzo IP del socket client come valore. Per X-Inoltred-host e X-Inoltred-proto, il valore viene sottoposto a override.

Altre informazioni sulle [intestazioni HTTP supportate per la porta anteriore](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo è necessario per distribuire un servizio front-end di Azure? Lo sportello anteriore continua a funzionare durante l'aggiornamento?

Una nuova creazione di porte anteriori o qualsiasi aggiornamento a una porta anteriore esistente richiede da 3 a 5 minuti per la distribuzione globale. Questo significa che in circa 3-5 minuti la configurazione della porta anteriore verrà distribuita in tutti i nostri pop a livello globale.

Nota: per la distribuzione globale degli aggiornamenti dei certificati SSL personalizzati sono necessari circa 30 minuti.

## <a name="configuration"></a>Configurazione

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Il servizio di bilanciamento del carico di Azure può instradare il traffico all'interno di una rete virtuale?

Il servizio di Azure front door (AFD) richiede un indirizzo IP pubblico o un nome DNS risolvibile pubblicamente per instradare il traffico. Quindi, la risposta non è direttamente in grado di eseguire il routing all'interno di una rete virtuale, ma l'uso di un gateway applicazione o di un Azure Load Balancer tra risolvono questo scenario.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quali sono i diversi timeout e limiti per il servizio front door di Azure?

Informazioni su tutti i [timeout e i limiti documentati per il servizio front door di Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Prestazioni

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>In che modo il servizio Azure front door supporta la disponibilità e la scalabilità elevate?

Il servizio front door di Azure è una piattaforma multi-tenant distribuita a livello globale con grandi volumi di capacità per soddisfare le esigenze di scalabilità dell'applicazione. Distribuito dal perimetro della rete globale di Microsoft, la porta anteriore fornisce funzionalità di bilanciamento del carico globale che consentono di eseguire il failover dell'intera applicazione o anche di singoli microservizi tra aree o cloud diversi.

## <a name="ssl-configuration"></a>Configurazione SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Quali versioni di TLS sono supportate dal servizio front door di Azure?

Tutti i profili di porte anteriori creati dopo il 2019 settembre usano TLS 1,2 come minimo predefinito.

Front door supporta le versioni di TLS 1,0, 1,1 e 1,2. TLS 1,3 non è ancora supportato.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Quali certificati sono supportati nel servizio front door di Azure?

Per abilitare il protocollo HTTPS per la distribuzione sicura di contenuto in un dominio personalizzato di una porta anteriore, è possibile scegliere di usare un certificato gestito dal servizio front door di Azure o usare il proprio certificato.
L'opzione di gestione front-end effettua il provisioning di un certificato SSL standard tramite DigiCert e viene archiviato nel Key Vault di porte anteriori. Se si sceglie di usare il proprio certificato, è possibile caricare un certificato da una CA supportata e può essere un certificato SSL standard, un certificato di convalida estesa o anche un certificato con caratteri jolly. I certificati autofirmati non sono supportati. Informazioni [su come abilitare HTTPS per un dominio personalizzato](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Lo sportello anteriore supporta la rotazione automatica dei certificati?

Per l'opzione del certificato gestito dalla porta anteriore, i certificati vengono ruotati in base alla porta anteriore. Se si usa un certificato gestito da sportello anteriore e si verifica che la data di scadenza del certificato è inferiore a 60 giorni, archiviare un ticket di supporto.
</br>Per il certificato SSL personalizzato, la rotazione automatica non è supportata. Analogamente al modo in cui è stato configurato la prima volta per un determinato dominio personalizzato, è necessario puntare davanti alla versione del certificato corretta nell'Key Vault e assicurarsi che l'entità servizio per la porta anteriore abbia ancora accesso al Key Vault. Questa operazione di implementazione del certificato aggiornata dalla porta anteriore è atomica e non provoca alcun effetto di produzione, purché il nome del soggetto o la SAN per il certificato non cambino.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quali sono i pacchetti di crittografia attualmente supportati dal servizio front door di Azure?

Di seguito sono riportati i pacchetti di crittografia attualmente supportati dal servizio front door di Azure:

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

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Il servizio front door di Azure supporta anche la ricrittografia del traffico nel back-end?

Sì, il servizio front door di Azure supporta l'offload SSL e l'SSL end-to-end, che consente di crittografare nuovamente il traffico nel back-end. Infatti, poiché le connessioni al back-end avvengono tramite l'indirizzo IP pubblico, è consigliabile configurare la porta anteriore per l'uso di HTTPS come protocollo di trasmissione.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>È possibile configurare i criteri SSL per gestire le versioni del protocollo SSL?

È possibile configurare una versione minima di TLS nella porta anteriore di Azure tramite l' [API REST di Azure](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Attualmente, è possibile scegliere tra 1,0 e 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>È possibile configurare la porta anteriore per supportare solo pacchetti di crittografia specifici?

No, la configurazione della porta anteriore per specifici pacchetti di crittografia non è supportata. 

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Quali tipi di metriche e log sono disponibili con il servizio front door di Azure?

Per informazioni sui log e altre funzionalità di diagnostica, vedere [monitoraggio di metriche e log per lo sportello anteriore](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Che cos'è il criterio di conservazione per i log di diagnostica?

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a log di Monitoraggio di Azure. Per altre informazioni, vedere [diagnostica del servizio front door di Azure](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Ricerca per categorie ottenere i log di controllo per il servizio front door di Azure?

I log di controllo sono disponibili per il servizio front door di Azure. Nel portale fare clic su **log attività** nel pannello dei menu della porta anteriore per accedere al log di controllo. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>È possibile impostare avvisi con il servizio front door di Azure?

Sì, il servizio Azure front door supporta gli avvisi. Gli avvisi vengono configurati nelle metriche. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come creare un Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
