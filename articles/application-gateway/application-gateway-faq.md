---
title: Domande frequenti sul gateway applicazione di Azure
description: Questa pagina offre risposte alle domande frequenti sul gateway applicazione di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: ae55f2abf9815174e7258c2ace949078794c380d
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286194"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Domande frequenti sul gateway applicazione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Generale

### <a name="what-is-application-gateway"></a>Informazioni sul servizio Gateway applicazione

Il gateway applicazione di Azure è un servizio di controller per la distribuzione di applicazioni (ADC) con numerose funzionalità di bilanciamento del carico di livello 7. Offre un servizio altamente disponibile e scalabile completamente gestito da Azure.

### <a name="what-features-does-application-gateway-support"></a>Quali funzionalità supporta il gateway applicazione?

Il gateway applicazione supporta scalabilità automatica, offload SSL e SSL end-to-end, Web application firewall, affinità della sessione basata su cookie, routing basato su percorso URL, hosting multisito e altro. Per un elenco completo delle funzionalità supportate, vedere [Cos'è il gateway applicazione di Azure?](application-gateway-introduction.md)

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Qual è la differenza tra gateway applicazione e Azure Load Balancer?

Il Gateway applicazione è un bilanciamento del carico di livello 7, quindi che usa solo il traffico web (HTTP/HTTPS/WebSocket/HTTP/2). Supporta funzionalità come terminazione SSL, affinità di sessione basata su cookie e round robin per il bilanciamento del carico del traffico. Load Balancer esegue il bilanciamento del carico del traffico al livello 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quali protocolli supporta il gateway applicazione?

Il gateway applicazione supporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>In che modo il gateway applicazione supporta HTTP/2?

Visualizzare [supporto HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) per scoprire se il gateway applicazione supporta il protocollo HTTP/2.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quali risorse sono supportate oggi nell'ambito del pool back-end?

Visualizzare [supportato risorse back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) per informazioni su quali risorse sono supportate dal gateway applicazione.

### <a name="what-regions-is-the-service-available-in"></a>In quale aree è disponibile il servizio?

Il gateway applicazione è disponibile in tutte le aree di Azure globale. È anche disponibile in [Azure Cina 21Vianet](https://www.azure.cn/) e [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/)

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Si tratta di una distribuzione dedicata per la sottoscrizione o è condivisa tra clienti?

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

### <a name="is-http-https-redirection-supported"></a>È supportato il reindirizzamento HTTP->HTTPS?

Il reindirizzamento è supportato. Per altre informazioni, vedere la [Panoramica del reindirizzamento nel gateway applicazione](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In quale ordine vengono elaborati i listener?

I listener vengono elaborati nell'ordine in cui sono visualizzati. Per tale motivo, se un listener di base corrisponde a una richiesta in ingresso, questa viene elaborata per prima.  I listener multisito devono essere configurati prima di un listener di base per garantire che il traffico sia indirizzato al back-end corretto.

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Dove è possibile trovare IP e DNS del gateway applicazione?

Quando si usa un indirizzo IP pubblico come endpoint, queste informazioni sono reperibili nella risorsa dell'indirizzo IP pubblico o nella pagina Panoramica del gateway applicazione nel portale. Per gli indirizzi IP interni, queste informazioni si trovano nella pagina Panoramica.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>Che cos'è il timeout di Keep-Alive e impostazione di timeout di inattività TCP nel Gateway applicazione?

Timeout Keep-Alive sullo SKU v1 è 120 sec. timeout Keep-Alive sullo SKU v2 è timeout di inattività TCP di 75 secondi. valore predefinito di 4-min nel front-end di indirizzo VIP del Gateway applicazione.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>L'IP o il nome DNS cambia durante il ciclo di vita del gateway applicazione?

L'indirizzo VIP può cambiare se il gateway applicazione viene arrestato e avviato. Il nome DNS associato al gateway applicazione non cambia durante il ciclo di vita del gateway. Per questo motivo è consigliabile usare un alias CNAME che punti all'indirizzo DNS del gateway applicazione.

### <a name="does-application-gateway-support-static-ip"></a>Il gateway applicazione supporta l'IP statico?

Sì, lo SKU versione 2 del gateway applicazione supporta gli indirizzi IP pubblici statici. Lo SKU versione 1 supporta indirizzi IP interni statici.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?

Un gateway applicazione supporta un solo indirizzo IP pubblico.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quali dimensioni deve avere la subnet per il gateway applicazione?

Il gateway applicazione utilizza un indirizzo IP privato per ogni istanza, oltre a un altro indirizzo IP privato in presenza di una configurazione degli indirizzi IP front-end privati. Inoltre, Azure riserva i primi quattro e l'ultimo indirizzo IP in ogni subnet per uso interno.
Se ad esempio il gateway applicazione è impostato su tre istanze e nessun IP front-end privato, le dimensioni necessarie per la subnet sono pari a /29 o superiori. In questo caso, il gateway applicazione usa tre indirizzi IP. Se si hanno tre istanze e un indirizzo IP per la configurazione degli indirizzi IP front-end privati, le dimensioni della subnet devono essere pari a /28 o superiori, perché sono necessari quattro indirizzi IP.

Come procedura consigliata, usare almeno/28 le dimensioni della subnet. In questo modo si 11 indirizzi utilizzabili. Se il carico dell'applicazione richiede più di 10 istanze, è necessario considerare/27 o/26 le dimensioni della subnet.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>D: È possibile distribuire più di una risorsa di gateway applicazione a una singola subnet?

Sì, oltre ad avere più istanze di una determinata distribuzione di Gateway applicazione, è possibile effettuare il provisioning di un'altra risorsa Gateway applicazione univoca a una subnet esistente che contiene un'altra risorsa Gateway applicazione.

La combinazione dei gateway applicazione Standard_v2 e Standard nella stessa subnet non è supportata. Inoltre, se è abilitata la scalabilità automatica, una subnet può avere un solo gateway applicazione.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Il gateway applicazione supporta le intestazioni x-forwarded-for?

Sì. Visualizzare [modifiche per richiedere](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) per apprendere le intestazioni x-forwarded-for supportate dal Gateway applicazione.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Quanto tempo occorre per distribuire un gateway applicazione? Il gateway applicazione funziona durante l'aggiornamento?

Nuove distribuzioni di SKU versione 1 del gateway applicazione possono richiedere fino a 20 minuti per effettuare il provisioning. Le modifiche all'istanza di conteggio/dimensioni non causano problemi e il gateway rimane attivo durante questo periodo di tempo.

Le distribuzioni di SKU versione 2 possono richiedere circa cinque e sei minuti per effettuare il provisioning.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Exchange server può essere usato come back-end con il Gateway applicazione?

No, il Gateway applicazione non supporta i protocolli di messaggio di posta elettronica quali SMTP, IMAP e POP3. 

## <a name="performance"></a>Prestazioni

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?

Lo SKU versione 1 del gateway applicazione supporta scenari di disponibilità elevata se sono presenti due o più istanze distribuite. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Lo SKU versione 1 supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

Lo SKU V2 garantisce automaticamente che le nuove istanze vengono distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza della zona, le istanze più recenti vengono anche distribuite tra le zone di disponibilità per offrire resilienza dagli errori delle zone.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Come si ottiene uno scenario di ripristino di emergenza tra data center con un gateway applicazione?

I clienti possono usare Gestione traffico per distribuire il traffico tra più gateway applicazione in data center diversi.

### <a name="is-autoscaling-supported"></a>La scalabilità automatica è supportata?

Sì, lo SKU versione 2 del gateway applicazione supporta la scalabilità automatica. Per altre informazioni, vedere [Gateway applicazione con scalabilità automatica e ridondanza della zona (anteprima pubblica)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Il ridimensionamento manuale causa tempi di inattività?

Non si verificano tempi di inattività. Le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

### <a name="does-application-gateway-support-connection-draining"></a>Il gateway applicazione supporta l'esaurimento delle connessioni?

Sì. È possibile configurare l'esaurimento delle connessioni per modificare i membri all'interno di un pool back-end senza interruzioni. In questo modo le connessioni esistenti continuano a essere inviate alla relativa destinazione precedente fino a quando tale connessione viene chiusa o un timeout configurabile scade. L'esaurimento delle connessioni attende solo il completamento delle connessioni correnti in corso. Gateway applicazione non rileva lo stato della sessione applicazione.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?

Sì, Azure distribuisce le istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Il gateway applicazione supporta la scalabilità con l'aggiunta di più istanze dello stesso gateway per la condivisione del carico.

## <a name="configuration"></a>Configurazione

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Il gateway applicazione viene sempre distribuito in una rete virtuale?

Sì, il gateway applicazione viene sempre distribuito in una subnet di rete virtuale. Questa subnet può contenere solo gateway applicazione. Visualizzare [requisiti di rete e subnet virtuali](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) per comprendere le considerazioni di subnet per il Gateway applicazione.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>Il Gateway applicazione può comunicare con le istanze all'esterno della rete virtuale in che è o all'esterno della sottoscrizione in che si trova?

Il Gateway applicazione può comunicare con istanze all'esterno della rete virtuale a cui si trova o all'esterno della sottoscrizione che è, purché vi sia connettività IP. Se si prevede di usare indirizzi IP interni come membri del pool back-end, è necessario il [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) o il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>È possibile distribuire altri elementi nella subnet del gateway applicazione?

No, ma è possibile distribuire altri gateway applicazione nella subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?

I gruppi di sicurezza di rete (NSG) sono supportati nella subnet del gateway applicazione con le restrizioni seguenti:

* Devono essere inserite eccezioni per il traffico in ingresso sulle porte 65503-65534 per lo SKU versione 1 del gateway applicazione e sulle porte 65200-65535 per lo SKU versione 2. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.

* La connettività Internet in uscita non può essere bloccata. Le regole in uscita predefinite in sicurezza di rete già consentono la connettività internet. Si consiglia di non rimuovere le regole in uscita predefinite e che non è possibile creare altre regole in uscita che nega la connettività internet in uscita.

* È necessario consentire il traffico dal tag AzureLoadBalancer.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Le route definite dall'utente sono supportate nella subnet del gateway applicazione?

Visualizzare [restrizioni di route definite dall'utente](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) per apprendere le route definite dall'utente supportate nella subnet del gateway applicazione.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quali sono i limiti del gateway applicazione? È possibile aumentare questi limiti?

Vedere [Limiti del gateway applicazione](../azure-subscription-service-limits.md#application-gateway-limits) per informazioni sui limiti.

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>È possibile usare il gateway applicazione per il traffico interno ed esterno contemporaneamente?

Sì, il gateway applicazione supporta un indirizzo IP interno e un indirizzo IP esterno.

### <a name="is-vnet-peering-supported"></a>Il peering reti virtuali è supportato?

Sì, il peering reti virtuali è supportato ed è utile per il bilanciamento del carico del traffico in altre reti virtuali.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>È possibile comunicare con server locali se connessi da tunnel VPN o ExpressRoute?

Sì, se il traffico è consentito.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>È possibile avere un pool back-end che serve molte applicazioni su porte diverse?

L'architettura di microservizi è supportata. È necessario configurare più impostazioni HTTP per il probe su porte diverse.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>I probe personalizzati supportano caratteri jolly o regex nei dati di risposta?

I probe personalizzati non supportano caratteri jolly o regex nei dati di risposta.

### <a name="how-are-rules-processed"></a>Come vengono elaborate le regole?

Le regole vengono elaborate nell'ordine in cui sono configurate. È consigliabile che le regole multisito siano configurate prima delle regole di base per ridurre le probabilità che il traffico sia indirizzato a un back-end inappropriato, in quanto la regola di base corrisponderebbe al traffico in base alla porta prima che venga valutata la regola multisito.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>Cosa indica il campo Host per i probe personalizzati?

Il campo Host specifica il nome a cui inviare il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM ed è nel formato \<protocollo\>://\<host\>:\<porta\>\<percorso\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>È possibile consentire l'accesso al gateway applicazione ad alcuni IP di origine?

Sì. Visualizzare [limitare l'accesso a specifici indirizzi IP di origine](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) per comprendere come garantire che solo nell'elenco elementi consentiti gli IP di origine possono accedere al Gateway applicazione.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>La stessa porta può essere usata per i listener pubblici e privati?

No, questa operazione non è supportata.

## <a name="configuration---ssl"></a>Configurazione di SSL:

### <a name="what-certificates-are-supported-on-application-gateway"></a>Quali certificati sono supportati dal gateway applicazione?

I certificati autofirmati, certificati di autorità di certificazione, EV certificati e i certificati con caratteri jolly sono supportati.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Quali sono i pacchetti di crittografia attualmente supportati dal gateway applicazione?

Di seguito sono elencati i pacchetti di crittografia attualmente supportati dal gateway applicazione. Per informazioni su come configurare le opzioni SSL, vedere l'articolo [Configurare i pacchetti di crittografia e le versioni dei criteri SSL nel gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Il gateway applicazione supporta anche la ripetizione della crittografia del traffico nel back-end?

Sì, il gateway applicazione supporta l'offload SSL e l'SSL end-to-end, che consente di crittografare nuovamente il traffico nel back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>È possibile configurare i criteri SSL per gestire le versioni del protocollo SSL?

Sì, è possibile configurare il gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 sono già disabilitati per impostazione predefinita e non sono configurabili.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>È possibile configurare l'ordine dei pacchetti di crittografia e dei criteri?

Sì, la [configurazione dei pacchetti di crittografia](application-gateway-ssl-policy-overview.md) è supportata. Quando si definiscono criteri personalizzati, deve essere abilitato almeno uno dei pacchetti di crittografia seguenti. Il gateway applicazione usa SHA256 per la gestione del back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Quanti certificati SSL sono supportati?

Sono supportati fino a 100 certificati SSL.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Quanti certificati di autenticazione sono supportati per la ripetizione della crittografia nel back-end?

Sono supportati fino a 10 certificati di autenticazione con un valore predefinito di 5.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Il gateway applicazione si integra con Azure Key Vault in modo nativo?

No, non è integrato con Azure Key Vault.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>Come configurare i listener HTTPS per i siti. com e .net? 

Per più basati su dominio (host routing basato su), è possibile creare i listener multisito, selezionare HTTPS come protocollo nella configurazione del listener e associare i listener con le regole di routing. Per altre informazioni, vedere [che ospita più siti con un Gateway applicazione](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configurazione di Web Application Firewall (WAF):

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>Lo SKU del WAF offre tutte le funzionalità disponibili con lo SKU Standard?

Sì, il WAF offre tutte le funzionalità disponibili nello SKU Standard.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Qual è la versione di CRS supportata dal gateway applicazione?

Il gateway applicazione supporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Come si monitora il Web application firewall?

Il Web application firewall viene monitorato tramite la registrazione diagnostica. Per altre informazioni sulla registrazione diagnostica, vedere [Integrità back-end, registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md)

### <a name="does-detection-mode-block-traffic"></a>La modalità di rilevamento blocca il traffico?

No, la modalità di rilevamento registra solo il traffico che ha attivato una regola del Web application firewall.

### <a name="can-i-customize-waf-rules"></a>È possibile personalizzare le regole del Web application firewall (WAF)?

Sì, le regole WAF sono personalizzabili. Per altre informazioni, vedere [Personalizzare le regole e i gruppi di regole del Web application firewall](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Quali regole sono attualmente disponibili

Il Web application firewall supporta attualmente CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), che offre la protezione di base per la maggior parte delle prime 10 vulnerabilità identificate dall'Open Web Application Security Project (OWASP) disponibili in [Prime 10 vulnerabilità OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Protezione dagli attacchi SQL injection

* Protezione dagli attacchi di scripting intersito

* Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

* Protezione dalle violazioni del protocollo HTTP

* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

* Prevenzione contro robot, crawler e scanner

* Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via)

### <a name="does-waf-also-support-ddos-prevention"></a>Il WAF supporta anche la prevenzione DDoS?

Sì. È possibile abilitare Protezione DDos nella rete virtuale in cui viene distribuito il gateway applicazione. Ciò assicura che anche l'indirizzo IP virtuale del gateway applicazione sia protetto dal servizio Protezione DDos di Azure.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Quali tipi di log sono disponibili con il gateway applicazione?

Sono disponibili tre log con il gateway applicazione. Per altre informazioni su questi log e altre funzionalità di diagnostica, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog**: il log di accesso contiene ogni richiesta inviata al front-end del gateway applicazione. I dati includono indirizzo IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza di gateway applicazione.
* **ApplicationGatewayPerformanceLog**: il log delle prestazioni acquisisce le informazioni sulle prestazioni di ogni singola istanza, come il totale delle richieste servite, la velocità effettiva in byte, il numero delle richieste non riuscite e il numero delle istanze back-end integre e non integre.
* **ApplicationGatewayFirewallLog**: il log del firewall contiene le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il Web application firewall.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Come è possibile sapere se i membri del pool back-end sono integri?

È possibile usare il cmdlet `Get-AzApplicationGatewayBackendHealth` di PowerShell o verificare l'integrità tramite il portale. Vedere la [diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Che cos'è il criterio di conservazione per i log di diagnostica?

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a log di Monitoraggio di Azure. Vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md) per altri dettagli.

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Come si ottengono i log di controllo per il gateway applicazione?

Sono disponibili log di controllo per il gateway applicazione. Nel portale, fare clic su **Log attività** nel pannello del menu di scelta di un gateway applicazione per il log di controllo di accesso. 

### <a name="can-i-set-alerts-with-application-gateway"></a>È possibile impostare avvisi con il gateway applicazione?

Sì, il gateway applicazione supporta gli avvisi. Gli avvisi vengono configurati nelle metriche. Per altre informazioni sulle metriche del gateway applicazione, vedere le [metriche del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics). Per altre informazioni sugli avvisi, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Come si analizzano le statistiche sul traffico per il gateway applicazione?

È possibile visualizzare e analizzare i log di accesso tramite una serie di meccanismi, ad esempio i log di Monitoraggio di Azure, Excel, Power BI e così via.

È stato anche pubblicato un modello di Resource Manager che installa ed esegue l'analizzatore di log [GoAccess](https://goaccess.io/), di ampia diffusione, per i log di accesso del gateway applicazione. GoAccess offre statistiche utili sul traffico HTTP, ad esempio il numero di visitatori unici, file richiesti, host, sistemi operativi, browser, codici di stato HTTP e altro ancora. Per altre informazioni, vedere il [file Readme nella cartella del modello di Resource Manager in GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>L'integrità back-end restituisce uno stato sconosciuto. Quale potrebbe essere la causa?

Nella maggior parte dei casi, l'accesso al back-end è bloccato da un gruppo di sicurezza di rete o da un DNS personalizzato. Vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md) per altre informazioni.

## <a name="next-steps"></a>Fasi successive

Per altre informazioni sul gateway applicazione, vedere [Cos'è il gateway applicazione di Azure?](overview.md)
