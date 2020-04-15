---
title: Domande frequenti sul gateway applicazione di AzureFrequently asked questions about Azure Application Gateway
description: Risposte alle domande frequenti sul gateway applicazione di Azure.Find answers to frequently asked questions about Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 04/01/2020
ms.author: victorh
ms.openlocfilehash: e64b0a8602a4a0806ada15546972856743c38161
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312462"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Domande frequenti sul gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Di seguito sono riportate le domande frequenti sul gateway applicazione di Azure.The following are common questions asked about Azure Application Gateway.

## <a name="general"></a>Generale

### <a name="what-is-application-gateway"></a>Informazioni sul servizio Gateway applicazione

Il gateway applicazione di Azure fornisce un controller di distribuzione delle applicazioni (ADC) come servizio. Offre varie funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Questo servizio è altamente disponibile, scalabile e completamente gestito da Azure.This service is highly available, scalable, and fully managed by Azure.

### <a name="what-features-does-application-gateway-support"></a>Quali funzionalità supporta il gateway applicazione?

Il gateway applicazione supporta la scalabilità automatica, l'offload TLS e TLS end-to-end, un Web Application Firewall (WAF), l'affinità di sessione basata su cookie, il routing basato sul percorso URL, l'hosting multisito e altre funzionalità. Per un elenco completo delle funzionalità supportate, vedere [Cos'è il gateway applicazione di Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Differenze tra Gateway applicazione e Azure Load Balancer?

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7, ovvero funziona solo con il traffico Web (HTTP, HTTPS, WebSocket e HTTP/2). Supporta funzionalità quali la terminazione TLS, l'affinità di sessione basata su cookie e il round robin per il bilanciamento del carico del traffico. Load Balancer bilancia il carico del traffico al livello 4 (TCP o UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quali protocolli supporta il gateway applicazione?

Il gateway applicazione supporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>In che modo il gateway applicazione supporta HTTP/2?

