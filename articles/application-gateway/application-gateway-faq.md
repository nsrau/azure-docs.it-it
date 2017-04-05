---
title: Domande frequenti sul gateway applicazione di Azure | Microsoft Docs
description: Questa pagina offre risposte alle domande frequenti sul gateway applicazione di Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d54ee7ec-4d6b-4db7-8a17-6513fda7e392
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 037045c4e76d0fb8e96944fe8a3235223594a034
ms.lasthandoff: 03/30/2017


---

# <a name="frequently-asked-questions-for-application-gateway"></a>Domande frequenti sul gateway applicazione

## <a name="general"></a>Generale

**D. Che cos'è il gateway applicazione?**

Il gateway applicazione di Azure è un servizio di controller per la distribuzione di applicazioni (ADC) con numerose funzionalità di bilanciamento del carico di livello 7. Offre un servizio altamente disponibile e scalabile completamente gestito da Azure.

**D. Quali funzionalità supporta il gateway applicazione?**

Il gateway applicazione supporta l'offload SSL e l'SSL end-to-end, il Web application firewall (anteprima), l'affinità di sessione basata su cookie, il routing basato sul percorso dell'URL, l'hosting di più siti e altro. Per un elenco completo delle funzionalità supportate, vedere la [panoramica del gateway applicazione](application-gateway-introduction.md)

**D. Qual è la differenza tra gateway applicazione e Azure Load Balancer?**

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7, ovvero gestisce solo il traffico Web (HTTP/HTTPS/WebSocket). Supporta funzionalità di bilanciamento del carico delle applicazioni quali la terminazione SSL, l'affinità di sessione basata su cookie e round robin per eseguire il bilanciamento del carico del traffico. Load Balancer esegue il bilanciamento del carico del traffico al livello 4 (TCP/UDP).

**D. Quali protocolli supporta il gateway applicazione?**

Il gateway applicazione supporta HTTP, HTTPS e WebSocket.

**D. Quali risorse sono supportate oggi nell'ambito del pool back-end?**

I pool back-end possono essere costituiti da schede di interfaccia di rete, set di scalabilità di macchine virtuali, indirizzi IP pubblici, indirizzi IP interni e nomi di dominio completi. Il supporto per le app Web di Azure non è attualmente disponibile. I membri del pool back-end del gateway applicazione non sono associati a un set di disponibilità. I membri del pool back-end possono trovarsi tra cluster, data center o all'esterno di Azure, purché abbiano connettività IP.

**D. In quale aree è disponibile il servizio?**

