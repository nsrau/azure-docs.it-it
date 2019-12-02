---
title: Servizio Frontdoor di Azure | Microsoft Docs
description: Questo articolo offre una panoramica di Azure Frontdoor. È possibile scoprire se si tratta della scelta giusta per il bilanciamento del carico del traffico utente per l'applicazione specifica.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: e92e51e8aabf24f1c5c4db31e2e203f391620ecc
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423480"
---
# <a name="what-is-azure-front-door-service"></a>Informazioni sul servizio Frontdoor di Azure
Il servizio Frontdoor di Azure consente di definire, gestire e monitorare il routing globale del traffico Web, rendendo le prestazioni ottimali e il failover globale immediato per una disponibilità elevata. Con Frontdoor è possibile trasformare il consumer globale (multi-area) e le applicazioni aziendali in applicazioni moderne, solide, personalizzate e dalle alte prestazioni, in API, con contenuti che raggiungono un audience globale grazie ad Azure.

Il Frontdoor funziona a livello 7 o HTTP e usa il protocollo di anycast con split TCP e con la rete globale di Microsoft per migliorare connettività globale. Pertanto, per la selezione del metodo di routing nella configurazione, è possibile garantire che Frontdoor instradi le richieste del client nel back-end dell'applicazione più veloce e maggiormente disponibile. Il backend di un'applicazione può essere qualsiasi servizio internet ospitato all'interno o all'esterno di Azure. Frontdoor offre diversi [metodi di routing del traffico](front-door-routing-methods.md) e [opzioni di monitoraggio dell'integrità del back-end](front-door-health-probes.md) per soddisfare diverse esigenze delle applicazioni e i modelli di failover automatico. Simile a [Gestione traffico](../traffic-manager/traffic-manager-overview.md), Frontdoor è resiliente agli errori, compreso l'errore di un'intera area di Azure.

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari. Se si cerca un routing globale basato su un DNS e **non** si dispone dei requisiti per la terminazione di protocollo Transport Layer Security (TLS) ("offload SSL") o di una richiesta HTTP/HTTPS, di una elaborazione a livello di applicazione, vedere [Gestione traffico](../traffic-manager/traffic-manager-overview.md). Se si cerca il bilanciamento del carico tra i server in un'area, un livello dell'applicazione, vedere [Gateway applicazione](../application-gateway/application-gateway-introduction.md) e per il bilanciamento del carico a livello di rete, vedere [Azure Load Balancer](../load-balancer/load-balancer-overview.md). Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.
>
> Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Frontdoor comprende le funzionalità seguenti:

## <a name="accelerate-application-performance"></a>Accelerare le prestazioni dell'applicazione
Usando il protocollo di anycast basato su split TCP, Frontdoor garantisce che gli utenti finali possano connettersi immediatamente al Frontdoor POP (Point of Presence) più vicino. Usare la rete globale di Microsoft per la connessione ai back-end dell'applicazione dai POP di Frontdoor garantisce una più elevata disponibilità e affidabilità mantenendo le prestazioni. Questa connettività al back-end è anche basata sulla minima latenza di rete. Per altre informazioni sulle tecniche di routing di Frontdoor, ad esempio [Split TCP ](front-door-routing-architecture.md#splittcp) e [Protocollo di anycast](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Aumentare la disponibilità dell'applicazione con probe di integrità intelligenti

Frontdoor assicura disponibilità elevata per le applicazioni critiche usando i probe di integrità intelligenti, monitorando sia la latenza che la disponibilità dei back-end e offrono un failover automatico istantaneo quando si verifica l'arresto di un back-end. Pertanto, è possibile eseguire operazioni pianificate di manutenzione delle applicazioni senza tempi di inattività. Frontdoor dirige il traffico verso back-end alternativi durante il corso della manutenzione.

## <a name="url-based-routing"></a>Routing basato su URL
Il routing basato su percorso URL consente di instradare il traffico verso pool di back-end in base ai percorsi URL della richiesta. Uno degli scenari è l'instradamento delle richieste di tipi di contenuto diversi a pool di back-end diversi.

Ad esempio, per le richieste `http://www.contoso.com/users/*` viene eseguito il rounting verso UserProfilePool, mentre per le richieste `http://www.contoso.com/products/*` viene eseguito il rounting verso ProductInventoryPool.  Frontdoor consente scenari di corrispondenza del percorso ancora più complessi usando i migliori algoritmi di corrispondenza. Pertanto, se nessuno dei criteri del percorso trova corrispondenza, viene selezionata la regola di gestione predefinita per `http://www.contoso.com/*` e il traffico viene indirizzato alla regola di gestione catch predefinita. Per altre informazioni vedere [Route Matching](front-door-route-matching.md) (Corridspondenza delle route).

## <a name="multiple-site-hosting"></a>Hosting di più siti
L'hosting di più siti consente di configurare più siti Web nella stessa configurazione di Frontdoor. Questa funzionalità consente di configurare una topologia più efficiente per le distribuzioni aggiungendo diversi siti Web a una singola configurazione di Frontdoor. A seconda dell'architettura dell'applicazione, è possibile configurare il servizio Frontdoor di Azure per indirizzare ogni sito Web al proprio pool di back-end o per indirizzare diversi siti Web allo stesso pool di back-end. Ad esempio, Frontdoor può servire il traffico per `images.contoso.com` e `videos.contoso.com` da due pool di back-end denominati ImagePool e VideoPool. In alternativa è possibile configurare entrambi gli host di front-end per indirizzare il traffico verso un pool di back-end singolo denominato MediaPool.

Analogamente, è possibile avere due domini diversi `www.contoso.com` e `www.fabrikam.com` configurati sullo stesso Frontdoor.

## <a name="session-affinity"></a>Affinità di sessione
La funzionalità affinità di sessione basata su cookie è utile quando si vuole mantenere una sessione utente nello stesso back-end dell'applicazione. Usando i cookie gestiti da Frontdoor, il traffico successivo proveniente da una sessione utente viene indirizzato allo stesso back-end dell'applicazione per l'elaborazione. Questa funzionalità è importante nei casi in cui lo stato di sessione viene salvato localmente nel back-end per una sessione utente.

## <a name="secure-sockets-layer-ssl-termination"></a>Terminazione di Secure Sockets Layer (SSL)
Frontdoor supporta la terminazione SSL sull'arco. Ciò significa che i singoli utenti possono configurare la connessione SSL con gli ambienti di Frontdoor anziché stabilire connessioni a lungo raggio con il back-end dell'applicazione. Inoltre, Frontdoor supporta sia la connettività HTTP che HTTPS tra gli ambienti di Frontdoor e i back-end. Pertanto, è possibile impostare anche la crittografia SSL end-to-end. Ad esempio, se Frontdoor riceve più di 5000 richieste in un minuto per il carico di lavoro dell'applicazione, a causa del riutilizzo delle connessioni a caldo, per i servizi attivi stabilisce solo 500 connessioni con il back-end dell'applicazione, riducendo il carico significativo sui back-end.

## <a name="custom-domains-and-certificate-management"></a>Dominio personalizzato e gestione di certificati
Quando si usa Frontdoor per distribuire un contenuto,è necessario un dominio personalizzato se si vuole che il nome di dominio sia visibile nell'URL del Frontdoor. Avere un nome di dominio visibile può essere pratico per i clienti e utile a scopo di personalizzazione.
Frontdoor supporta anche HTTPS per nomi di dominio personalizzato. Usare questa funzionalità scegliendo i certificati gestiti di Frontdoor o caricando il certificato SSL personalizzato.

## <a name="application-layer-security"></a>Sicurezza del livello dell'applicazione
Il servizio Frontdoor di Azure consente di creare regole di Web Application Firewall (WAF) personalizzate per il controllo degli accessi al fine di proteggere il carico di lavoro HTTP/HTTPS dallo sfruttamento basato su indirizzi IP del client, codice paese e parametri HTTP. Inoltre, Frontdoor consente di creare regole di limitazione della tariffa per combattere il dannoso traffico dei bot. Per altre informazioni su Web Application Firewall, vedere [Che cos'è Web Application Firewall di Azure?](../web-application-firewall/overview.md)

La stessa piattaforma Frontdoor è protetta da [Protezione DDoS di Azure](../virtual-network/ddos-protection-overview.md) di Siti Web Basic. Per una protezione maggiore, Protezione DDoS Standard di Azure può essere abilitata nelle reti virtuali per proteggere le risorse dagli attacchi a livello di rete (TCP/UDP) attraverso l'ottimizzazione automatica e la mitigazione. Frontdoor è un proxy inverso di livello 7 e consente solo al traffico Web di attraversare i back-end e bloccare altri tipi di traffico per impostazione predefinita.

## <a name="url-redirection"></a>Reindirizzamento URL
Con la forte richiesta degli addetti ai lavori per un supporto solo per comunicazioni sicure, le applicazioni Web dovrebbero reindirizzare automaticamente il traffico da HTTP a HTTPS. Questo garantisce che tutte le comunicazioni tra gli utenti e l'applicazione si verifichino tramite un percorso crittografato. 

Tradizionalmente, i proprietari delle applicazioni soddisfano questo requisito con la creazione di un servizio dedicato, il cui unico scopo è il reindirizzamento a HTTPS delle richieste ricevute su HTTP. Il servizio Frontdoor di Azure offre la possibilità di reindirizzare il traffico da HTTP a HTTPS. Questo semplifica la configurazione delle applicazioni, ottimizza l'utilizzo delle risorse e supporta i nuovi scenari di reindirizzamento, tra cui il reindirizzamento globale e basato sul percorso. Il reindirizzamento degli URL dal servizio Frontdoor di Azure non è limitato al reindirizzamento da HTTP a HTTPS, ma anche a un nome host diverso, a un percorso diverso o a una nuova stringa di query nell'URL.

Per altre informazioni, vedere come [reindirizzare il traffico](front-door-url-redirect.md) con il servizio Frontdoor di Azure.

## <a name="url-rewrite"></a>Riscrittura URL
Frontdoor supporta [URL rewrite](front-door-url-rewrite.md) (riscrivere URL) consentendo di configurare un percorso di trasferimento personalizzato opzionale da usare quando si costruisce la richiesta da trasferire al back-end. Frontdoor consente inoltre di configurare l'Intestazione host da inviare quando si trasferisce la richiesta al back-end.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Supporto del protocollo: traffico IPv6 e HTTP/2
Frontdoor di Azure supporta nativamente a connettività IPv6 end-to-end oltre al protocollo HTTP/2. 

I protocolli HTTP/2 consentono una comunicazione full duplex tra i back-end dell'applicazione e un client su una connessione TCP con esecuzione prolungata. HTTP/2 consente una comunicazione più interattiva tra il back-end e il client che può essere bidirezionale senza necessità di polling come richiesto per le implementazioni basate su HTTP. Il protocollo HTTP/2 dispone di un overhead ridotto, a differenza di HTTP, e può riutilizzare la stessa connessione TCP per più richieste o risposte, con conseguente utilizzo più efficiente delle risorse. Altre informazioni su [Supporto HTTP/2 del servizio Frontdoor di Azure](front-door-http2.md).

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere [Prezzi di Frontdoor](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
