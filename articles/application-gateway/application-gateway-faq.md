---
title: Domande frequenti sul gateway applicazione di Azure
description: Questa pagina offre risposte alle domande frequenti sul gateway applicazione di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 7b2a550c902e85caf02f05fcbbe5dd7b02acd0cc
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868854"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Domande frequenti sul gateway applicazione

## <a name="general"></a>Generale

**D. Che cos'è il gateway applicazione?**

Il gateway applicazione di Azure è un servizio di controller per la distribuzione di applicazioni (ADC) con numerose funzionalità di bilanciamento del carico di livello 7. Offre un servizio altamente disponibile e scalabile completamente gestito da Azure.

**D. Quali funzionalità supporta il gateway applicazione?**

Il gateway applicazione supporta la scalabilità automatica, l'offload SSL e SSL end-to-end, Web application firewall, affinità della sessione basata su cookie, routing basato su percorso URL, hosting multisito e altro. Per un elenco completo delle funzionalità supportate, vedere [Cos'è il gateway applicazione di Azure?](application-gateway-introduction.md)

**D. Qual è la differenza tra gateway applicazione e Azure Load Balancer?**

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7 e funziona quindi solo con il traffico Web (HTTP/HTTPS/WebSocket). Supporta funzionalità come terminazione SSL, affinità di sessione basata su cookie e round robin per il bilanciamento del carico del traffico. Load Balancer esegue il bilanciamento del carico del traffico al livello 4 (TCP/UDP).

**D. Quali protocolli supporta il gateway applicazione?**

Il gateway applicazione supporta HTTP, HTTPS, HTTP/2 e WebSocket.

**D. In che modo il gateway applicazione supporta HTTP/2?**

Il supporto del protocollo HTTP/2 è disponibile per i client che si connettono solo a listener del gateway applicazione. La comunicazione con i pool di server back-end avviene tramite HTTP/1.1. 

Per impostazione predefinita, il supporto di HTTP/2 è disabilitato. Il frammento di codice di esempio seguente, di Azure PowerShell, illustra come abilitarlo:

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

**D. Quali risorse sono supportate oggi nell'ambito del pool back-end?**

I pool back-end possono essere costituiti da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni, nomi di dominio completi (FQDN) e back-end multi-tenant come App Web di Azure. I membri del pool back-end del gateway applicazione non sono associati a un set di disponibilità. I membri del pool back-end possono trovarsi tra cluster, data center o all'esterno di Azure, purché abbiano connettività IP.

**D. In quale aree è disponibile il servizio?**

