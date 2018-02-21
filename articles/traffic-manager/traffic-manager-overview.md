---
title: "Che cos'è Gestione traffico | Microsoft Docs"
description: Questo articolo fornisce informazioni su Gestione traffico e permette di determinare se sia la scelta giusta per il routing del traffico dell'applicazione
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-traffic-manager"></a>Panoramica di Gestione traffico

Gestione traffico di Microsoft Azure consente di controllare la distribuzione del traffico utente per gli endpoint di servizio in diversi data center. Gli endpoint di servizio supportati da Gestione traffico includono servizi cloud, app Web e macchine virtuali di Azure. È anche possibile usare Gestione traffico con endpoint esterni, non di Azure.

Gestione traffico usa il sistema DNS (Domain Name System) per indirizzare le richieste del client all'endpoint più appropriato in base a un metodo di routing del traffico e all'integrità degli endpoint. Gestione traffico offre diversi [metodi di routing del traffico](traffic-manager-routing-methods.md) e [opzioni di monitoraggio degli endpoint](traffic-manager-monitoring.md) per soddisfare le diverse esigenze delle applicazioni e i modelli di failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.

## <a name="traffic-manager-benefits"></a>Vantaggi di Gestione traffico

Con Gestione traffico è possibile:

* **Migliorare la disponibilità delle applicazioni critiche**

    Gestione traffico assicura la disponibilità elevata delle applicazioni attraverso il monitoraggio degli endpoint e il failover automatico in caso di inattività di un endpoint.

* **Migliorare la velocità di risposta delle applicazioni a prestazioni elevate**

    Azure consente di eseguire servizi cloud o siti Web in datacenter di tutto il mondo. Gestione traffico può migliorare la velocità di risposta delle applicazioni indirizzando il traffico all'endpoint con la minore latenza di rete per il client.

* **Eseguire la manutenzione dei servizi senza tempi di inattività**

    È possibile eseguire le operazioni pianificate di manutenzione delle applicazioni senza tempi di inattività. Durante l'esecuzione delle operazioni di manutenzione, Gestione traffico indirizza il traffico verso endpoint alternativi.

* **Combinare applicazioni locali e applicazioni basate sul cloud**

    Gestione traffico supporta anche endpoint esterni, non di Azure, e può quindi essere usato con distribuzioni cloud ibride e locali, inclusi gli scenari di tipo "burst nel cloud", "migrazione nel cloud" o "failover nel cloud".

* **Distribuire il traffico in distribuzioni complesse e di grandi dimensioni**

    Usando i [profili di Gestione traffico annidati](traffic-manager-nested-profiles.md) è possibile combinare metodi di routing del traffico per creare regole sofisticare e flessibili in grado di supportare le esigenze di distribuzioni più complesse e di maggiori dimensioni.

## <a name="how-traffic-manager-works"></a>Modalità di funzionamento di Gestione traffico

Gestione traffico di Azure consente di controllare in che modo il traffico viene distribuito tra gli endpoint dell'applicazione. Un endpoint è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure.

Gestione traffico offre due vantaggi principali:

1. Distribuzione del traffico in base a uno dei diversi [metodi di routing del traffico](traffic-manager-routing-methods.md)
2. [Monitoraggio continuo dell'integrità degli endpoint](traffic-manager-monitoring.md) e failover automatico quando si verificano errori sugli endpoint.

Quando un client tenta di connettersi a un servizio, è necessario prima risolvere il nome DNS del servizio a un indirizzo IP. Il client si connette quindi a questo indirizzo IP per accedere al servizio.

**Il punto più importante da comprendere è che Gestione traffico lavora a livello di DNS.**  Gestione traffico usa DNS per indirizzare i client a specifici endpoint di servizio in base alle regole del metodo di routing del traffico. I client si connettono quindi **direttamente** all'endpoint selezionato. Gestione traffico non è un proxy o un gateway. Gestione traffico non visualizza il traffico tra il client e il servizio.

### <a name="traffic-manager-example"></a>Esempio di Gestione traffico

Contoso Corp ha sviluppato un nuovo portale per i partner. L'URL per questo portale è https://partners.contoso.com/login.aspx. L'applicazione è ospitata in tre aree di Azure. Per migliorare la disponibilità e ottimizzare le prestazioni globali, viene usato Gestione traffico per distribuire il traffico client all'endpoint disponibile più vicino.

Per ottenere questa configurazione, si completa la seguente procedura:

1. Distribuire tre istanze del servizio. I nomi DNS di queste distribuzioni sono 'contoso-us.cloudapp.net', 'contoso-eu.cloudapp.net' e 'contoso-asia.cloudapp.net'.
2. Creare un profilo di Gestione traffico con il nome "contoso.trafficmanager.net", e configurarlo per usare il metodo di routing del traffico "Prestazioni" tra i 3 endpoint.
* Configurare il nome di dominio personale "partners.contoso.com" in modo che punti a "contoso.trafficmanager.net" tramite il record DNS CNAME.

![Configurazione DNS di Gestione traffico][1]

> [!NOTE]
> Quando si usa un dominio personale con Gestione traffico di Azure, è necessario usare un record CNAME per scegliere il nome del dominio personale sul nome di dominio di Gestione traffico. Gli standard DNS non consentono di creare un record CNAME al "vertice" (o alla radice) di un dominio. Non è quindi possibile creare un record CNAME per "contoso.com", detto anche dominio "naked". È possibile creare solo un record CNAME per un dominio in "contoso.com", ad esempio "www.contoso.com". Per risolvere questa limitazione, è consigliabile usare un semplice reindirizzamento HTTP per indirizzare le richieste di "contoso.com" su un nome alternativo, ad esempio "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Come si connettono i client tramite Gestione traffico

Per continuare l'esempio precedente, quando un client richiede la pagina https://partners.contoso.com/login.aspx, il client esegue i passaggi seguenti per risolvere il nome DNS e stabilire una connessione:

![Stabilire una connessione tramite Gestione traffico][2]

1. Il client invia una query DNS al relativo servizio DNS ricorsivo configurato per risolvere il nome "partners.contoso.com". Un servizio DNS ricorsivo, denominato anche servizio "DNS locale", non ospita direttamente i domini DNS, ma delega il lavoro di contattare i vari servizi DNS autorevoli su Internet necessari per risolvere un nome DNS.
2. Per risolvere il nome DNS, il servizio DNS ricorsivo cerca i server dei nomi per il dominio "contoso.com". A questo punto, il servizio contatta i server dei nomi per richiedere il record DNS di "partners.contoso.com". I server DNS contoso.com restituiscono il record CNAME che punta a contoso.trafficmanager.net.
3. Il servizio DNS ricorsivo quindi trova i server dei nomi per il dominio "trafficmanager, net", che vengono forniti dal servizio Gestione traffico di Azure. Quindi invia una richiesta per il record DNS "contoso.trafficmanager.net" a questi server DNS.
4. I server dei nomi di Gestione traffico ricevono la richiesta. Scelgono un endpoint in base a:

    - Lo stato configurato di ogni endpoint (gli endpoint disabilitati non vengono restituiti)
    - L'integrità corrente di ogni endpoint, determinata dai controlli di integrità di Gestione traffico. Per altre informazioni, vedere [Informazioni sul monitoraggio di Gestione traffico](traffic-manager-monitoring.md).
    - Il metodo di routing del traffico scelto. Per altre informazioni, vedere [Metodi di routing di Gestione traffico](traffic-manager-routing-methods.md).

5. L'endpoint scelto viene restituito come un altro record DNS CNAME. In questo caso, si supponga che venga restituito contoso-us.cloudapp.net.
6. Il servizio DNS ricorsivo cerca quindi i server dei nomi per il dominio "cloudapp.net". Il servizio contatta questi server dei nomi per richiedere il record DNS "contoso-us.cloudapp.net". Viene restituito un record DNS "A" contenente l'indirizzo IP dell'endpoint di servizio situato negli Stati Uniti.
7. Il servizio DNS ricorsivo consente di consolidare i risultati e restituisce una singola risposta DNS al client.
8. Il client riceve i risultati DNS e si connette all'indirizzo IP specificato. Il client si connette all'endpoint di servizio dell'applicazione in modo diretto, senza passare per Gestione traffico. Poiché si tratta di un endpoint HTTPS, il client esegue l'handshake SSL/TLS necessario e quindi esegue una richiesta HTTP GET per la pagina "/login.aspx".

Il servizio DNS ricorsivo memorizza nella cache le risposte DNS ricevute. Il resolver DNS nel dispositivo client memorizza nella cache anche il risultato. Il caching consente alle query DNS successive di ricevere risposte più rapide usando i dati dalla cache al posto di query ad altri server dei nomi. La durata della memorizzazione nella cache è determinata dalla proprietà "Durata" (TTL) di ogni record DNS. Valori più bassi implicano una scadenza della cache più breve e pertanto più round trip ai server dei nomi di Gestione traffico. Valori più alti indicano che potrebbe essere necessario più tempo per indirizzare il traffico da un endpoint non riuscito. Gestione traffico consente di configurare la durata (TTL) delle risposte DNS di Gestione traffico affinché siano di un minimo di 0 secondi e di un massimo di 2.147.483.647 secondi (con l'intervallo massimo conforme [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), per permettere all'utente di scegliere il valore ideale per bilanciare meglio le esigenze dell'applicazione.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere [Prezzi per Gestione traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>Domande frequenti

Per le domande frequenti su Gestione traffico, vedere le [domande frequenti su Gestione traffico](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [monitoraggio degli endpoint e sul failover automatico](traffic-manager-monitoring.md)di Gestione traffico.

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