Il gateway applicazione è disponibile in tutte le aree di Azure pubblico. È anche disponibile in [Azure Cina](https://www.azure.cn/) e [Azure per enti pubblici](https://azure.microsoft.com/en-us/overview/clouds/government/)

**D. Si tratta di una distribuzione dedicata per la sottoscrizione o è condivisa tra clienti?**

Il gateway applicazione è una distribuzione dedicata nella rete virtuale.

**D. È supportato il reindirizzamento HTTP->HTTPS?**

Non supportato attualmente.

**D. Dove è possibile trovare IP e DNS del gateway applicazione?**

Quando si usa un indirizzo IP pubblico come endpoint, queste informazioni sono reperibili nella risorsa dell'indirizzo IP pubblico o nella pagina Panoramica del gateway applicazione nel portale. Per gli indirizzi IP interni, queste informazioni si trovano nella pagina Panoramica.

**D. L'IP o il DNS cambia durante il ciclo di vita del gateway applicazione?**

L'indirizzo VIP può cambiare se il gateway viene arrestato e avviato dal cliente. Il DNS associato al gateway applicazione non cambia durante il ciclo di vita del gateway. Per questo motivo è consigliabile usare un alias CNAME che punti all'indirizzo DNS del gateway applicazione.

**D. Il gateway applicazione supporta l'IP statico?**

No, il gateway applicazione non supporta indirizzi IP pubblici statici, ma supporta indirizzi IP interni statici.

**D. Il gateway applicazione supporta più indirizzi IP pubblici nel gateway?**

Il gateway applicazione supporta un solo indirizzo IP pubblico.

**D. Il gateway applicazione supporta le intestazioni x-forwarded-for?**

Sì, il gateway applicazione inserisce le intestazioni x-forwarded-for, x-forwarded-proto e x-forwarded-port nella richiesta inoltrata al back-end. Il formato dell'intestazione x-forwarded-for è un elenco di IP:Porta separato da virgole. I valori validi per x-forwarded-proto sono http o https. X-forwarded-port specifica la porta raggiunta dalla richiesta nel gateway applicazione.

## <a name="configuration"></a>Configurazione

**D. Il gateway applicazione viene sempre distribuito in una rete virtuale?**

Sì, il gateway applicazione viene sempre distribuito in una subnet di rete virtuale. Questa subnet può contenere solo gateway applicazione.

**D. Il gateway applicazione può comunicare con istanze all'esterno della rete virtuale?**

Il gateway applicazione può comunicare con le istanze all'esterno della rete virtuale nella quale si trova se è disponibile la connettività IP. Se si prevede di usare indirizzi IP interni come membri del pool back-end, è necessario il [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md) o il [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

**D. È possibile distribuire altri elementi nella subnet del gateway applicazione?**

No, ma è possibile distribuire altri gateway applicazione nella subnet

**D. I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione?**

I gruppi di sicurezza di rete sono supportati nella subnet del gateway applicazione, ma è necessario applicare eccezioni per le porte 65503-65534 per il corretto funzionamento dell'integrità back-end. La connettività Internet in uscita non deve essere bloccata.

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

**D. Cosa indica il campo Host per i probe personalizzati?**

Il campo Host specifica il nome a cui inviare il probe. Applicabile solo quando vengono configurati più siti nel gateway applicazione. In caso contrario, usare "127.0.0.1". Questo valore è diverso dal nome host della VM ed è nel formato \<protocollo\>://\<host\>:\<porta\>\<percorso\>. 

## <a name="performance"></a>Prestazioni

**D. In che modo il gateway applicazione supporta la disponibilità elevata e la scalabilità?**

Il gateway applicazione supporta scenari di disponibilità elevata se si hanno più di 2 istanze distribuite. Azure distribuisce queste istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Il gateway applicazione supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

**D. Come si ottiene uno scenario di ripristino di emergenza tra data center con un gateway applicazione?**

I clienti possono usare Gestione traffico per distribuire il traffico tra più gateway applicazione in data center diversi.

**D. La scalabilità automatica è supportata?**

No, ma il gateway applicazione offre una metrica di velocità effettiva che può essere usata per ricevere un avviso se viene raggiunta una soglia. L'operazione manuale di aggiunta di istanze o ridimensionamento non provoca il riavvio del gateway e non ha alcun impatto sul traffico esistente.

**D. Il ridimensionamento manuale causa tempi di inattività?**

Non si verificano tempi di inattività, le istanze vengono distribuite tra domini di aggiornamento e domini di errore.

**D. È possibile modificare le dimensioni di un'istanza da medie a grandi senza interruzioni?**

Sì, Azure distribuisce le istanze nei domini di errore e di aggiornamento per impedire l'arresto simultaneo di tutte le istanze. Il gateway applicazione supporta la scalabilità tramite l'aggiunta di più istanze dello stesso gateway per ripartire il carico.

## <a name="ssl-configuration"></a>Configurazione SSL

**D. Quali certificati sono supportati dal gateway applicazione?**

Sono supportati certificati autofirmati, certificati della CA e certificati con caratteri jolly. I certificati di convalida estesa non sono supportati.

**D. Quali sono i pacchetti di crittografia attualmente supportati dal gateway applicazione?**

Di seguito sono indicati i pacchetti di crittografia attualmente supportati in ordine di priorità.

TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384

TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256

TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256

TLS_RSA_WITH_AES_256_GCM_SHA384

TLS_RSA_WITH_AES_128_GCM_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA256

TLS_RSA_WITH_AES_128_CBC_SHA256

TLS_RSA_WITH_AES_256_CBC_SHA

TLS_RSA_WITH_AES_128_CBC_SHA

TLS_RSA_WITH_3DES_EDE_CBC_SHA

**D. Il gateway applicazione supporta anche la ripetizione della crittografia del traffico nel back-end?**

Sì, il gateway applicazione supporta l'offload SSL e l'SSL end-to-end, che consente di crittografare nuovamente il traffico nel back-end.

**D. È possibile configurare i criteri SSL per gestire le versioni del protocollo SSL?**

Sì, è possibile configurare il gateway applicazione per rifiutare TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 sono già disabilitati per impostazione predefinita e non sono configurabili.

**D. È possibile configurare i criteri SSL per gestire i pacchetti di crittografia?**

No, non attualmente.

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

Sì, le regole WAF sono personalizzabili. Per altre informazioni sulla relativa personalizzazione, vedere [Customize WAF rule groups and rules](application-gateway-customize-waf-rules-portal.md) (Personalizzare le regole e i gruppi di regole di WAF)

**D. Quali regole sono attualmente disponibili?**

Il Web application firewall supporta attualmente CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), che offre la protezione di base per la maggior parte delle prime 10 vulnerabilità identificate dall'Open Web Application Security Project (OWASP) disponibili in [Prime 10 vulnerabilità OWASP](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)