Il gateway applicazione è disponibile in tutte le aree di Azure globale. È anche disponibile in [Azure Cina](https://www.azure.cn/) e [Azure per enti pubblici](https://azure.microsoft.com/overview/clouds/government/)

**D. Si tratta di una distribuzione dedicata per la sottoscrizione o è condivisa tra clienti?**

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

**D. È supportato il reindirizzamento HTTP->HTTPS?**

Il reindirizzamento è supportato. Per altre informazioni, vedere la [Panoramica del reindirizzamento nel gateway applicazione](application-gateway-redirect-overview.md).

**D. In quale ordine vengono elaborati i listener?**

I listener vengono elaborati nell'ordine in cui sono visualizzati. Per tale motivo, se un listener di base corrisponde a una richiesta in ingresso, questa viene elaborata per prima.  I listener multisito devono essere configurati prima di un listener di base per garantire che il traffico sia indirizzato al back-end corretto.

**D. Dove è possibile trovare IP e DNS del gateway applicazione?**

Quando si usa un indirizzo IP pubblico come endpoint, queste informazioni sono reperibili nella risorsa dell'indirizzo IP pubblico o nella pagina Panoramica del gateway applicazione nel portale. Per gli indirizzi IP interni, queste informazioni si trovano nella pagina Panoramica.

**D. L'IP o il nome DNS cambia durante il ciclo di vita del gateway applicazione?**

L'indirizzo VIP può cambiare se il gateway applicazione viene arrestato e avviato. Il nome DNS associato al gateway applicazione non cambia durante il ciclo di vita del gateway. Per questo motivo è consigliabile usare un alias CNAME che punti all'indirizzo DNS del gateway applicazione.

**D. Il gateway applicazione supporta l'IP statico?**

Sì, lo SKU versione 2 del gateway applicazione supporta gli indirizzi IP pubblici statici. Lo SKU versione 1 supporta indirizzi IP interni statici.

**D. Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?**

Un gateway applicazione supporta un solo indirizzo IP pubblico.

**D. Quali dimensioni deve avere la subnet per il gateway applicazione?**

Il gateway applicazione utilizza un indirizzo IP privato per ogni istanza, oltre a un altro indirizzo IP privato in presenza di una configurazione degli indirizzi IP front-end privati. Inoltre, Azure riserva i primi quattro e l'ultimo indirizzo IP in ogni subnet per uso interno.
Se ad esempio il gateway applicazione è impostato su tre istanze e nessun IP front-end privato, le dimensioni necessarie per la subnet sono pari a /29 o superiori. In questo caso, il gateway applicazione usa tre indirizzi IP. Se si hanno tre istanze e un indirizzo IP per la configurazione degli indirizzi IP front-end privati, le dimensioni della subnet devono essere pari a /28 o superiori, perché sono necessari quattro indirizzi IP.

**D. Il gateway applicazione supporta le intestazioni x-forwarded-for?**

Sì, il gateway applicazione inserisce le intestazioni x-forwarded-for, x-forwarded-proto e x-forwarded-port nella richiesta inoltrata al back-end. Il formato dell'intestazione x-forwarded-for è un elenco di IP:Porta separato da virgole. I valori validi per x-forwarded-proto sono http o https. X-forwarded-port specifica la porta raggiunta dalla richiesta nel gateway applicazione.

Il gateway applicazione inserisce anche un'intestazione X-Original-Host contenente l'intestazione Host originale con cui è arrivata la richiesta. Questa intestazione è utile in scenari come l'integrazione di un sito Web di Azure, in cui l'intestazione host in ingresso viene modificata prima che il traffico venga indirizzato al back-end.

**D. Quanto tempo occorre per distribuire un gateway applicazione? Il gateway applicazione funziona durante l'aggiornamento?**

Nuove distribuzioni di SKU versione 1 del gateway applicazione possono richiedere fino a 20 minuti per effettuare il provisioning. Le modifiche all'istanza di conteggio/dimensioni non causano problemi e il gateway rimane attivo durante questo periodo di tempo.

Le distribuzioni di SKU versione 2 possono richiedere circa cinque e sei minuti per effettuare il provisioning.

## <a name="configuration"></a>Configurazione

**D. Il gateway applicazione viene sempre distribuito in una rete virtuale?**

Sì, il gateway applicazione viene sempre distribuito in una subnet di rete virtuale. Questa subnet può contenere solo gateway applicazione.

**D. Il gateway applicazione può comunicare con istanze all'esterno della rete virtuale?**

Il gateway applicazione può comunicare con le istanze all'esterno della rete virtuale nella quale si trova se è disponibile la connettività IP. Se si prevede di usare indirizzi IP interni come membri del pool back-end, è necessario il [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) o il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**D. È possibile distribuire altri elementi nella subnet del gateway applicazione?**

No, ma è possibile distribuire altri gateway applicazione nella subnet.

**D. I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?**

I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione con le restrizioni seguenti:

* Devono essere inserite eccezioni per il traffico in ingresso sulle porte 65503-65534 per lo SKU versione 1 del gateway applicazione e sulle porte 65200-65535 per lo SKU versione 2. Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.

* La connettività Internet in uscita non può essere bloccata.

* È necessario consentire il traffico dal tag AzureLoadBalancer.

**D. Le route definite dall'utente sono supportate nella subnet del gateway applicazione?**

Le route definite dall'utente (Udr) sono supportate nella subnet del gateway applicazione, purché non modificano la comunicazione end-to-end di richiesta/risposta.

Ad esempio, è possibile configurare una route UDR nella subnet del gateway applicazione in modo che punti a un'appliance firewall per l'ispezione dei pacchetti, ma è necessario assicurarsi che il pacchetto possa raggiungere l'ispezione dei post relativa alla destinazione prevista. In caso contrario, potrebbe tradursi in probe di integrità o comportamento di instradamento del traffico non corretti. Sono incluse route o route 0.0.0.0/0 predefinite propagate dal gateway VPN o ExpressRoute nella rete virtuale.

**D. Quali sono i limiti del gateway applicazione? È possibile aumentare questi limiti?**

Vedere [Limiti del gateway applicazione](../azure-subscription-service-limits.md#application-gateway-limits) per informazioni sui limiti.

**D. È possibile usare il gateway applicazione per il traffico interno ed esterno contemporaneamente?**

Sì, il gateway applicazione supporta un indirizzo IP interno e un indirizzo IP esterno.

**D. Il peering reti virtuali è supportato?**

Sì, il peering reti virtuali è supportato ed è utile per il bilanciamento del carico del traffico in altre reti virtuali.

**D. È possibile comunicare con server locali se connessi da tunnel VPN o ExpressRoute?**

Sì, se il traffico è consentito.

**D. È possibile avere un pool back-end che serve molte applicazioni su porte diverse?**

L'architettura di microservizi è supportata. È necessario configurare più impostazioni HTTP per il probe su porte diverse.

**D. I probe personalizzati supportano caratteri jolly o regex nei dati di risposta?**

I probe personalizzati non supportano caratteri jolly o regex nei dati di risposta. 

**D. Come vengono elaborate le regole?**

Le regole vengono elaborate nell'ordine in cui sono configurate. È consigliabile che le regole multisito siano configurate prima delle regole di base per ridurre le probabilità che il traffico sia indirizzato a un back-end inappropriato, in quanto la regola di base selezionarebbe il traffico in base alla porta prima che venga valutata la regola multisito.

**D. Cosa indica il campo Host per i probe personalizzati?**

Il campo Host specifica il nome a cui inviare il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM ed è nel formato \<protocollo\>://\<host\>:\<porta\>\<percorso\>.

**D. È possibile consentire l'accesso al gateway applicazione ad alcuni IP di origine?**

Questo scenario è possibile usando gruppi di sicurezza di rete nella subnet del gateway applicazione. Nella subnet nell'ordine di priorità elencato, è necessario inserire le restrizioni seguenti:

* Consentire il traffico in ingresso dall'intervallo di IP/IP di origine.

* Consentire le richieste in ingresso da tutte le origini alle porte 65503-65534 per la [comunicazione integrità back-end](application-gateway-diagnostics.md). Questo intervallo di porte è necessario per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non saranno in grado di avviare alcuna modifica su tali endpoint.

* Consentire probe di bilanciamento del carico di Azure in ingresso (tag AzureLoadBalancer) e il traffico di rete virtuale in ingresso (tag VirtualNetwork) nei [gruppi di sicurezza di rete](../virtual-network/security-overview.md).

* Bloccare tutto il traffico in ingresso con una regola Nega tutto.

* Consentire il traffico in uscita a Internet per tutte le destinazioni.

**D. La stessa porta può essere usata per i listener pubblici e privati?**

No, questa operazione non è supportata.

## <a name="performance"></a>Prestazioni

**D. In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?**

Lo SKU versione 1 del gateway applicazione supporta scenari di disponibilità elevata se sono presenti due o più istanze distribuite. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Lo SKU versione 1 supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

Lo SKU V2 garantisce automaticamente che le nuove istanze vengono distribuite tra domini di errore e domini di aggiornamento. Se si sceglie la ridondanza della zona, le istanze più recenti vengono anche distribuite tra le zone di disponibilità per offrire resilienza dagli errori delle zone.

**D. Come si ottiene uno scenario di ripristino di emergenza tra data center con un gateway applicazione?**

I clienti possono usare Gestione traffico per distribuire il traffico tra più gateway applicazione in data center diversi.

**D. La scalabilità automatica è supportata?**

Sì, lo SKU versione 2 del gateway applicazione supporta la scalabilità automatica. Per altre informazioni, vedere [Gateway applicazione con scalabilità automatica ridondanza della zona (anteprima pubblica)](application-gateway-autoscaling-zone-redundant.md).

**D. Il ridimensionamento manuale causa tempi di inattività?**

Non si verificano tempi di inattività, le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

**D. Il gateway applicazione supporta l'esaurimento delle connessioni?**

Sì. È possibile configurare l'esaurimento delle connessioni per modificare i membri all'interno di un pool back-end senza interruzioni. In questo modo le connessioni esistenti continuano a essere inviate alla relativa destinazione precedente fino a quando tale connessione viene chiusa o un timeout configurabile scade. L'esaurimento delle connessioni attende solo il completamento delle connessioni correnti in corso. Gateway applicazione non rileva lo stato della sessione applicazione.

**D. Quali sono le dimensioni del gateway applicazione?**

Il servizio Gateway applicazione è attualmente disponibile in tre dimensioni: **Small**, **Medium** e **Large**. Le dimensioni delle istanze piccole sono destinate a scenari di sviluppo e test.

È possibile creare fino a 50 gateway applicazione per sottoscrizione e ogni gateway applicazione può includere fino a 10 istanze. Ogni gateway applicazione può essere costituito da 20 listener HTTP. Per un elenco completo dei limiti del gateway applicazione, vedere i [limiti del servizio Gateway applicazione](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

La tabella seguente illustra una velocità effettiva media delle prestazioni per ogni istanza del gateway applicazione con offload SSL abilitato:

| Dimensioni medie risposta della pagina di back-end | Piccolo | Media | Grande |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Questi valori sono indicazioni approssimative della velocità effettiva di un gateway applicazione. La velocità effettiva dipende da vari dettagli ambientali come le dimensioni medie delle pagine, la posizione delle istanze back-end e il tempo di elaborazione per gestire una pagina. Per dati esatti sulle prestazioni, è consigliabile eseguire propri test. Questi valori vengono forniti solo come indicazioni per la pianificazione della capacità.

**D. È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?**

Sì, Azure distribuisce le istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Il gateway applicazione supporta la scalabilità con l'aggiunta di più istanze dello stesso gateway per la condivisione del carico.

## <a name="ssl-configuration"></a>Configurazione SSL

**D. Quali certificati sono supportati dal gateway applicazione?**

Sono supportati certificati autofirmati, certificati della CA e certificati con caratteri jolly. I certificati di convalida estesa non sono supportati.

**D. Quali sono i pacchetti di crittografia attualmente supportati dal gateway applicazione?**

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

**D. Il gateway applicazione supporta anche la ripetizione della crittografia del traffico nel back-end?**

Sì, il gateway applicazione supporta l'offload SSL e l'SSL end-to-end, che consente di crittografare nuovamente il traffico nel back-end.

**D. È possibile configurare i criteri SSL per gestire le versioni del protocollo SSL?**

Sì, è possibile configurare il gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 sono già disabilitati per impostazione predefinita e non sono configurabili.

**D. È possibile configurare l'ordine dei pacchetti di crittografia e dei criteri?**

Sì, la [configurazione dei pacchetti di crittografia](application-gateway-ssl-policy-overview.md) è supportata. Quando si definiscono criteri personalizzati, deve essere abilitato almeno uno dei pacchetti di crittografia seguenti. Il gateway applicazione usa SHA256 per la gestione del back-end.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

**D. Quanti certificati SSL sono supportati?**

Sono supportati fino a 20 certificati SSL.

**D. Quanti certificati di autenticazione sono supportati per la ripetizione della crittografia nel back-end?**

Sono supportati fino a 10 certificati di autenticazione con un valore predefinito di 5.

**D. Il gateway applicazione si integra con Azure Key Vault in modo nativo?**

No, non è integrato con Azure Key Vault.

## <a name="web-application-firewall-waf-configuration"></a>Configurazione del Web application firewall (WAF)

**D. Lo SKU del WAF offre tutte le funzionalità disponibili con lo SKU Standard?**

Sì, il WAF offre tutte le funzionalità disponibili nello SKU Standard.

**D. Qual è la versione di CRS supportata dal gateway applicazione?**

Il gateway applicazione supporta CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

**D. Come si monitora il Web application firewall?**

Il Web application firewall viene monitorato tramite la registrazione diagnostica. Per altre informazioni sulla registrazione diagnostica, vedere [Integrità back-end, registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md)

**D. La modalità di rilevamento blocca il traffico?**

No, la modalità di rilevamento registra solo il traffico che ha attivato una regola del Web application firewall.

**D. Come si personalizzano le regole del Web application firewall?**

Sì, le regole WAF sono personalizzabili. Per altre informazioni sulla relativa personalizzazione, vedere [Personalizzare le regole del web application firewall con il portale di Azure](application-gateway-customize-waf-rules-portal.md)

**D. Quali regole sono attualmente disponibili?**

Il Web application firewall supporta attualmente CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), che offre la protezione di base per la maggior parte delle prime 10 vulnerabilità identificate dall'Open Web Application Security Project (OWASP) disponibili in [Prime 10 vulnerabilità OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Protezione dagli attacchi SQL injection

* Protezione dagli attacchi di scripting intersito

* Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

* Protezione dalle violazioni del protocollo HTTP

* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

* Prevenzione contro robot, crawler e scanner

 * Rilevamento di errori di configurazione dell'applicazione comuni (ad esempio, Apache, IIS e così via)

**D. Il WAF supporta anche la prevenzione DDoS?**

Sì. È possibile abilitare Protezione DDos nella rete virtuale in cui viene distribuito il gateway applicazione. Ciò assicura che anche l'indirizzo IP virtuale del gateway applicazione sia protetto dal servizio Protezione DDos di Azure.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

**D. Quali tipi di log sono disponibili con il gateway applicazione?**

Sono disponibili tre log con il gateway applicazione. Per altre informazioni su questi log e altre funzionalità di diagnostica, vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog**: il log di accesso contiene ogni richiesta inviata al front-end del gateway applicazione. I dati includono indirizzo IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza di gateway applicazione.
- **ApplicationGatewayPerformanceLog**: il log delle prestazioni acquisisce le informazioni sulle prestazioni di ogni singola istanza, come il totale delle richieste servite, la velocità effettiva in byte, il numero delle richieste non riuscite e il numero delle istanze back-end integre e non integre.
- **ApplicationGatewayFirewallLog**: il log del firewall contiene le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il Web application firewall.

**D. Come è possibile sapere se i membri del pool back-end sono integri?**

È possibile usare il cmdlet `Get-AzureRmApplicationGatewayBackendHealth` di PowerShell o verificare l'integrità tramite il portale. Vedere la [diagnostica del gateway applicazione](application-gateway-diagnostics.md).

**D. Che cos'è il criterio di conservazione per i log di diagnostica?**

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a Log Analytics. Vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md) per altri dettagli.

**D. Come si ottengono i log di controllo per il gateway applicazione?**

Sono disponibili log di controllo per il gateway applicazione. Nel portale fare clic su **Log attività** nel pannello del menu di un gateway applicazione per accedere al log di controllo. 

**D. È possibile impostare avvisi con il gateway applicazione?**

Sì, il gateway applicazione supporta gli avvisi. Gli avvisi vengono configurati nelle metriche. Per altre informazioni sulle metriche del gateway applicazione, vedere le [metriche del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics). Per altre informazioni sugli avvisi, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**D. Come si analizzano le statistiche sul traffico per il gateway applicazione?**

È possibile visualizzare e analizzare i log di accesso tramite una serie di meccanismi, ad esempio Azure Log Analytics, Excel, Power BI e così via.

È stato anche pubblicato un modello di Resource Manager che installa ed esegue l'analizzatore di log [GoAccess](https://goaccess.io/), di ampia diffusione, per i log di accesso del gateway applicazione. GoAccess offre statistiche utili sul traffico HTTP, ad esempio il numero di visitatori unici, file richiesti, host, sistemi operativi, browser, codici di stato HTTP e altro ancora. Per altre informazioni, vedere il [file Readme nella cartella del modello di Resource Manager in GitHub](https://aka.ms/appgwgoaccessreadme).

**D. L'integrità back-end restituisce uno stato sconosciuto. Quale potrebbe essere la causa?**

Nella maggior parte dei casi, l'accesso al back-end è bloccato da un gruppo di sicurezza di rete o un DNS personalizzato. Vedere [Integrità back-end, log di diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione, vedere [Cos'è il gateway applicazione di Azure?](overview.md)
