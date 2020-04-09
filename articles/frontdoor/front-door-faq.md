---
title: Porta frontale di Azure - Domande frequenti
description: In questa pagina vengono fornite le risposte alle domande frequenti su Azure Front Door
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
ms.openlocfilehash: 0fe5d245d629c731a47ca5441afd2a3388a22de4
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878018"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Domande frequenti per la porta anteriore di Azure

Questo articolo risponde alle domande comuni sulle caratteristiche e le funzionalità di Azure Front Door.This article answers common questions about Azure Front Door features and functionality. Se non è presente la risposta a una domanda specifica, è possibile contattare Microsoft tramite i seguenti canali (in ordine di escalation):

1. Sezione dei commenti di questo articolo.
2. [Azure Front Door UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Supporto tecnico Microsoft:** Per creare una nuova richiesta di supporto, nel portale di Azure, nella scheda **Guida,** selezionare il pulsante **Guida e supporto** e quindi selezionare Nuova richiesta di **supporto**.

## <a name="general"></a>Generale

### <a name="what-is-azure-front-door"></a>Che cos'è Frontdoor di Azure?

Azure Front Door è una rete di distribuzione di applicazioni (ADN) come servizio, che offre varie funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Fornisce l'accelerazione dinamica del sito (DSA) insieme al bilanciamento del carico globale con failover quasi in tempo reale. Si tratta di un servizio altamente disponibile e scalabile, completamente gestito da Azure.It is a highly available and scalable service, which is fully managed by Azure.

### <a name="what-features-does-azure-front-door-support"></a>Quali funzionalità sono supportate da Azure Front Door?

Azure Front Door supporta l'accelerazione dinamica del sito (DSA), la ripartizione del carico di lavoro TLS/SSL e TLS end-to-end, il firewall delle applicazioni Web, l'affinità di sessione basata su cookie, il routing basato su percorso URL, i certificati gratuiti e la gestione di più domini e altri. Per un elenco completo delle funzionalità supportate, vedere [Panoramica di Azure Front Door](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Qual è la differenza tra Il portefrontale di Azure e il gateway applicazione di Azure?

Mentre sia Front Door che Application Gateway sono servizi di bilanciamento del carico di livello 7 (HTTP/HTTPS), la differenza principale è che Front Door è un servizio globale, mentre il gateway applicazione è un servizio regionale. Mentre Front Door è in grado di bilanciare il carico tra le diverse unità di scala/cluster/unità di timbro tra le aree, il gateway applicazione consente di bilanciare il carico tra le macchine virtuali/contenitori e così via che si trova all'interno dell'unità di scala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando è necessario distribuire un gateway applicazione dietro Front Door?

Gli scenari principali per cui è consigliabile utilizzare il gateway applicazione dietro Front Door sono:

- Front Door può eseguire il bilanciamento del carico basato su percorso solo a livello globale, ma se si desidera bilanciare ulteriormente il carico del traffico all'interno della rete virtuale (VNET), è necessario utilizzare il gateway applicazione.
- Poiché Front Door non funziona a livello di macchina virtuale/contenitore, non è possibile eseguire lo svuotamento della connessione. Tuttavia, il gateway applicazione consente di eseguire lo svuotamento della connessione. 
- Con un gateway applicazione dietro AFD, è possibile ottenere 100% offload TLS/SSL e instradare solo le richieste HTTP all'interno della propria rete virtuale (VNET).
- Front Door e Application Gateway supportano entrambe l'affinità di sessione. Mentre Front Door può indirizzare il traffico successivo da una sessione utente allo stesso cluster o back-end in una determinata area, il gateway applicazione può indirizzare l'affinità del traffico allo stesso server all'interno del cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>È possibile distribuire Azure Load Balancer dietro Front Door?

Azure Front Door richiede un indirizzo VIP pubblico o un nome DNS disponibile pubblicamente per instradare il traffico. La distribuzione di un servizio di bilanciamento del carico di Azure dietro la porta anteriore è un caso d'uso comune.

### <a name="what-protocols-does-azure-front-door-support"></a>Quali protocolli sono supportati da Azure Front Door?

La porta anteriore di Azure supporta HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>In che modo Azure Front Door supporta HTTP/2?

Il supporto del protocollo HTTP/2 è disponibile solo per i client che si connettono ad Azure Front Door.HTTP/2 protocol support is available to clients connecting to Azure Front Door only. La comunicazione ai back-end nel pool back-end è superiore a HTTP/1.1. Il supporto HTTP/2 è abilitato per impostazione predefinita.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quali risorse sono supportate oggi nell'ambito del pool back-end?

I pool back-end possono essere composti da archiviazione, app Web, istanze di Kubernetes o qualsiasi altro nome host personalizzato con connettività pubblica. Lo sportello anteriore di Azure richiede che i back-end siano definiti tramite un indirizzo IP pubblico o un nome host DNS risolvibile pubblicamente. I membri dei pool back-end possono trovarsi tra aree, aree o anche all'esterno di Azure, purché dispongano di connettività pubblica.

### <a name="what-regions-is-the-service-available-in"></a>In quale aree è disponibile il servizio?

Lo sportello frontale di Azure è un servizio globale e non è legato ad alcuna area di Azure specifica. L'unico percorso che è necessario specificare durante la creazione di una porta frontale è la posizione del gruppo di risorse, che è fondamentalmente specificando dove verranno archiviati i metadati per il gruppo di risorse. La risorsa Front Door stessa viene creata come risorsa globale e la configurazione viene distribuita globalmente a tutti i POP (Point of Presence). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Quali sono le posizioni POP per Lo stolto di Azure?

Azure Front Door has the same list of POP (Point of Presence) locations as Azure CDN from Microsoft. Per l'elenco completo dei POP, fare riferimento gentilmente alle [posizioni POP della rete CDN](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)di Azure di Microsoft .

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door è una distribuzione dedicata per l'applicazione o è condivisa tra i clienti?

Il front door di Azure è un servizio multi-tenant distribuito a livello globale. Così, l'infrastruttura per Front Door è condivisa tra tutti i suoi clienti. Tuttavia, creando un profilo Front Door, si definisce la configurazione specifica richiesta per l'applicazione e non sono state apportate modifiche alla porta anteriore, le configurazioni della porta frontale influiscono su altre configurazioni della porta anteriore.

### <a name="is-http-https-redirection-supported"></a>È supportato il reindirizzamento HTTP->HTTPS?

Sì. Infatti, Azure Front Door supporta il reindirizzamento di host, percorso e stringa di query, nonché parte del reindirizzamento degli URL. Ulteriori informazioni sul [reindirizzamento degli URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>In quale ordine vengono elaborate le regole di routing?

I percorsi per la porta anteriore non vengono ordinati e viene selezionato un percorso specifico in base alla corrispondenza migliore. Ulteriori informazioni su Come Front Door associa le richieste a una regola di [routing.](front-door-route-matching.md)

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Come è possibile bloccare l'accesso al back-end solo al server anteriore di Azure?

Per bloccare l'applicazione in modo che accetti traffico solo dalla porta frontale specifica, è necessario configurare gli ACL IP per il back-end e quindi limitare il traffico sul back-end al valore specifico dell'intestazione 'X-Azure-FDID' inviata da Front Door. Questi passaggi sono descritti in dettaglio come di seguito:These steps are detailed out as below:

- Configurare l'ACLIP affinché i back-end accettino il traffico proveniente dallo spazio di indirizzi IP back-end di Azure Front Door e solo dai servizi di infrastruttura di Azure.Configure IP ACLing for your backends to accept traffic from Azure Front Door's BACKend IP address space and Azure's infrastructure services only. Fare riferimento ai dettagli IP seguenti per l'utilizzo di ACLing del back-end:
 
    - Fare riferimento alla sezione *AzureFrontDoor.Backend* in [Intervalli IP di Azure e tag di servizio](https://www.microsoft.com/download/details.aspx?id=56519) per l'intervallo di indirizzi IP IPv4 di Front Door oppure è anche possibile usare il tag di servizio *AzureFrontDoor.Backend* nei gruppi di sicurezza di [rete](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) o con Firewall di [Azure.](https://docs.microsoft.com/azure/firewall/service-tags)
    - Lo spazio IP back-end **IPv6** di Front Door mentre è coperto nel tag del servizio non è elencato nel file JSON degli intervalli IP di Azure. Se si sta cercando un intervallo di indirizzi IPv6 esplicito, è attualmente limitato`2a01:111:2050::/44`
    - [Servizi di infrastruttura di base](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) di Azure `168.63.129.16` tramite indirizzi IP host virtualizzati: e`169.254.169.254`

    > [!WARNING]
    > Lo spazio IP back-end di Front Door può cambiare in un secondo momento, tuttavia, ci assicureremo che prima che ciò accada, che avremmo integrato con [gli intervalli IP](https://www.microsoft.com/download/details.aspx?id=56519)di Azure e tag di servizio . È consigliabile sottoscrivere [gli intervalli IP di Azure e](https://www.microsoft.com/download/details.aspx?id=56519) i tag di servizio per eventuali modifiche o aggiornamenti.

-    Eseguire un'operazione GET sul portello anteriore con la versione `2020-01-01` API o successiva. Nella chiamata API, `frontdoorID` cercare il campo. Filtrare l'intestazione in ingresso '**X-Azure-FDID**' inviata da Front `frontdoorID`Door al back-end con il valore come quello del campo . 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>L'IP anycast può cambiare nel corso della vita della mia Porta Front?

L'IP anycast frontend per la porta anteriore in genere non deve cambiare e può rimanere statico per tutta la durata della porta anteriore. Tuttavia, non ci sono **garanzie** per lo stesso. Si imeila no di prendere alcuna dipendenza diretta sul IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door supporta IP statici o dedicati?

No, il portetno anteriore di Azure attualmente non supporta gli indirizzi IP anycast del frontend statici o dedicati. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door supporta le intestazioni x-forwarded-for?

Sì, la porta anteriore di Azure supporta le intestazioni X-Forwarded-For, X-Forwarded-Host e X-Forwarded-Proto. Per X-Forwarded-For se l'intestazione era già presente, Front Door aggiunge l'IP del socket client. In caso contrario, aggiunge l'intestazione con il socket client IP come valore. Per X-Forwarded-Host e X-Forwarded-Proto, il valore viene sostituito.

Ulteriori informazioni sulle [intestazioni HTTP supportate da Front Door](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo è necessario per distribuire un server anteriore di Azure? La mia porta anteriore funziona ancora quando viene aggiornata?

Una nuova creazione di porte anteriori o eventuali aggiornamenti a una porta anteriore esistente richiede circa 3-5 minuti per la distribuzione globale. Ciò significa che tra circa 3 o 5 minuti, la configurazione della porta frontale verrà distribuita in tutti i nostri POP a livello globale.

Nota: gli aggiornamenti dei certificati TLS/SSL personalizzati richiedono circa 30 minuti per essere distribuiti a livello globale.

Eventuali aggiornamenti alle route o ai pool back-end e così via sono senza soluzione di continuità e non causeranno tempi di inattività (se la nuova configurazione è corretta). Gli aggiornamenti dei certificati sono anche atomici e non causeranno alcuna interruzione, a meno che non si passi da 'AFD Managed' a 'Usa il proprio certificato' o viceversa.


## <a name="configuration"></a>Configurazione

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Il bilanciamento del carico di Azure Front Door può instradare il traffico all'interno di una rete virtuale?

Il front-door di Azure (AFD) richiede un indirizzo IP pubblico o un nome DNS risolvibile pubblicamente per instradare il traffico. Pertanto, la risposta è che nessun AFD direttamente non può instradare all'interno di una rete virtuale, ma utilizzando un gateway applicazione o Azure Load Balancer in mezzo risolverà questo scenario.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quali sono i vari timeout e limiti per lo sportello anteriore di Azure?

Informazioni su tutti i timeout e i limiti documentati [per lo sportello anteriore](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-front-door-service-limits)di Azure.

## <a name="performance"></a>Prestazioni

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>In che modo Azure Front Door supporta la disponibilità elevata e la scalabilità?

Azure Front Door è una piattaforma multi-tenant distribuita a livello globale con enormi volumi di capacità per soddisfare le esigenze di scalabilità dell'applicazione. Fornito dal perimetro della rete globale di Microsoft, Front Door offre funzionalità di bilanciamento del carico globali che consentono di eseguire il failover dell'intera applicazione o anche di singoli microservizi in aree o cloud diversi.

## <a name="tls-configuration"></a>Configurazione TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Quali versioni TLS sono supportate da Azure Front Door?

Tutti i profili Front Door creati dopo settembre 2019 utilizzano TLS 1.2 come minimo predefinito.

Front Door supporta TLS versioni 1.0, 1.1 e 1.2. TLS 1.3 non è ancora supportato.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Quali certificati sono supportati in Azure Front Door?

Per abilitare il protocollo HTTPS per la distribuzione sicura del contenuto in un dominio personalizzato Front Door, è possibile scegliere di usare un certificato gestito da Azure Front Door o usare il proprio certificato.
L'opzione gestita Front Door effettua il provisioning di un certificato TLS/SSL standard tramite Digicert e archiviata nel Key Vault di Front Door. Se si sceglie di utilizzare il proprio certificato, è possibile caricare un certificato da un'autorità di certificazione supportata e può essere un TLS standard, un certificato di convalida estesa o anche un certificato con caratteri jolly. I certificati autofirmati non sono supportati. Informazioni su [come abilitare HTTPS per un dominio personalizzato.](https://aka.ms/FrontDoorCustomDomainHTTPS)

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door supporta la rotazione automatica dei certificati?

Per l'opzione Certificato gestito Porta anteriore, i certificati vengono ruotati automaticamente da Front Door. Se si utilizza un certificato gestito Front Door e si nota che la data di scadenza del certificato è a meno di 60 giorni di distanza, presentare un ticket di supporto.
</br>Per il proprio certificato TLS/SSL personalizzato, la rotazione automatica non è supportata. Analogamente a come è stato configurato la prima volta per un determinato dominio personalizzato, è necessario puntare Front Door alla versione corretta del certificato nel Vault delle chiavi e assicurarsi che l'entità servizio per Front Door abbia ancora accesso all'insieme di credenziali delle chiavi. Questa operazione aggiornata di implementazione del certificato da Front Door è atomica e non causa alcun impatto sulla produzione a condizione che il nome del soggetto o la SAN per il certificato non venga modificato.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Quali sono le suite di crittografia correnti supportate da Azure Front Door?

Di seguito sono riportati i gruppi di crittografia correnti supportati da Azure Front Door:

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
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>È possibile configurare i criteri TLS per controllare le versioni del protocollo TLS?

È possibile configurare una versione TLS minima in Azure Front Door nelle impostazioni HTTPS del dominio personalizzato tramite il portale di Azure o l'API REST di Azure.You can configure a minimum TLS version in Azure Front Door in the custom domain HTTPS settings via Azure portal or the [Azure REST API.](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) Attualmente, è possibile scegliere tra 1.0 e 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>È possibile configurare Front Door per supportare solo suite di crittografia specifiche?

No, la configurazione di Front Door per suite di crittografia specifiche non è supportata. Tuttavia, è possibile ottenere il proprio certificato TLS/SSL personalizzato dall'autorità di certificazione (ad esempio Verisign, Entrust o Digicert) e avere suite di crittografia specifiche contrassegnate sul certificato quando è stato generato. 

### <a name="does-front-door-support-ocsp-stapling"></a>Front Door supporta la pinzatura OCSP?

Sì, la graffatura OCSP è supportata per impostazione predefinita da Front Door e non è richiesta alcuna configurazione.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door supporta anche la ricrittografia del traffico verso il back-end?

Sì, la porta frontale di Azure supporta l'offload TLS/SSL e TLS end-to-end, che crittografa nuovamente il traffico verso il back-end. Infatti, poiché le connessioni al back-end avvengono tramite il proprio IP pubblico, è consigliabile configurare la porta frontale per l'utilizzo di HTTPS come protocollo di inoltro.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>Front Door supporta certificati autofirmati nel back-end per la connessione HTTPS?

No, i certificati autofirmati non sono supportati su Front Door e la restrizione si applica a entrambi:

1. **Backend**: Non è possibile utilizzare certificati autofirmati quando si inoltra il traffico come probe di integrità HTTPS o HTTPS o si compila la cache dall'origine per le regole di routing con la memorizzazione nella cache abilitata.
2. **Frontend**: Non è possibile utilizzare certificati autofirmati quando si utilizza un certificato TLS/SSL personalizzato per abilitare HTTPS nel dominio personalizzato.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Perché il traffico HTTPS verso il back-end non riesce?

Per avere esito positivo delle connessioni HTTPS al back-end sia per i probe di integrità che per le richieste di inoltro, potrebbero esserci due motivi per cui il traffico HTTPS potrebbe non riuscire:

1. **Nome soggetto del certificato non corrispondente**: Per le connessioni HTTPS, Front Door prevede che il back-end presenti il certificato di un'autorità di certificazione valida con i nomi soggetto corrispondenti al nome host back-end. Ad esempio, se il nome host `myapp-centralus.contosonews.net` back-end è impostato su e il certificato `myapp-centralus.contosonews.net` `*myapp-centralus*.contosonews.net` che il back-end presenta durante l'handshake TLS non ha né nel nome del soggetto, la porta frontale rifiuterà la connessione e genererà un errore. 
    1. **Soluzione:** sebbene non sia consigliabile dal punto di vista della conformità, è possibile risolvere questo errore disattivando il controllo del nome del soggetto del certificato per la porta anteriore. Questo è presente in Impostazioni nel portale di Azure e in BackendPoolsSettings nell'API.
2. **Certificato di hosting back-end da CA non valida:** solo i certificati provenienti da [CA valide](/azure/frontdoor/front-door-troubleshoot-allowed-ca) possono essere utilizzati nel back-end con Front Door. I certificati provenienti da CA interne o certificati autofirmati non sono consentiti.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Quali tipi di metriche e log sono disponibili con Lo scappatoio di Azure?

Per informazioni sui log e su altre funzionalità di diagnostica, vedere [Monitoraggio delle metriche e dei log per Front Door](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Che cos'è il criterio di conservazione per i log di diagnostica?

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a log di Monitoraggio di Azure. Per altre informazioni, vedere [Diagnostica porta frontale](front-door-diagnostics.md)di Azure .For more information, see Azure Front Door Diagnostics .

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Come si ottengono i log di controllo per lo sportello anteriore di Azure?

I log di controllo sono disponibili per lo secco anteriore di Azure.Audit logs are available for Azure Front Door. Nel portale fare clic su **Registro attività** nel pannello dei menu della porta frontale per accedere al registro di controllo. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>È possibile impostare avvisi con lo sportello anteriore di Azure?

Sì, lo sportello anteriore di Azure supporta gli avvisi. Gli avvisi vengono configurati nelle metriche. 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
