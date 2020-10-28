---
title: Domande frequenti sul gateway applicazione di Azure
description: Questa pagina offre le risposte alle domande frequenti sul gateway applicazione di Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 492041e39cf3e7be256bc783afc82fc756e17bf4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791546"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Domande frequenti sul gateway applicazione di Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Di seguito sono riportate le domande frequenti sul gateway applicazione di Azure.

## <a name="general"></a>Generale

### <a name="what-is-application-gateway"></a>Informazioni sul servizio Gateway applicazione

Il gateway applicazione di Azure offre un controller per la distribuzione di applicazioni (ADC, Application Delivery Controller) come servizio. Offre varie funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Questo servizio è altamente disponibile, scalabile e completamente gestito da Azure.

### <a name="what-features-does-application-gateway-support"></a>Quali funzionalità supporta il gateway applicazione?

Il gateway applicazione supporta scalabilità automatica, offload TLS e TLS end-to-end, Web application firewall, affinità della sessione basata su cookie, routing basato su percorso URL, hosting multisito e altre funzionalità. Per un elenco completo delle funzionalità supportate, vedere [Cos'è il gateway applicazione di Azure?](application-gateway-introduction.md)

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Qual è la differenza tra gateway applicazione e Azure Load Balancer?

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7 e funziona quindi solo con il traffico Web (HTTP, HTTPS, WebSocket e HTTP/2). Supporta funzionalità come terminazione TLS, affinità di sessione basata su cookie e round robin per il bilanciamento del carico del traffico. Load Balancer esegue il bilanciamento del carico del traffico al livello 4 (TCP o UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quali protocolli supporta il gateway applicazione?

Il gateway applicazione supporta HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>In che modo il gateway applicazione supporta HTTP/2?

Vedere [Supporto HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quali risorse sono supportate come parte di un pool back-end?

Vedere [Risorse back-end supportate](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>In quali aree è disponibile il gateway applicazione?

Il gateway applicazione V1 (standard e WAF) è disponibile in tutte le aree di Azure globale. È anche disponibile in [Azure Cina (21Vianet)](https://www.azure.cn/) e [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/).

Per la disponibilità del gateway applicazione V2 (Standard_v2 e WAF_v2), vedere [aree supportate per il gateway applicazione V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#supported-regions)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Si tratta di una distribuzione dedicata per la sottoscrizione o è condivisa tra clienti?

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Il gateway applicazione supporta il reindirizzamento da HTTP a HTTPS?

Il reindirizzamento è supportato. Vedere [Panoramica del reindirizzamento nel gateway applicazione](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>In quale ordine vengono elaborati i listener?

Vedere l'[ordine di elaborazione del listener](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Dove è possibile trovare IP e DNS del gateway applicazione?

Se si usa un indirizzo IP pubblico come endpoint, le informazioni sull'indirizzo IP e sul DNS sono disponibili nella risorsa indirizzo IP pubblico. In alternativa, è disponibile nel portale nella pagina di panoramica per il gateway applicazione. Se si usano indirizzi IP interni, le informazioni sono disponibili nella pagina di panoramica.

Per lo SKU v2, aprire la risorsa IP pubblico e selezionare **Configurazione** . Il campo **Etichetta del nome DNS (facoltativa)** è disponibile per configurare il nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quali sono le impostazioni per il timeout keep-alive e il timeout di inattività TCP?

Il *timeout keep-alive* controlla per quanto tempo il gateway applicazione deve attendere che un client invii un'altra richiesta HTTP su una connessione permanente prima di riutilizzarla o chiuderla. Il *timeout di inattività TCP* controlla per quanto tempo una connessione TCP viene mantenuta aperta in caso di assenza di attività. 

Il *timeout keep-alive* nello SKU v1 del gateway applicazione è 120 secondi e nello SKU v2 è 75 secondi. Il *timeout di inattività TCP* è un valore predefinito di 4 minuti nell'indirizzo IP virtuale front-end (VIP) dello SKU v1 e v2 del gateway applicazione. È possibile configurare il valore di timeout di inattività TCP nei gateway applicazione V1 e V2 in un punto qualsiasi compreso tra 4 minuti e 30 minuti. Per i gateway applicazione V1 e V2 è necessario passare all'indirizzo IP pubblico del gateway applicazione e modificare il timeout di inattività TCP nel pannello "configurazione" dell'indirizzo IP pubblico nel portale. È possibile impostare il valore di timeout di inattività TCP dell'IP pubblico tramite PowerShell eseguendo i comandi seguenti: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>L'IP o il nome DNS cambia durante il ciclo di vita del gateway applicazione?

Nello SKU v1 del gateway applicazione l'indirizzo VIP può cambiare se si arresta e si avvia il gateway applicazione. Tuttavia, il nome DNS associato al gateway applicazione non cambia durante il ciclo di vita del gateway. Poiché il nome DNS non cambia, è consigliabile usare un alias CNAME che punti all'indirizzo DNS del gateway applicazione. Nello SKU v2 del gateway applicazione, è possibile impostare l'indirizzo IP come statico in modo che l'IP e il nome DNS non cambino nel corso del ciclo di vita del gateway applicazione. 

### <a name="does-application-gateway-support-static-ip"></a>Il gateway applicazione supporta l'IP statico?

Sì, lo SKU v2 del gateway applicazione supporta gli indirizzi IP pubblici statici. Lo SKU versione 1 supporta indirizzi IP interni statici.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?

Un gateway applicazione supporta un solo indirizzo IP pubblico.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quali dimensioni deve avere la subnet per il gateway applicazione?

Vedere [Considerazioni sulle dimensioni della subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>È possibile distribuire più di una risorsa di gateway applicazione a una singola subnet?

Sì. Oltre ad avere più istanze di una determinata distribuzione del gateway applicazione, è possibile effettuare il provisioning di un'altra risorsa gateway applicazione univoca a una subnet esistente che contiene un'altra risorsa gateway applicazione.

Una singola subnet non può supportare entrambi gli SKU v2 e v1 del gateway applicazione.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Il gateway applicazione v2 supporta le route definite dall'utente (UDR)?

Sì, ma solo scenari specifici. Per altre informazioni, vedere [configurazione dell'infrastruttura del gateway applicazione](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Il gateway applicazione supporta le intestazioni x-forwarded-for?

Sì. Vedere [Modifiche a una richiesta](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo occorre per distribuire un gateway applicazione? Il gateway applicazione funziona durante l'aggiornamento?

Nuove distribuzioni di SKU versione 1 del gateway applicazione possono richiedere fino a 20 minuti per effettuare il provisioning. Le modifiche all'istanza di conteggio o dimensioni non causano problemi e il gateway rimane attivo durante questo periodo di tempo.

Per la maggior parte delle distribuzioni che usano lo SKU v2 sono necessari circa 6 minuti per il provisioning. Tuttavia, può essere necessario più tempo a seconda del tipo di distribuzione. Ad esempio, le distribuzioni in più zone di disponibilità con molte istanze possono richiedere più di 6 minuti. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>È possibile usare Exchange Server come back-end con il gateway applicazione?

No. Il gateway applicazione non supporta i protocolli di posta elettronica, come SMTP, IMAP e POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Sono disponibili linee guida per la migrazione dallo SKU v1 allo SKU v2?

Sì. Per informazioni dettagliate, vedere [Eseguire la migrazione del gateway applicazione di Azure e del web application firewall da v1 a v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Lo SKU v1 del gateway applicazione continuerà a essere supportato?

Sì. Lo SKU versione 1 del gateway applicazione continuerà a essere supportato. Tuttavia, si consiglia di passare alla versione 2 per sfruttare i vantaggi degli aggiornamenti delle funzionalità nello SKU. Per altre informazioni, vedere [Gateway applicazione con scalabilità automatica e ridondanza della zona versione 2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Il gateway applicazione v2 supporta il proxying delle richieste con l'autenticazione NTLM?

No. Il gateway applicazione v2 non supporta ancora il proxying delle richieste con l'autenticazione NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Il cookie di affinità del gateway applicazione supporta l'attributo SameSite?
Sì, l'[aggiornamento V80](https://chromiumdash.appspot.com/schedule) del [browser Chromium](https://www.chromium.org/Home) ha introdotto un mandato sui cookie HTTP senza attributo SameSite affinché vengano trattati come SameSite=Lax. Ciò significa che il cookie di affinità del gateway applicazione non verrà inviato dal browser in un contesto di terze parti. 

Per supportare questo scenario, il gateway applicazione inserisce un altro cookie denominato *ApplicationGatewayAffinityCORS* in aggiunta al cookie esistente *ApplicationGatewayAffinity* .  Questi cookie sono simili, ma al cookie *ApplicationGatewayAffinityCORS* sono stati aggiunti altri due attributi: *SameSite=None; Secure* . Questi attributi gestiscono sessioni permanenti anche per le richieste tra origini. Per altre informazioni, vedere la [sezione relativa all'affinità basata su cookie](configuration-http-settings.md#cookie-based-affinity).

## <a name="performance"></a>Prestazioni

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?

Lo SKU v1 del gateway applicazione supporta scenari di disponibilità elevata se sono state distribuite due o più istanze. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Lo SKU versione 1 supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

Lo SKU V2 garantisce automaticamente che le nuove istanze vengono distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza della zona, le istanze più recenti vengono anche distribuite tra le zone di disponibilità per offrire resilienza dagli errori delle zone.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Come si ottiene uno scenario di ripristino di emergenza tra data center che usano un gateway applicazione?

Usare Gestione traffico per distribuire il traffico tra più gateway applicazione in data center diversi.

### <a name="does-application-gateway-support-autoscaling"></a>Il gateway applicazione supporta il ridimensionamento automatico?

Sì, lo SKU versione 2 del gateway applicazione supporta la scalabilità automatica. Per altre informazioni, vedere [Gateway applicazione con scalabilità automatica e ridondanza della zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>L'aumento o la riduzione manuale o automatica causa tempo di inattività?

No. Le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

### <a name="does-application-gateway-support-connection-draining"></a>Il gateway applicazione supporta l'esaurimento delle connessioni?

Sì. È possibile configurare lo svuotamento delle connessioni per modificare i membri all'interno di un pool back-end senza interruzioni. Per altre informazioni, vedere la [sezione Svuotamento delle connessioni di Gateway applicazione](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?

Sì.

## <a name="configuration"></a>Configurazione

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Il gateway applicazione viene sempre distribuito in una rete virtuale?

Sì. Il gateway applicazione viene sempre distribuito in una subnet di rete virtuale. Questa subnet può contenere solo gateway applicazione. Per altre informazioni, vedere i [requisiti della rete virtuale e della subnet](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Il gateway applicazione può comunicare con istanze all'esterno della rete virtuale o della relativa sottoscrizione?

Fino a quando è disponibile la connettività IP, il gateway applicazione può comunicare con le istanze all'esterno della rete virtuale in cui si trova. Il gateway applicazione può anche comunicare con istanze all'esterno della sottoscrizione in cui si trova. Se si prevede di usare indirizzi IP interni come membri del pool back-end, usare il [peering di reti virtuali](../virtual-network/virtual-network-peering-overview.md) o il [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>È possibile distribuire altri elementi nella subnet del gateway applicazione?

No. Ma è possibile distribuire altri gateway applicazione nella subnet.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?

Vedere [Gruppi di sicurezza di rete nella subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-infrastructure#network-security-groups).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>La subnet del gateway applicazione supporta le route definite dall'utente?

Vedere [Route definite dall'utente supportate nella subnet del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/configuration-infrastructure#supported-user-defined-routes).

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>I criteri dell'endpoint servizio sono supportati nella subnet del gateway applicazione?

No. I [criteri dell'endpoint di servizio](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) per gli account di archiviazione non sono supportati nella subnet del gateway applicazione e la configurazione bloccherà il traffico dell'infrastruttura di Azure.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quali sono i limiti del gateway applicazione? È possibile aumentare questi limiti?

Vedere [Limiti del gateway applicazione](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>È possibile usare il gateway applicazione per il traffico interno ed esterno contemporaneamente?

Sì. Il gateway applicazione supporta un indirizzo IP interno e un indirizzo IP esterno.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Il gateway applicazione supporta il peering di reti virtuali?

Sì. Il peering di reti virtuali consente di bilanciare il carico del traffico in altre reti virtuali.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>È possibile comunicare con server locali se connessi da tunnel VPN o ExpressRoute?

Sì, se il traffico è consentito.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>È possibile avere un pool back-end che serve molte applicazioni su porte diverse?

L'architettura di microservizi è supportata. Per il probe su porte diverse è necessario configurare più impostazioni HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>I probe personalizzati supportano caratteri jolly o regex nei dati di risposta?

No. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Come vengono elaborate le regole di gestione nel gateway applicazione?

Vedere [Ordine di elaborazione delle regole](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Cosa indica il campo Host per i probe personalizzati?

Il campo Host specifica il nome a cui inviare il probe quando è stato configurato il multisito nel gateway applicazione. In caso contrario, usare '127.0.0.1'. Questo valore è diverso dal nome host della macchina virtuale. Il formato è \<protocol\> :// \<host\> : \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>È possibile consentire l'accesso del gateway applicazione solo ad alcuni indirizzi IP di origine?

Sì. Vedere [Limitare l'accesso a IP di origine specifici](https://docs.microsoft.com/azure/application-gateway/configuration-infrastructure#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>La stessa porta può essere usata per i listener pubblici e privati?

No.

### <a name="does-application-gateway-support-ipv6"></a>Il gateway applicazione supporta IPv6?

Il gateway applicazione v2 attualmente non supporta IPv6. Può essere usato in una rete virtuale dual stack usando solo IPv4, ma la subnet del gateway deve essere solo IPv4. Il gateway applicazione v1 non supporta reti virtuali dual stack. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Come usare il gateway applicazione v2 con solo indirizzi IP front-end privati?

Il gateway applicazione v2 attualmente non supporta solo la modalità IP privato. Supporta le combinazioni seguenti
* IP privato e IP pubblico
* Solo IP pubblico

Tuttavia, se si vuole usare il gateway applicazione v2 con solo IP privato, è possibile seguire questa procedura:
1. Creare un gateway applicazione con un indirizzo IP front-end pubblico e privato
2. Non creare listener per l'indirizzo IP front-end pubblico. Il gateway applicazione non sarà in ascolto di alcun traffico sull'indirizzo IP pubblico se non viene creato alcun listener.
3. Creare e associare un [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per la subnet del gateway applicazione con la configurazione seguente in ordine di priorità:
    
    a. Consentire il traffico dall'origine come tag di servizio **GatewayManager** , la destinazione come **Any** e la porta di destinazione come **65200-65535** . Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Queste porte sono protette (bloccate) tramite l'autenticazione del certificato. Le entità esterne, inclusi gli amministratori utenti del gateway, non possono avviare modifiche sugli endpoint senza certificati appropriati
    
    b. Consentire il traffico dall'origine come tag di servizio **GatewayManager** e la destinazione e la porta di destinazione come **Any**
    
    c. Negare tutto il traffico in ingresso dall'origine come tag di servizio **Internet** e la destinazione e la porta di destinazione come **Any** . Assegnare a questa regola la *priorità minima* nelle regole in ingresso
    
    d. Mantenere le regole predefinite come consentire VirtualNetwork in ingresso in modo che l'accesso all'indirizzo IP privato non venga bloccato
    
    e. La connettività Internet in uscita non può essere bloccata. In caso contrario, si verificheranno problemi con la registrazione, le metriche e così via.

Esempio di configurazione del gruppo di sicurezza di rete per l'accesso solo IP privato: ![Configurazione del gruppo di sicurezza di rete del gateway applicazione v2 solo per l'accesso IP privato](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configurazione - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Quali certificati supporta il gateway applicazione?

Il gateway applicazione supporta i certificati autofirmati, i certificati dell'autorità di certificazione (CA), i certificati di convalida estesa (EV), i certificati di più domini (SAN) e i certificati con caratteri jolly.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quali pacchetti di crittografia supporta il gateway applicazione?

Il gateway applicazione supporta i pacchetti di crittografia seguenti. 

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

Per informazioni su come personalizzare le opzioni TLS, vedere [Configurare i pacchetti di crittografia e le versioni dei criteri TLS nel gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Il gateway applicazione supporta la ripetizione della crittografia del traffico nel back-end?

Sì. Il gateway applicazione supporta l'offload TLS e il TLS end-to-end per ripetere la crittografia del traffico nel back-end.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>È possibile configurare i criteri TLS per gestire le versioni del protocollo TLS?

Sì. È possibile configurare il gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. Per impostazione predefinita, SSL 2.0 e 3.0 sono già disabilitati e non sono configurabili.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>È possibile configurare l'ordine dei pacchetti di crittografia e dei criteri?

Sì. Nel gateway applicazione è possibile [configurare i pacchetti di crittografia](application-gateway-ssl-policy-overview.md). Per definire criteri personalizzati, abilitare almeno uno dei pacchetti di crittografia seguenti. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Il gateway applicazione usa SHA256 per la gestione del back-end.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Quanti certificati TLS/SSL supporta il gateway applicazione?

Il gateway applicazione supporta fino a 100 certificati TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quanti certificati di autenticazione per la ripetizione della crittografia nel back-end supporta il gateway applicazione?

Il gateway applicazione supporta fino a 100 certificati di autenticazione.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Il gateway applicazione si integra con Azure Key Vault in modo nativo?

Sì, lo SKU versione 2 del gateway applicazione supporta Key Vault. Per altre informazioni, vedere [Terminazione TLS con certificati di Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Come configurare i listener HTTPS per i siti .com e .NET? 

Per routing basati su dominio (basati su host) multipli, è possibile creare listener multisito, configurare i listener che usano HTTPS come protocollo e associare i listener alle regole di gestione. Per altre informazioni, vedere [Hosting di più siti in un gateway applicazione](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>È possibile usare caratteri speciali nella password del file con estensione pfx?

No, usare solo caratteri alfanumerici nella password del file con estensione pfx.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>Il certificato EV viene emesso da DigiCert e il certificato intermedio è stato revocato. Ricerca per categorie rinnovare il certificato nel gateway applicazione?

I membri del browser dell'autorità di certificazione (CA) hanno pubblicato di recente report che illustrano più certificati rilasciati dai fornitori di CA usati dai clienti, da Microsoft e dalla più ampia community di tecnologie che non sono conformi agli standard del settore per le autorità di certificazione pubbliche attendibili.I report relativi alle autorità di certificazione non conformi sono disponibili qui:  

* [Bug 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bug 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

In base ai requisiti di conformità del settore, i fornitori di autorità di certificazione hanno iniziato a revocare le CA non conformi e a emettere CA conformi che richiedono la riemissione dei certificati da parte dei clienti.Microsoft collabora a stretto contatto con questi fornitori per ridurre al minimo il potenziale impatto sui servizi di Azure, tuttavia i certificati autocertificati **o i certificati utilizzati negli scenari "Bring your own certificate" (BYOC) sono ancora a rischio di essere revocati in modo imprevisto** .

Per verificare se i certificati utilizzati dall'applicazione sono stati revocati, fare riferimento all' [annuncio di DigiCert](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) e allo strumento di [rilevamento delle revoche di certificati](https://misissued.com/#revoked). Se i certificati sono stati revocati o verranno revocati, sarà necessario richiedere nuovi certificati al fornitore della CA utilizzato nelle applicazioni. Per evitare che la disponibilità dell'applicazione venga interrotta a causa di un arresto imprevisto dei certificati o per aggiornare un certificato revocato, fare riferimento al post degli aggiornamenti di Azure per i collegamenti correttivi dei vari servizi di Azure che supportano BYOC: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Per informazioni specifiche sul gateway applicazione, vedere di seguito.

Se si utilizza un certificato emesso da uno dei ICAs revocati, la disponibilità dell'applicazione potrebbe essere interrotta e, a seconda dell'applicazione, potrebbe essere visualizzata una serie di messaggi di errore, tra cui: 

1.  Certificato/certificato revocato non valido
2.  Timeout della connessione
3.  HTTP 502

Per evitare interruzioni nell'applicazione a causa di questo problema o per riemettere una CA revocata, è necessario eseguire le azioni seguenti: 

1.  Contattare il provider di certificati per informazioni su come rilasciare nuovamente i certificati
2.  Una volta rilasciata, aggiornare i certificati in applicazione Azure gateway/WAF con la [catena di attendibilità](https://docs.microsoft.com/windows/win32/seccrypto/certificate-chains) completa (foglia, intermedia, certificato radice). In base alla posizione in cui si usa il certificato, nel listener o nelle impostazioni HTTP del gateway applicazione, attenersi alla procedura seguente per aggiornare i certificati e controllare i collegamenti alla documentazione indicati per ulteriori informazioni.
3.  Aggiornare i server applicazioni back-end in modo da usare il certificato emesso nuovamente. A seconda del server back-end in uso, i passaggi per l'aggiornamento del certificato possono variare. Verificare la documentazione del fornitore.

Per aggiornare il certificato nel listener:

1.  Nella [portale di Azure](https://portal.azure.com/)aprire la risorsa del gateway applicazione
2.  Aprire le impostazioni del listener associate al certificato
3.  Fare clic su "rinnova o modifica certificato selezionato"
4.  Caricare il nuovo certificato PFX con la password e fare clic su Save (Salva)
5.  Accedere al sito Web e verificare se il sito funziona come previsto per ulteriori informazioni, vedere la documentazione [qui](https://docs.microsoft.com/azure/application-gateway/renew-certificates).

Se si fa riferimento a certificati da Azure Vault nel listener del gateway applicazione, è consigliabile attenersi alla procedura seguente per una modifica rapida:

1.  Nella [portale di Azure](https://portal.azure.com/)passare alle impostazioni dell'insieme di credenziali delle credenziali di Azure associate al gateway applicazione
2.  Aggiungere/importare il certificato rilasciato nell'archivio. Per ulteriori informazioni sulle procedure, vedere la documentazione [qui](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal) .
3.  Una volta importato il certificato, passare alle impostazioni del listener del gateway applicazione e in "scegliere un certificato da Key Vault", fare clic sull'elenco a discesa "certificato" e scegliere il certificato aggiunto di recente.
4.  Fare clic su Salva per ulteriori informazioni sulla terminazione TLS nel gateway applicazione con Key Vault certificati, vedere la documentazione [qui](https://docs.microsoft.com/azure/application-gateway/key-vault-certs).


Per aggiornare il certificato nelle impostazioni HTTP:

Se si usa lo SKU V1 del servizio gateway applicazione/WAF, è necessario caricare il nuovo certificato come certificato di autenticazione back-end.
1.  Nella [portale di Azure](https://portal.azure.com/)aprire la risorsa del gateway applicazione
2.  Aprire le impostazioni HTTP associate al certificato
3.  Fare clic su "Aggiungi certificato" e caricare il certificato riemesso e fare clic su Salva.
4.  È possibile rimuovere il certificato precedente in un secondo momento facendo clic sul pulsante "...". pulsante Opzioni accanto al certificato precedente e selezionare Elimina, quindi fare clic su Salva.
Per ulteriori informazioni, vedere la documentazione [qui](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal#add-authenticationtrusted-root-certificates-of-back-end-servers).

Se si usa lo SKU V2 del servizio gateway applicazione/WAF, non è necessario caricare il nuovo certificato nelle impostazioni HTTP perché lo SKU V2 USA "certificati radice attendibili" e non è necessario eseguire alcuna azione.

## <a name="configuration---ingress-controller-for-aks"></a>Configurazione - Controller di ingresso per il servizio Azure Kubernetes

### <a name="what-is-an-ingress-controller"></a>Che cos'è un controller di ingresso?

Kubernetes consente di creare una risorsa `deployment` e `service` per esporre un gruppo di pod internamente nel cluster. Per esporre lo stesso servizio esternamente, viene definita una risorsa [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) che offre il bilanciamento del carico, la terminazione TLS e l'hosting virtuale basato sul nome.
Per soddisfare questa risorsa `Ingress`, è necessario un controller di ingresso che rimane in ascolto delle modifiche apportate alle risorse `Ingress` e configura i criteri del servizio di bilanciamento del carico.

Il controller di ingresso del gateway applicazione (AGIC) consente di usare [applicazione Azure gateway](https://azure.microsoft.com/services/application-gateway/) come ingresso per un [servizio Kubernetes di Azure](https://azure.microsoft.com/services/kubernetes-service/) , noto anche come cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Una singola istanza del controller di ingresso può gestire più gateway applicazione?

Attualmente, un'istanza del controller di ingresso può essere associata a un solo gateway applicazione.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Perché il cluster AKS con kubenet non funziona con AGIC?

AGIC tenta di associare automaticamente la risorsa della tabella di route alla subnet del gateway applicazione, ma potrebbe non riuscire a causa della mancanza di autorizzazioni per il AGIC. Se AGIC non è in grado di associare la tabella di route alla subnet del gateway applicazione, si verifica un errore nei log di AGIC. in tal caso, è necessario associare manualmente la tabella di route creata dal cluster AKS alla subnet del gateway applicazione. Per altre informazioni, vedere [route definite dall'utente supportate](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>È possibile connettere il cluster AKS e il gateway applicazione in reti virtuali separate? 

Sì, purché le reti virtuali vengano sottoposti a peering e non abbiano spazi di indirizzi sovrapposti. Se si esegue AKS con kubenet, assicurarsi di associare la tabella di route generata da AKS alla subnet del gateway applicazione. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Quali funzionalità non sono supportate nel componente aggiuntivo AGIC? 

Vedere le differenze tra AGIC distribuite tramite Helm e distribuito come componente aggiuntivo AKS [qui](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>Quando è consigliabile usare il componente aggiuntivo rispetto alla distribuzione Helm? 

Vedere le differenze tra AGIC distribuite tramite Helm e distribuito come componente aggiuntivo [AKS, in](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)particolare le tabelle che documentano quali scenari sono supportati da AGIC distribuiti tramite Helm anziché un componente aggiuntivo AKS. In generale, la distribuzione tramite Helm consente di testare le funzionalità beta e rilasciare i candidati prima di un rilascio ufficiale. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>È possibile controllare quale versione di AGIC verrà distribuita con il componente aggiuntivo?

No, il componente aggiuntivo AGIC è un servizio gestito che significa che Microsoft aggiornerà automaticamente il componente aggiuntivo alla versione stabile più recente. 

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quali tipi di log offre il gateway applicazione?

Il gateway applicazione offre tre log: 

* **ApplicationGatewayAccessLog** : il log di accesso contiene ogni richiesta inviata al front-end del gateway applicazione. I dati includono IP del chiamante, URL richiesto, latenza della risposta, codice restituito e byte in ingresso e in uscita. Il log contiene un record per ogni gateway applicazione.
* **ApplicationGatewayPerformanceLog** : il log delle prestazioni acquisisce le informazioni sulle prestazioni per ogni gateway applicazione. Le informazioni includono la velocità effettiva in byte, le richieste totali gestite, il numero di richieste non riuscite e il numero di istanze back-end integre e non integre.
* **ApplicationGatewayFirewallLog** : per i gateway applicazione configurati con WAF, il log del firewall contiene le richieste registrate tramite la modalità di rilevamento o la modalità di prevenzione.

Tutti i log vengono raccolti ogni 60 secondi. Per altre informazioni, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Come è possibile sapere se i membri del pool back-end sono integri?

Verificare lo stato di integrità usando il cmdlet di PowerShell `Get-AzApplicationGatewayBackendHealth` o il portale. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Che cosa sono i criteri di conservazione per i log di diagnostica?

Il flusso dei log di diagnostica viene inviato all'account di archiviazione del cliente. I clienti possono impostare i criteri di conservazione in base alle proprie preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a log di Monitoraggio di Azure. Per altre informazioni, vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Come si ottengono i log di controllo per il gateway applicazione?

Nel portale nel pannello del menu di un gateway applicazione selezionare **Log attività** per accedere al log di controllo. 

### <a name="can-i-set-alerts-with-application-gateway"></a>È possibile impostare avvisi con il gateway applicazione?

Sì. Nel gateway applicazione gli avvisi sono configurati nelle metriche. Per altre informazioni, vedere [Metriche per il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) e [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Come si analizzano le statistiche sul traffico per il gateway applicazione?

È possibile visualizzare e analizzare i log di accesso in diversi modi. Usare i log di Monitoraggio di Azure, Excel, Power BI e così via.

È anche possibile usare un modello di Resource Manager che installa ed esegue il diffuso analizzatore di log [GoAccess](https://goaccess.io/) per i log di accesso del gateway applicazione. GoAccess offre statistiche utili sul traffico HTTP, ad esempio numero di visitatori unici, file richiesti, host, sistemi operativi, browser, codici di stato HTTP. Per altre informazioni, in GitHub vedere il [file Readme nella cartella del modello di Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Cosa può fare in modo che l'integrità back-end restituisca uno stato sconosciuto?

In genere viene visualizzato uno stato sconosciuto quando l'accesso al back-end è bloccato da un gruppo di sicurezza di rete (NSG), un DNS personalizzato o un routing definito dall'utente (UDR) nella subnet del gateway applicazione. Per altre informazioni, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Esistono casi in cui i log del flusso del gruppo di sicurezza di rete non visualizzano il traffico consentito?

Sì. Se la configurazione corrisponde allo scenario seguente, non verrà visualizzato il traffico consentito nei log del flusso del gruppo di sicurezza di rete:
- È stato distribuito il gateway applicazione v2
- È presente un gruppo di sicurezza di rete nella subnet del gateway applicazione
- Sono stati abilitati i log del flusso del gruppo di sicurezza di rete nel gruppo di sicurezza

### <a name="does-application-gateway-store-customer-data"></a>Il gateway applicazione archivia i dati dei clienti?

No, il gateway applicazione non archivia i dati dei clienti.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione, vedere [Cos'è il gateway applicazione di Azure?](overview.md)