* Protezione dagli attacchi SQL injection

* Protezione dagli attacchi di scripting intersito

* Protezione dai comuni attacchi Web, ad esempio attacchi di iniezione di comandi, richieste HTTP non valide, attacchi HTTP Response Splitting e Remote File Inclusion

* Protezione dalle violazioni del protocollo HTTP

* Protezione contro eventuali anomalie del protocollo HTTP, ad esempio user agent host mancante e accept header

* Prevenzione contro robot, crawler e scanner

* Rilevamento di errori di configurazione comuni dell'applicazione (ad esempio, Apache, IIS e così via)

**D. Il WAF supporta anche la prevenzione DDoS?**

No, non fornisce la prevenzione DDoS.

## <a name="diagnostics-and-logging"></a>Diagnostica e registrazione

**D. Quali tipi di log sono disponibili con il gateway applicazione?**

Sono disponibili tre log con il gateway applicazione. Per altre informazioni su questi log e altre funzionalità di diagnostica, vedere [Integrità back-end, registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md).

- **ApplicationGatewayAccessLog**: questo log contiene ogni richiesta inviata al front-end del gateway applicazione. I dati includono indirizzo IP del chiamante, URL richiesto, latenza della risposta, codice restituito, byte in ingresso e in uscita. Il log di accesso viene raccolto ogni 300 secondi. Il log contiene un record per ogni istanza del gateway applicazione.
- **ApplicationGatewayPerformanceLog**: questo log acquisisce le informazioni sulle prestazioni di ogni singola istanza, come il totale delle richieste servite, la velocità effettiva in byte, il numero delle richieste non riuscite e il numero delle istanze back-end integre e non integre.
- **ApplicationGatewayFirewallLog**: questo log contiene le richieste registrate tramite la modalità di rilevamento o prevenzione di un gateway applicazione configurato con il Web application firewall.

**D. Come è possibile sapere se i membri del pool back-end sono integri?**

È possibile usare il cmdlet `Get-AzureRmApplicationGatewayBackendHealth` di PowerShell o verificare l'integrità tramite il portale. Vedere la [diagnostica del gateway applicazione](application-gateway-diagnostics.md).

**D. Che cos'è il criterio di conservazione per i log di diagnostica?**

I log di diagnostica vengono inseriti nell'account di archiviazione dei clienti, i quali possono impostare i criteri di conservazione in base alle preferenze. I log di diagnostica possono anche essere inviati a un hub eventi o a Log Analytics. Vedere [Diagnostica del gateway applicazione](application-gateway-diagnostics.md) per altri dettagli.

**D. Come si ottengono i log di controllo per il gateway applicazione?**

Sono disponibili log di controllo per il gateway applicazione. Nel portale fare clic su **Log attività** nel pannello del menu di un gateway applicazione per accedere al log di controllo. 

**D. È possibile impostare avvisi con il gateway applicazione?**

Sì, il gateway applicazione supporta gli avvisi, che vengono configurati in base alle metriche.  Il gateway applicazione ha attualmente una metrica di "velocità effettiva" che può essere configurata per l'invio di avvisi. Per altre informazioni sugli avvisi, vedere [Ricevere notifiche di avviso](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

**D. L'integrità back-end restituisce uno stato sconosciuto. Quale potrebbe essere la causa?**

Nella maggior parte dei casi, l'accesso al back-end è bloccato da un gruppo di sicurezza di rete o un DNS personalizzato. Vedere [Integrità back-end, registrazione diagnostica e metriche per il gateway applicazione](application-gateway-diagnostics.md) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul gateway applicazione, vedere la [panoramica del gateway applicazione](application-gateway-introduction.md).
