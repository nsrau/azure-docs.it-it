---
title: Domande frequenti sul Gateway applicazione di Azure
description: Trova le risposte alle domande frequenti sul Gateway applicazione di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 25779cd62f70df41a7734d2f8097504923dcb3a3
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430878"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Domande frequenti sul Gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Di seguito sono comuni alle domande frequenti sul Gateway applicazione di Azure.

## <a name="general"></a>Generale

### <a name="what-is-application-gateway"></a>Informazioni sul servizio Gateway applicazione

Gateway applicazione di Azure fornisce un application delivery controller (ADC) come servizio. Offre varie funzionalità livello 7 con bilanciamento del carico per le applicazioni. Questo servizio è a disponibilità elevata, scalabili e completamente gestito da Azure.

### <a name="what-features-does-application-gateway-support"></a>Quali funzionalità supporta il gateway applicazione?

Gateway applicazione supporta la scalabilità automatica, offload SSL e SSL end-to-end, un web application firewall (WAF), l'affinità di sessione basata su cookie, URL routing basato sul percorso, hosting multisito e altre funzionalità. Per un elenco completo delle funzionalità supportate, vedere [Cos'è il gateway applicazione di Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Come il Gateway applicazione e Azure Load Balancer è diversa?

Il Gateway applicazione è un bilanciamento del carico di livello 7, ovvero che funziona solo con il traffico web (HTTP, HTTPS, WebSocket e HTTP/2). Supporta funzionalità come terminazione SSL, affinità di sessione basata su cookie e round robin per il bilanciamento del carico di traffico. Il carico del servizio di bilanciamento bilancia il carico del traffico al livello 4 (TCP o UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quali protocolli supporta il gateway applicazione?

Il gateway applicazione supporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>In che modo il gateway applicazione supporta HTTP/2?

Visualizzare [supporto HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quali risorse sono supportate come parte di un pool di back-end?

Visualizzare [supportato risorse back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>In quali aree è disponibile il Gateway applicazione?

Il gateway applicazione è disponibile in tutte le aree di Azure globale. È anche disponibile in [Azure Cina 21Vianet](https://www.azure.cn/) e [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>È dedicato per la sottoscrizione di questa distribuzione, o è condivisa tra clienti?

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Il Gateway applicazione supporta il reindirizzamento HTTP-a-HTTPS?

Il reindirizzamento è supportato. Visualizzare [Panoramica del reindirizzamento nel Gateway applicazione](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In quale ordine vengono elaborati i listener?

Vedere le [ordine di elaborazione listener](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Dove trovare l'indirizzo IP del Gateway applicazione e DNS?

Se si usa un indirizzo IP pubblico come endpoint, è possibile trovare le informazioni di IP e DNS nella risorsa indirizzo IP pubblico. Oppure individuarlo e selezionarlo nel portale, nella pagina di panoramica per il gateway applicazione. Se si usa gli indirizzi IP interni, trovare le informazioni nella pagina di panoramica.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quali sono le impostazioni del timeout di Keep-Alive e timeout di inattività TCP?

 Nello SKU del Gateway applicazione v1, il timeout di Keep-Alive è 120 secondi. Il timeout di Keep-Alive per lo SKU v2 è 75 secondi. Il timeout di inattività TCP è un'impostazione predefinita 4 minuti sul front-end IP virtuale (VIP) del Gateway applicazione.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Il nome di indirizzo IP o DNS cambia nel corso della durata del gateway applicazione?

L'indirizzo VIP può cambiare se si arresta e avvia il gateway applicazione. Ma il nome DNS associato al gateway applicazione non cambia nel corso della durata del gateway. Poiché il nome DNS non viene modificati, è necessario utilizzare un alias CNAME e puntarlo all'indirizzo DNS del gateway applicazione.

### <a name="does-application-gateway-support-static-ip"></a>Il gateway applicazione supporta l'IP statico?

Sì, lo SKU di Gateway applicazione v2 supporta indirizzi IP pubblici statici. Lo SKU versione 1 supporta indirizzi IP interni statici.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?

Un gateway applicazione supporta un solo indirizzo IP pubblico.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quali dimensioni deve avere la subnet per il gateway applicazione?

Visualizzare [considerazioni sulle dimensioni di subnet di Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>È possibile distribuire più di una risorsa di gateway applicazione a una singola subnet?

Sì. Oltre a più istanze di una determinata distribuzione di Gateway applicazione, è possibile eseguire il provisioning di un'altra risorsa Gateway applicazione univoca a una subnet esistente che contiene un'altra risorsa Gateway applicazione.

Una singola subnet non può supportare sia Standard_v2 e il Gateway applicazione Standard tra loro.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Il gateway applicazione supporta le intestazioni x-forwarded-for?

Sì. Visualizzare [le modifiche a una richiesta](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo occorre per distribuire un gateway applicazione? Mentre è in corso l'aggiornamento, il gateway applicazione funziona correttamente?

Nuove distribuzioni di SKU versione 1 del gateway applicazione possono richiedere fino a 20 minuti per effettuare il provisioning. Modifiche alle dimensioni dell'istanza o il conteggio non sono problematici e il gateway rimane attivo durante questo periodo.

Le distribuzioni che usano lo SKU di versione 2 possono richiedere fino a 6 minuti per effettuare il provisioning.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>È possibile usare Exchange Server come un back-end con il Gateway applicazione?

No. Il Gateway applicazione non supporta i protocolli di messaggio di posta elettronica quali SMTP, IMAP e POP3. 

## <a name="performance"></a>Prestazioni

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?

Lo SKU di versione 1 il Gateway applicazione supporta scenari a disponibilità elevata quando si sono distribuita due o più istanze. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per garantire che le istanze non tutti hanno esito negativo allo stesso tempo. Lo SKU versione 1 supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

Lo SKU V2 garantisce automaticamente che le nuove istanze vengono distribuite tra domini di errore e domini di aggiornamento. Se si sceglie di ridondanza di zona, le istanze più recenti vengono anche distribuite tra le zone di disponibilità per offrire resilienza di zona errore.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Come posso ottenere uno scenario di ripristino di emergenza tra Data Center usando il Gateway applicazione?

Usare Gestione traffico per distribuire il traffico tra più gateway applicazione in diversi Data Center.

### <a name="does-application-gateway-support-autoscaling"></a>Il Gateway applicazione supporta la scalabilità automatica?

Sì, lo SKU versione 2 del gateway applicazione supporta la scalabilità automatica. Per altre informazioni, vedere [la scalabilità automatica e il Gateway applicazione ridondanza della zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Esegue manuale aumento o riduzione delle prestazioni a causa dei tempi di inattività?

No. Le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

### <a name="does-application-gateway-support-connection-draining"></a>Il gateway applicazione supporta l'esaurimento delle connessioni?

Sì. È possibile impostare la connessione di svuotamento per modificare i membri all'interno di un pool di back-end senza interruzioni. Questa configurazione consente di continuare ad inviare le connessioni esistenti verso la destinazione precedente fino a tale connessione viene chiusa o della scadenza del timeout configurabile. Esaurimento delle connessioni in attesa di solo connessioni in corso corrente alla fine. Il Gateway applicazione non è compatibile con l'applicazione dello stato della sessione.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?

Sì. Azure distribuisce le istanze nei domini di errore e di aggiornamento per garantire che le istanze non abbiano esito negativo, tutto nello stesso momento. Il gateway applicazione supporta la scalabilità con l'aggiunta di più istanze dello stesso gateway per la condivisione del carico.

## <a name="configuration"></a>Configurazione

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Il gateway applicazione viene sempre distribuito in una rete virtuale?

Sì. Il Gateway applicazione viene sempre distribuito in una subnet della rete virtuale. Questa subnet può contenere solo i gateway applicazione. Per altre informazioni, vedere [requisiti di rete e subnet virtuali](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Il Gateway applicazione può comunicare con istanze all'esterno delle rete virtuale o di fuori di relativa sottoscrizione?

Purché si disponga di connettività tramite indirizzo IP, il Gateway applicazione può comunicare con istanze all'esterno della rete virtuale che si trova in. Il Gateway applicazione può anche comunicare con istanze all'esterno della sottoscrizione in che si trova. Se si prevede di usare indirizzi IP interni come membri del pool back-end, usare [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) oppure [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>È possibile distribuire altri elementi nella subnet del gateway applicazione?

No. Ma è possibile distribuire altri gateway applicazione nella subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?

Visualizzare [gruppi di sicurezza nella subnet del Gateway applicazione di rete](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>La subnet del gateway applicazione supporta route definite dall'utente?

Visualizzare [route definite dall'utente supportate nella subnet del Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quali sono i limiti del gateway applicazione? È possibile aumentare questi limiti?

Visualizzare [limiti del Gateway applicazione](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>È possibile usare contemporaneamente il Gateway applicazione per il traffico interno ed esterno?

Sì. Il Gateway applicazione supporta un indirizzo IP interno e un indirizzo IP esterno per il gateway applicazione.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Il Gateway applicazione supporta il peering di rete virtuale?

Sì. Peering reti virtuali consente il traffico di bilanciamento del carico nelle altre reti virtuali.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>È possibile comunicare con server locali quando sono connessi da tunnel VPN o ExpressRoute?

Sì, se il traffico è consentito.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Un pool di back-end può essere usato molte applicazioni su porte diverse?

Architettura di Microservizi è supportata. Per il probe su porte diverse, è necessario configurare più impostazioni HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>I probe personalizzati supportano caratteri jolly o regex nei dati di risposta?

No. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Come vengono elaborate le regole di routine nel Gateway applicazione?

Visualizzare [ordine di elaborazione delle regole](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Per i probe personalizzati, cosa il campo Host indica?

Il campo Host specifica il nome di inviare il probe dopo avere configurato multisito nel Gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della macchina virtuale. Il formato è \<protocol\>://\<host\>:\<porta\>\<percorso\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Si può consentire accesso al Gateway applicazione per solo alcuni indirizzi IP di origine?

Sì. Visualizzare [limitare l'accesso a specifici indirizzi IP di origine](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>È possibile usare la stessa porta per il listener sia pubblico che privato rivolte?

No.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>È disponibile materiale sussidiario disponibile per eseguire la migrazione da SKU v1 per lo SKU di versione 2?

Sì. Per informazioni dettagliate, vedere [eseguire la migrazione di Gateway applicazione di Azure e Web Application Firewall da v1 a v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Configurazione di SSL:

### <a name="what-certificates-does-application-gateway-support"></a>Quali certificati sono supportate dal Gateway applicazione?

Gateway applicazione supporta i certificati autofirmati, certificati Autorità di certificazione (CA), EV (Extended Validation) certificati e certificati con caratteri jolly.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quali pacchetti di crittografia viene Gateway applicazione supporta?

Il Gateway applicazione supporta i seguenti pacchetti di crittografia. 

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

Per informazioni su come personalizzare le opzioni SSL, vedere [le versioni dei criteri di configurazione di SSL e pacchetti di crittografia nel Gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Il Gateway applicazione supporta relative a nuove crittografie del traffico nel back-end?

Sì. Il Gateway applicazione supporta l'offload SSL ed SSL end-to-end, quale crittografare di nuovo il traffico al back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>È possibile configurare i criteri SSL per le versioni del protocollo SSL controllo?

Sì. È possibile configurare i Gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. Per impostazione predefinita, SSL 2.0 e 3.0 sono già disabilitati e non sono configurabili.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>È possibile configurare l'ordine dei pacchetti di crittografia e dei criteri?

Sì. Nel Gateway applicazione, è possibile [configurare i pacchetti di crittografia](application-gateway-ssl-policy-overview.md). Per definire un criterio personalizzato, abilitare almeno uno dei pacchetti di crittografia seguenti. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Il Gateway applicazione usa SHA256 per la gestione di back-end.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Quanti certificati SSL il Gateway applicazione supporta?

Il Gateway applicazione supporta fino a 100 certificati SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quanti certificati di autenticazione per back-end relative a nuove crittografie supporta il Gateway applicazione?

Il Gateway applicazione supporta fino a 10 certificati di autenticazione. Il valore predefinito è 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Il Gateway applicazione integra in modo nativo con Azure Key Vault?

Sì, lo SKU di Gateway applicazione v2 supporta l'insieme di credenziali delle chiavi. Per altre informazioni, vedere [terminazione SSL con certificati Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Come si configura il listener HTTPS per i siti. com e .net? 

Per più basati su dominio (host routing basato su), è possibile creare i listener multisiti, configurare i listener che usano HTTPS come protocollo e associare i listener con le regole di routing. Per altre informazioni, vedere [che ospita più siti usando il Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configurazione - firewall applicazione web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Lo SKU WAF offre tutte le funzionalità disponibili nello SKU Standard?

Sì. Web Application firewall supporta tutte le funzionalità nella SKU Standard.

### <a name="which-crs-versions-does-application-gateway-support"></a>Quali versioni CRS supporta il Gateway applicazione?

Il gateway applicazione supporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Come si monitora il Web application firewall?

Monitoraggio WAF tramite la registrazione diagnostica. Per altre informazioni, vedere [la registrazione diagnostica e metriche per il Gateway applicazione](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>La modalità di rilevamento blocca il traffico?

No. Modalità di rilevamento registra solo il traffico che attiva una regola WAF.

### <a name="can-i-customize-waf-rules"></a>È possibile personalizzare le regole del Web application firewall (WAF)?

Sì. Per altre informazioni, vedere [regole e gruppi di regole WAF personalizzare](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quali regole sono attualmente disponibili per Web Application firewall?

Web Application firewall supporta attualmente CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Queste regole forniscono protezione di base per la maggior parte dei primi 10 vulnerabilità che identifica Open Web Application Security Project (OWASP): 

* Protezione dagli attacchi SQL injection
* Protezione di scripting intersito
* Protezione dagli attacchi web comuni come inserimento di comandi, di tipo HTTP request smuggling, nella suddivisione della risposta HTTP e di attacco di tipo remote file inclusion
* Protezione dalle violazioni del protocollo HTTP
* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header
* Prevenzione contro robot, crawler e scanner
* Rilevamento di comuni errori di configurazione dell'applicazione (vale a dire, Apache, IIS e così via)

Per altre informazioni, vedere [vulnerabilità top 10 OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>Web Application firewall supporta la protezione DDoS?

Sì. È possibile abilitare protezione DDoS nella rete virtuale in cui viene distribuito il gateway applicazione. Questa impostazione garantisce che il servizio protezione DDoS di Azure protegge anche l'applicazione gateway indirizzo IP virtuale (VIP).

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>È disponibile materiale sussidiario disponibile per eseguire la migrazione da SKU v1 per lo SKU di versione 2?

Sì. Per informazioni dettagliate, vedere [eseguire la migrazione di Gateway applicazione di Azure e Web Application Firewall da v1 a v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quali tipi di log fornisce il Gateway applicazione?

Il Gateway applicazione offre tre tipi di log: 

* **ApplicationGatewayAccessLog**: Il log di accesso contiene ogni richiesta inviata al front-end di gateway applicazione. I dati includono IP del chiamante, URL richiesto, latenza della risposta, codice restituito e byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Contiene un record per ogni gateway applicazione.
* **ApplicationGatewayPerformanceLog**: Il log delle prestazioni acquisisce informazioni sulle prestazioni per ogni gateway applicazione. Le informazioni includono la velocità effettiva in byte, il numero totale di richieste, numero di richieste non riuscite e il numero di istanze di back-end integre e non integre.
* **ApplicationGatewayFirewallLog**: Per i gateway applicazione configurato con Web Application firewall, il log del firewall contiene le richieste registrate tramite la modalità di rilevamento o prevenzione.

Per altre informazioni, vedere [back-end dell'integrità, i log di diagnostica e metriche per il Gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Come è possibile sapere se i membri del pool back-end sono integri?

Verificare l'integrità tramite il cmdlet di PowerShell `Get-AzApplicationGatewayBackendHealth` o il portale. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Quali sono i criteri di conservazione per i log di diagnostica?

Flusso di log di diagnostica all'account di archiviazione del cliente. I clienti possono impostare i criteri di conservazione in base alle loro preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o log di monitoraggio di Azure. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Come si ottengono i log di controllo per il gateway applicazione?

Nel portale, nel pannello del menu di scelta di un gateway applicazione, selezionare **Log attività** per accedere ai log di controllo. 

### <a name="can-i-set-alerts-with-application-gateway"></a>È possibile impostare avvisi con il gateway applicazione?

Sì. Nel Gateway applicazione, sono configurati avvisi sulle metriche. Per altre informazioni, vedere [metriche di Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) e [ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Come si analizzano le statistiche sul traffico per il gateway applicazione?

È possibile visualizzare e analizzare i log di accesso in diversi modi. Usare i log di monitoraggio di Azure, Excel, Power BI e così via.

È anche possibile usare un modello di Resource Manager che installa ed esegue il famoso [GoAccess](https://goaccess.io/) analyzer per il Gateway applicazione di accedere ai log di log. GoAccess fornisce utili statistiche sul traffico HTTP, ad esempio visitatori univoci, i file richiesti, gli host, sistemi operativi, browser e codici di stato HTTP. Per altre informazioni, in GitHub, vedere la [file Leggimi nella cartella del modello di Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Ciò che potrebbe causare l'integrità back-end restituire uno stato sconosciuto?

In genere, viene visualizzato uno stato sconosciuto quando l'accesso al back-end è bloccato da un gruppo di sicurezza di rete (NSG), DNS personalizzato o routing definito dall'utente (UDR) nella subnet del gateway applicazione. Per altre informazioni, vedere [back-end dell'integrità, la registrazione diagnostica e metriche per il Gateway applicazione](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul Gateway applicazione, vedere [qual è il Gateway applicazione di Azure?](overview.md).