Vedere [Supporto HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quali risorse sono supportate come parte di un pool back-end?

Vedere [le risorse back-end supportate](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>In quali aree è disponibile il gateway applicazione?

Il gateway applicazione è disponibile in tutte le aree di Azure globale. È disponibile anche in [Azure China 21Vianet](https://www.azure.cn/) e [Azure per enti pubblici.](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Questa distribuzione è dedicata alla sottoscrizione o è condivisa tra i clienti?

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Il gateway applicazione supporta il reindirizzamento da HTTP a HTTPS?

Il reindirizzamento è supportato. Vedere Panoramica del [reindirizzamento del gateway applicazione](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In quale ordine vengono elaborati i listener?

Vedere [l'ordine di elaborazione del listener](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Dove si trovano l'IP del gateway applicazione e il DNS?

Se si usa un indirizzo IP pubblico come endpoint, le informazioni IP e DNS sono disponibili nella risorsa indirizzo IP pubblico. In alternativa, individuarlo nel portale, nella pagina di panoramica del gateway applicazione. Se si utilizzano indirizzi IP interni, trovare le informazioni nella pagina di panoramica.

Per lo SKU v2, aprire la risorsa IP pubblica e selezionare **Configurazione**. Il campo **Etichetta nome DNS (facoltativo)** è disponibile per configurare il nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quali sono le impostazioni per il timeout Keep-Alive e il timeout di inattività TCP?

*Il timeout Keep-Alive* determina il tempo di attesa di un client per l'invio di un'altra richiesta HTTP su una connessione permanente prima di riutilizzarla o chiuderla. Il timeout di *inattività TCP* determina per quanto tempo una connessione TCP viene mantenuta aperta in caso di assenza di attività. 

Il *timeout Keep-Alive* nello SKU del gateway applicazione v1 è 120 secondi e nello SKU v2 è 75 secondi. Il timeout di *inattività TCP* è un valore predefinito di 4 minuti nell'IP virtuale front-end (VIP) dello SKU v1 e v2 del gateway applicazione. 

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>L'IP o il nome DNS cambia nel corso della durata del gateway applicazione?

Nello SKU del gateway applicazione V1, l'indirizzo VIP può cambiare se si arresta e si avvia il gateway applicazione. Tuttavia, il nome DNS associato al gateway applicazione non cambia nel corso della durata del gateway. Poiché il nome DNS non cambia, è necessario utilizzare un alias CNAME e puntare all'indirizzo DNS del gateway applicazione. Nello SKU del gateway applicazione V2 è possibile impostare l'indirizzo IP come statico, pertanto il nome IP e DNS non cambierà nel corso della durata del gateway applicazione. 

### <a name="does-application-gateway-support-static-ip"></a>Il gateway applicazione supporta l'IP statico?

Sì, lo SKU del gateway applicazione v2 supporta gli indirizzi IP pubblici statici. Lo SKU versione 1 supporta indirizzi IP interni statici.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?

Un gateway applicazione supporta un solo indirizzo IP pubblico.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quali dimensioni deve avere la subnet per il gateway applicazione?

Vedere [Considerazioni sulle dimensioni delle subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>È possibile distribuire più di una risorsa di gateway applicazione a una singola subnet?

Sì. Oltre a più istanze di una determinata distribuzione di Gateway applicazione, è possibile eseguire il provisioning di un'altra risorsa gateway applicazione univoca in una subnet esistente che contiene una risorsa gateway applicazione diversa.

Una singola subnet non può supportare insieme Standard_v2 e il gateway applicazione standard.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Il gateway applicazione v2 supporta le route definite dall'utente (UDR)?

Sì, ma solo scenari specifici. Per altre informazioni, vedere [Panoramica della configurazione del gateway applicazione](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Il gateway applicazione supporta le intestazioni x-forwarded-for?

Sì. Vedere [Modifiche a una richiesta](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo è necessario per distribuire un gateway applicazione? Il gateway dell'applicazione funzionerà durante l'aggiornamento?

Nuove distribuzioni di SKU versione 1 del gateway applicazione possono richiedere fino a 20 minuti per effettuare il provisioning. Le modifiche alle dimensioni o al conteggio dell'istanza non sono dannose e il gateway rimane attivo durante questo periodo.

Il provisioning della maggior parte delle distribuzioni che usano lo SKU v2 richiede circa 6 minuti. Tuttavia, può richiedere più tempo a seconda del tipo di distribuzione. Ad esempio, le distribuzioni in più zone di disponibilità con molte istanze possono richiedere più di 6 minuti. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>È possibile utilizzare Exchange Server come back-end con il gateway applicazione?

No. Il gateway applicazione non supporta protocolli di posta elettronica quali SMTP, IMAP e POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Sono disponibili indicazioni per eseguire la migrazione dallo SKU v1 allo SKU v2?

Sì. Per informazioni dettagliate, vedere [Migrate Azure Application Gateway and Web Application Firewall from v1 to v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Lo SKU del gateway applicazione v1 continuerà a essere supportato?

Sì. Lo SKU del gateway applicazione v1 continuerà a essere supportato. Tuttavia, è consigliabile passare alla versione 2 per sfruttare gli aggiornamenti delle funzionalità in tale SKU. Per ulteriori informazioni, vedere [Scalabilità automatica e gateway applicazione con ridondanza](application-gateway-autoscaling-zone-redundant.md)zona v2 .

## <a name="performance"></a>Prestazioni

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?

Lo SKU del gateway applicazione v1 supporta scenari a disponibilità elevata quando sono state distribuite due o più istanze. Azure distribuisce queste istanze tra i domini di aggiornamento e di errore per garantire che le istanze non abbiano esito negativo contemporaneamente. Lo SKU versione 1 supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

Lo SKU V2 garantisce automaticamente che le nuove istanze vengono distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza delle zone, le istanze più recenti vengono distribuite anche tra le zone di disponibilità per offrire la resilienza degli errori di zona.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Come è possibile ottenere uno scenario di ripristino di emergenza tra i data center utilizzando il gateway applicazione?

Usare Gestione traffico per distribuire il traffico tra più gateway applicazione in data center diversi.

### <a name="does-application-gateway-support-autoscaling"></a>Il gateway applicazione supporta la scalabilità automatica?

Sì, lo SKU versione 2 del gateway applicazione supporta la scalabilità automatica. Per ulteriori informazioni, vedere [Scalabilità automatica e gateway applicazione con ridondanza zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>La scalabilità verticale o automatica manuale o automatica causa tempi di inattività?

No. Le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

### <a name="does-application-gateway-support-connection-draining"></a>Il gateway applicazione supporta l'esaurimento delle connessioni?

Sì. È possibile configurare lo svuotamento della connessione per modificare i membri all'interno di un pool back-end senza interruzioni. Per ulteriori informazioni, vedere la sezione di [svuotamento della connessione del gateway applicazione](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?

Sì.

## <a name="configuration"></a>Configurazione

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Il gateway applicazione viene sempre distribuito in una rete virtuale?

Sì. Il gateway applicazione viene sempre distribuito in una subnet di rete virtuale. Questa subnet può contenere solo gateway applicazione. Per altre informazioni, vedere Requisiti di [rete virtuale e subnet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Il gateway applicazione può comunicare con istanze esterne alla rete virtuale o all'esterno della sottoscrizione?

Finché si dispone di connettività IP, il gateway applicazione può comunicare con le istanze esterne alla rete virtuale in cui si trovi. Il gateway applicazione può anche comunicare con istanze esterne alla sottoscrizione in cui si è. Se si prevede di usare gli indirizzi IP interni come membri del pool back-end, usare [il peering](../virtual-network/virtual-network-peering-overview.md) della rete virtuale o il gateway VPN di [Azure.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>È possibile distribuire altri elementi nella subnet del gateway applicazione?

No. Tuttavia, è possibile distribuire altri gateway applicazione nella subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?

Vedere Gruppi di sicurezza di [rete nella subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>La subnet del gateway applicazione supporta le route definite dall'utente?

Vedere [Route definite dall'utente supportate nella subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quali sono i limiti del gateway applicazione? È possibile aumentare questi limiti?

Vedere [Limiti del gateway applicazione](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>È possibile utilizzare contemporaneamente il gateway applicazione sia per il traffico esterno che per quello interno?

Sì. Il gateway applicazione supporta un indirizzo IP interno e un indirizzo IP esterno per ogni gateway applicazione.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Il gateway applicazione supporta il peering della rete virtuale?

Sì. Il peering di reti virtuali consente di bilanciare il carico del traffico in altre reti virtuali.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>È possibile comunicare con i server locali quando sono connessi tramite tunnel ExpressRoute o VPN?

Sì, se il traffico è consentito.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Un pool back-end può gestire molte applicazioni su porte diverse?

L'architettura dei microservizi è supportata. Per eseguire il probe su porte diverse, è necessario configurare più impostazioni HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>I probe personalizzati supportano i caratteri jolly o le aree reex nei dati di risposta?

No. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Come vengono elaborate le regole di routing nel gateway applicazione?

Vedere [Ordine delle regole di elaborazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Per i probe personalizzati, cosa significa il campo Host?

Il campo Host specifica il nome a cui inviare il probe quando è stato configurato il multisito nel gateway applicazione. In caso contrario, utilizzare '127.0.0.1'. Questo valore è diverso dal nome host della macchina virtuale. Il suo \<\>formato\<\>è\<\>\<protocol\>:// host : percorso porta .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>È possibile consentire l'accesso al gateway applicazione solo a pochi indirizzi IP di origine?

Sì. Vedere [Limitare l'accesso a IP di origine specifici](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>È possibile utilizzare la stessa porta sia per gli ascoltatori pubblici che per quelli privati?

No.

### <a name="does-application-gateway-support-ipv6"></a>Il gateway applicazione supporta IPv6?

Il gateway applicazione v2 non supporta attualmente IPv6. Può operare in una rete virtuale dual stack utilizzando solo IPv4, ma la subnet del gateway deve essere solo IPv4. Il gateway applicazione v1 non supporta le reti virtuali dual stack. 

## <a name="configuration---tls"></a>Configurazione - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Quali certificati supporta noto da Gateway applicazione?

Il gateway applicazione supporta certificati autofirmati, certificati di autorità di certificazione (CA), certificati EV (Extended Validation) e certificati con caratteri jolly.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quali suite di crittografia sono supportate da Application Gateway?

Il gateway applicazione supporta i seguenti pacchetti di crittografia. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Per informazioni su come personalizzare le opzioni TLS, vedere [Configurare le versioni](application-gateway-configure-ssl-policy-powershell.md)dei criteri TLS e i pacchetti di crittografia nel gateway applicazione .

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Il gateway applicazione supporta la ricrittografia del traffico verso il back-end?

Sì. Il gateway applicazione supporta l'offload TLS e il TLS end-to-end, che ricrittografano il traffico verso il back-end.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>È possibile configurare i criteri TLS per controllare le versioni del protocollo TLS?

Sì. È possibile configurare il gateway applicazione per negare TLS1.0, TLS1.1 e TLS1.2. Per impostazione predefinita, SSL 2.0 e 3.0 sono già disabilitati e non sono configurabili.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>È possibile configurare l'ordine dei pacchetti di crittografia e dei criteri?

Sì. Nel gateway applicazione è possibile [configurare i pacchetti](application-gateway-ssl-policy-overview.md)di crittografia. Per definire un criterio personalizzato, abilitare almeno uno dei seguenti pacchetti di crittografia. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Il gateway applicazione usa SHA256 per la gestione back-end.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Quanti certificati TLS/SSL supportano il gateway applicazione?

Il gateway applicazione supporta fino a 100 certificati TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quanti certificati di autenticazione per la ricrittografia back-end supporta il gateway applicazione?

Il gateway applicazione supporta fino a 100 certificati di autenticazione.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Il gateway applicazione si integra in modo nativo con l'insieme di chiavi di Azure?

Sì, lo SKU del gateway applicazione v2 supporta l'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere [Terminazione TLS con certificati dell'insieme](key-vault-certs.md)di credenziali delle chiavi .

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Come si configurano i listener HTTPS per i siti .com e .net? 

Per il routing basato su più domini (basato su host), è possibile creare listener multisito, configurare listener che utilizzano HTTPS come protocollo e associare i listener alle regole di routing. Per ulteriori informazioni, vedere [Hosting di più siti tramite Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Posso usare caratteri speciali nella mia password del file .pfx?

No, utilizzare solo caratteri alfanumerici nella password del file con estensione pfx.

## <a name="configuration---web-application-firewall-waf"></a>Configurazione - Web Application Firewall (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Lo SKU WAF offre tutte le funzionalità disponibili nello SKU Standard?

Sì. WAF supporta tutte le funzionalità dello SKU Standard.

### <a name="how-do-i-monitor-waf"></a>Come si monitora il Web application firewall?

Monitorare WAF tramite la registrazione diagnostica. Per ulteriori informazioni, vedere [Registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>La modalità di rilevamento blocca il traffico?

No. La modalità di rilevamento registra solo il traffico che attiva una regola WAF.

### <a name="can-i-customize-waf-rules"></a>È possibile personalizzare le regole del Web application firewall (WAF)?

Sì. Per ulteriori informazioni, consultate Personalizzare le regole e le [regole WAF.](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available-for-waf"></a>Quali regole sono attualmente disponibili per WAF?

WAF supporta attualmente CRS [2.2.9](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp229), [3.0](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp30)e [3.1](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md#owasp31). Queste regole forniscono la sicurezza di base contro la maggior parte delle principali 10 vulnerabilità che Open Web Application Security Project (OWASP) identifica: 

* Protezione dagli attacchi SQL injection
* Protezione da cross-site scripting
* Protezione contro gli attacchi web comuni come l'iniezione di comandi, il contrabbando di richieste HTTP, la suddivisione delle risposte HTTP e l'attacco di inclusione remota dei file
* Protezione dalle violazioni del protocollo HTTP
* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header
* Prevenzione contro robot, crawler e scanner
* Rilevamento di errori di configurazione comuni delle applicazioni, ovvero Apache, IIS e così via,

Per ulteriori informazioni, vedere [OWASP top-10 vulnerabilities](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>WAF supporta la protezione DDoS?

Sì. È possibile abilitare la protezione DDoS nella rete virtuale in cui viene distribuito il gateway applicazione. Questa impostazione garantisce che il servizio Protezione DDoS di Azure protegga anche l'IP virtuale (VIP) del gateway applicazione.

## <a name="configuration---ingress-controller-for-aks"></a>Configurazione - controller in ingresso per AKSConfiguration - ingress controller for AKS

### <a name="what-is-an-ingress-controller"></a>Che cos'è un controller di ingresso?

Kubernetes consente `deployment` la `service` creazione di e risorsa per esporre un gruppo di baccelli internamente nel cluster. Per esporre lo stesso servizio [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) esternamente, viene definita una risorsa che fornisce il bilanciamento del carico, la terminazione TLS e l'hosting virtuale basato sui nomi.
Per soddisfare `Ingress` questa risorsa, è necessario un controller di `Ingress` ingresso che rimane in ascolto di eventuali modifiche alle risorse e configura i criteri di bilanciamento del carico.

Il controller di ingresso del gateway applicazione consente di usare il [gateway applicazione](https://azure.microsoft.com/services/application-gateway/) di Azure come ingresso per un servizio [Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/) noto anche come cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Una singola istanza del controller in ingresso può gestire più gateway applicazione?

Attualmente, un'istanza del controller in ingresso può essere associata a un solo gateway applicazione.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quali tipi di log vengono forniti dal gateway applicazione?

Il gateway applicazione fornisce tre log:Application Gateway provides three logs: 

* **ApplicationGatewayAccessLog:** il log di accesso contiene ogni richiesta inviata al front-end del gateway applicazione. I dati includono l'indirizzo IP del chiamante, l'URL richiesto, la latenza di risposta, il codice restituito e i byte in uscita e in uscita. Contiene un record per ogni gateway applicazione.
* **ApplicationGatewayPerformanceLog:** il log delle prestazioni acquisisce le informazioni sulle prestazioni per ogni gateway applicazione. Le informazioni includono la velocità effettiva in byte, le richieste totali servite, il numero di richieste non riuscite e il numero di istanze back-end integro e non integro.
* **ApplicationGatewayFirewallLog:** per i gateway applicazione configurati con WAF, il registro del firewall contiene le richieste registrate tramite la modalità di rilevamento o la modalità di prevenzione.

Tutti i registri vengono raccolti ogni 60 secondi. Per ulteriori informazioni, vedere [Integrità back-end, log di diagnostica e metriche per](application-gateway-diagnostics.md)il gateway applicazione .

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Come è possibile sapere se i membri del pool back-end sono integri?

Verificare l'integrità utilizzando `Get-AzApplicationGatewayBackendHealth` il cmdlet PowerShell o il portale. Per ulteriori informazioni, vedere [Diagnostica gateway applicazione](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Quali sono i criteri di conservazione per i log di diagnostica?

I log di diagnostica vengono trasportati all'account di archiviazione del cliente. I clienti possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o ai log di Monitoraggio di Azure.Diagnostic logs can also be sent to an event hub or Azure Monitor logs. Per ulteriori informazioni, vedere [Diagnostica gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Come si ottengono i log di controllo per il gateway applicazione?

Nel portale, nel pannello menu di un gateway applicazione, selezionare **Log attività** per accedere al log di controllo. 

### <a name="can-i-set-alerts-with-application-gateway"></a>È possibile impostare avvisi con il gateway applicazione?

Sì. Nel gateway applicazione gli avvisi vengono configurati nelle metriche. Per ulteriori informazioni, vedere [Metriche del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Come si analizzano le statistiche sul traffico per il gateway applicazione?

È possibile visualizzare e analizzare i log di accesso in diversi modi. Usare i log di Monitoraggio di Azure, Excel, Power BI e così via.

È anche possibile usare un modello di Resource Manager che installa ed esegue l'analizzatore di log [GoAccess](https://goaccess.io/) popolare per i log di accesso al gateway dell'applicazione. GoAccess fornisce preziose statistiche sul traffico HTTP come visitatori unici, file richiesti, host, sistemi operativi, browser e codici di stato HTTP. Per ulteriori informazioni, in GitHub vedere il [file Readme nella cartella dei modelli di Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Che cosa potrebbe causare l'integrità del back-end per restituire uno stato sconosciuto?

In genere, viene visualizzato uno stato sconosciuto quando l'accesso al back-end è bloccato da un gruppo di sicurezza di rete (NSG), DNS personalizzato o routing definito dall'utente (UDR) nella subnet del gateway applicazione. Per ulteriori informazioni, vedere [Integrità back-end, registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Esiste un caso in cui i log di flusso del gruppo di sicurezza di rete non mostreranno il traffico consentito?

Sì. Se la configurazione corrisponde allo scenario seguente, non verrà visualizzato il traffico consentito nei log di flusso del gruppo di sicurezza di rete:If your configuration matches following scenario, you won't see allowed traffic in your NSG flow logs:
- Il gateway applicazione è stato distribuito v2
- Si dispone di un gruppo di sicurezza di rete nella subnet del gateway applicazione
- Sono stati abilitati i registri di flusso del gruppo di sicurezza di rete su tale gruppo di sicurezza di rete

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Come si usa il gateway applicazione V2 con solo l'indirizzo IP front-end privato?

Il gateway applicazione V2 attualmente non supporta solo la modalità IP privata. Supporta le seguenti combinazioni
* IP privato e IP pubblico
* Solo IP pubblico

Ma se si desidera utilizzare il gateway applicazione V2 con solo IP privato, è possibile seguire la procedura seguente:
1. Creare un gateway applicazione con indirizzo IP front-end pubblico e privatoCreate an Application Gateway with sia public and private frontend IP address
2. Non creare listener per l'indirizzo IP del front-end pubblico. Il gateway applicazione non ascolterà alcun traffico sull'indirizzo IP pubblico se non vengono creati listener.
3. Creare e collegare un gruppo di sicurezza di rete per la subnet del gateway applicazione con la configurazione seguente nell'ordine di priorità:Create and attach a [Network Security Group](https://docs.microsoft.com/azure/virtual-network/security-overview) for the Application Gateway subnet with the following configuration in the order of priority:
    
    a. Consenti traffico dall'origine come tag del servizio **GatewayManager** e Destinazione come porta **qualsiasi** e destinazione come **65200-65535**. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) dall'autenticazione del certificato. Le entità esterne, inclusi gli amministratori utente gateway, non possono avviare modifiche su tali endpoint senza certificati appropriati sul posto
    
    b. Consenti traffico dall'origine come tag di servizio AzureLoadBalancer e porta di destinazione come **QualsiasiAllow** traffic from Source as **AzureLoadBalancer** service tag and destination port as Any
    
    c. Negare tutto il traffico in ingresso dall'origine come tag del servizio **Internet** e dalla porta di destinazione come **Qualsiasi**. Assegnare a questa regola la *priorità minima* nelle regole in ingresso
    
    d. Mantenere le regole predefinite, ad esempio consentire VirtualNetwork in ingresso in modo che l'accesso all'indirizzo IP privato non sia bloccato
    
    e. La connettività Internet in uscita non può essere bloccata. In caso contrario, si dovranno affrontare problemi con la registrazione, metriche e così via.

Configurazione del gruppo di sicurezza ![di rete di esempio per l'accesso solo IP privato: Configurazione del gruppo di sicurezza di rete del gateway applicazione V2 solo per l'accesso IP privatoSample NSG configuration for private IP only access: Application Gateway V2 NSG Configuration for private IP access only](./media/application-gateway-faq/appgw-privip-nsg.png)

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Il cookie di affinità del gateway applicazione supporta l'attributo SameSite?
Sì, l'aggiornamento [Chromium browser](https://www.chromium.org/Home) [v80](https://chromiumdash.appspot.com/schedule) ha introdotto un mandato sui cookie HTTP senza l'attributo SameSite da trattare come SameSite-Lax. Ciò significa che il cookie di affinità del gateway applicazione non verrà inviato dal browser in un contesto di terze parti. Per supportare questo scenario, il gateway applicazione inserisce un altro cookie denominato *ApplicationGatewayAffinityCORS* oltre al cookie *ApplicationGatewayAffinity* esistente.  Questi cookie sono simili, ma il cookie *ApplicationGatewayAffinityCORS* ha altri due attributi aggiunti: *SameSite- None; Sicuro*. Questi attributi mantengono sessioni permanenti anche per le richieste tra origini. Vedere la [sezione affinità basata su cookie](configuration-overview.md#cookie-based-affinity) per ulteriori informazioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione, vedere [Che cos'è il gateway applicazione](overview.md)di Azure? .
