---
title: Gateway applicazione con scalabilità automatica e ridondanza della zona versione 2
description: Questo articolo presenta lo SKU dell'applicazione Azure Standard_v2 e WAF_v2, che include funzionalità di scalabilità automatica e di ridondanza della zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 8c989e426faa77025a84515fe0a19424cb6bfa89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826633"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Gateway applicazione con scalabilità automatica e ridondanza della zona versione 2 

Il gateway applicazione è disponibile in uno SKU Standard_v2. Il Web Application Firewall (WAF) è disponibile in uno SKU WAF_v2. Lo SKU V2 offre miglioramenti alle prestazioni e aggiunge il supporto per nuove importanti funzionalità quali scalabilità automatica, ridondanza della zona e supporto per indirizzi VIP statici. Le funzionalità disponibili nello SKU Standard e WAF continuano a essere supportate anche nel nuovo SKU v2, con alcune eccezioni elencate nella sezione di [confronto](#differences-from-v1-sku).

Il nuovo SKU v2 include i miglioramenti seguenti:

- **Ridimensionamento automatico**: le distribuzioni del gateway applicazione o WAF con lo SKU di scalabilità automatica possono aumentare o ridurre le prestazioni in base alla modifica dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Lo SKU offre una reale elasticità. In Standard_v2 e WAF_v2 SKU, il gateway applicazione può operare sia in modalità di capacità fissa (ridimensionamento automatico disabilitato) che di ridimensionamento automatico abilitato. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. La modalità di scalabilità automatica è vantaggiosa nelle applicazioni con traffico delle applicazioni soggetto a varianze.
- **Ridondanza della zona**: una distribuzione del gateway applicazione o WAF può estendersi a più zone di disponibilità, eliminando la necessità di effettuare il provisioning di istanze del gateway applicazione separate in ogni zona con un'utilità di Gestione traffico. È possibile scegliere una o più zone in cui distribuire le istanze del gateway applicazione, aumentando così la resilienza rispetto agli errori di zona. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.

  La ridondanza della zona è disponibile solo dove sono disponibili zone di Azure. In altre aree sono supportate tutte le altre funzionalità. Per altre informazioni, vedere [Aree e zone di disponibilità](../availability-zones/az-overview.md)
- **Indirizzo VIP statico**: lo SKU v2 del gateway applicazione supporta esclusivamente il tipo di indirizzo VIP statico. Questa funzionalità garantisce che l'indirizzo VIP associato al gateway applicazione non cambi nel corso del ciclo di vita della distribuzione, anche dopo un riavvio.  Nella versione 1 non è presente un indirizzo VIP statico ed è quindi necessario usare l'URL del gateway applicazione, anziché l'indirizzo IP, per eseguire il routing del nome di dominio a Servizi app tramite il gateway applicazione.
- **Riscrittura dell'intestazione**: il gateway applicazione consente di aggiungere, rimuovere o aggiornare intestazioni di richiesta e risposta HTTP con lo SKU v2. Per altre informazioni, vedere [Riscrivere le intestazioni HTTP con il gateway applicazione ](rewrite-http-headers.md).
- **Integrazione di Key Vault**: lo SKU v2 del gateway applicazione supporta l'integrazione con Key Vault per i certificati server associati a listener abilitati per HTTPS. Per altre informazioni, vedere [Terminazione TLS con certificati di Key Vault](key-vault-certs.md).
- **Controller di ingresso del servizio Azure Kubernetes**: il controller di ingresso dello SKU v2 del gateway applicazione consente di usare il gateway applicazione Azure come ingresso per un servizio Azure Kubernetes, chiamato anche cluster AKS. Per altre informazioni, vedere [Controller di ingresso del gateway applicazione di Azure](ingress-controller-overview.md).
- **Miglioramenti delle prestazioni**: lo SKU v2 offre prestazioni di offload TLS 5 volte superiori rispetto allo SKU Standard/WAF.
- **Tempi di distribuzione e di aggiornamento più veloci**: lo SKU v2 offre tempi di distribuzione e di aggiornamento più veloci rispetto allo SKU Standard/WAF. Include anche modifiche alla configurazione di WAF.

![Diagramma della zona di scalabilità automatica.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Aree supportate

Lo SKU Standard_v2 e WAF_v2 è disponibile nelle aree seguenti: Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale, Asia sudorientale, Francia centrale, Regno Unito occidentale, Giappone orientale, Giappone occidentale, Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Asia orientale, Corea centrale, Corea meridionale, Regno Unito meridionale, India centrale, India occidentale, India meridionale.

## <a name="pricing"></a>Prezzi

Con lo SKU v2, il modello dei prezzi è associato al consumo e non più al numero o alle dimensioni delle istanze. I prezzi dello SKU v2 si articolano in due componenti:

- **Prezzo fisso**: prezzo orario (o per parte di ora) per il provisioning di un gateway Standard_v2 o WAF_v2. Si noti che un numero minimo di istanze aggiuntive pari a 0 garantisce comunque una disponibilità elevata del servizio, sempre inclusa nel prezzo fisso.
- **Prezzo per unità di capacità**: costo in base al consumo addebitato in aggiunta al costo fisso. L'addebito per unità di capacità viene calcolato anche a livello di ora o di parte di ora. Esistono tre dimensioni per le unità di capacità: l'unità di calcolo, le connessioni correnti e la velocità effettiva. L'unità di calcolo è una misura della capacità del processore usata. I fattori che influiscono sull'unità di calcolo sono il numero di connessioni TLS/sec, i calcoli di URL Rewrite e l'elaborazione di regole di WAF. La connessione corrente è una misura delle connessioni TCP stabilite al gateway applicazione in un determinato intervallo di fatturazione. La velocità effettiva è la media di Megabit/sec elaborati dal sistema in un determinato intervallo di fatturazione.  La fatturazione viene effettuata a livello di unità di capacità per qualsiasi valore superiore al numero di istanze riservate.

Ogni unità di capacità è composta al massimo: 1 unità di calcolo, 2500 connessioni permanenti e velocità effettiva di 2,22 Mbps.

Per altre informazioni, vedere [informazioni sui prezzi](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>Scalabilità del gateway applicazione e WAF v2

La scalabilità del gateway applicazione e di WAF può essere configurata nelle due modalità seguenti:

- **Scalabilità automatica**: con la scalabilità automatica abilitata, gli SKU v2 del gateway applicazione e di WAF possono essere aumentati o diminuiti in base ai requisiti del traffico dell'applicazione. Questa modalità offre una migliore elasticità all'applicazione ed elimina la necessità di indovinare le dimensioni o il numero di istanze del gateway applicazione. Consente inoltre di ridurre i costi evitando che il gateway venga eseguito al massimo della capacità con provisioning per il carico di traffico massimo previsto. È necessario specificare il numero minimo e, facoltativamente, il numero massimo di istanze. La capacità minima garantisce che il gateway applicazione e WAF v2 non scendano al di sotto del numero minimo di istanze specificato, anche in assenza di traffico. Ogni istanza corrisponde approssimativamente a 10 unità di capacità riservata aggiuntive. "Zero" indica che non è disponibile alcuna capacità riservata ed è prevista esclusivamente la scalabilità automatica. Facoltativamente, è possibile specificare anche un numero massimo di istanze, in modo da garantire che il gateway applicazione non venga aumentato oltre il numero specificato di istanze. Verrà addebitata solo la quantità di traffico gestito dal gateway. Il numero di istanze può essere compreso tra 0 e 125. Se non viene specificato, si userà il valore predefinito per il numero massimo di istanze, pari a 20.
- **Manuale**: in alternativa, è possibile scegliere la modalità manuale, in cui viene esclusa la scalabilità automatica per il gateway. In questa modalità, se si genera più traffico di quanto possa essere gestito dal gateway applicazione o da WAF, potrebbe verificarsi una perdita di traffico. Con la modalità manuale, è obbligatorio specificare il numero di istanze. Il numero di istanze deve essere compreso tra 1 e 125.

## <a name="autoscaling-and-high-availability"></a>Scalabilità automatica e disponibilità elevata

I gateway applicazione Azure vengono sempre distribuiti in una modalità a disponibilità elevata. Il servizio è costituito da più istanze create in base al tipo di configurazione (se la scalabilità automatica è abilitata) o al carico applicativo (se la scalabilità automatica è disabilitata). L'utente non avrà necessariamente la visibilità delle singole istanze, ma solo del gateway applicazione come servizio nel suo complesso. Se in un'istanza si verifica un problema e smette di funzionare, il gateway applicazione Azure creerà una nuova istanza in modo trasparente.

Tenere presente che, anche se si configura la scalabilità automatica con zero istanze minime, il servizio continuerà a essere a disponibilità elevata, sempre inclusa nel prezzo fisso.

La creazione di una nuova istanza, tuttavia, può richiedere sei o sette minuti. Per evitare questo tempo di inattività, quindi, è possibile configurare un numero minimo di istanze pari a 2, idealmente con il supporto per la zona di disponibilità. In questo modo, in circostanze normali nel gateway applicazione Azure saranno sempre disponibili almeno due istanze e, se in una di essi si verifica un problema, l'altra tenterà di gestire tutto il traffico, concedendo il tempo necessario per creare una nuova istanza. Un'istanza del gateway applicazione Azure può supportare circa 10 unità di capacità; pertanto, a seconda della quantità di traffico normalmente presente, potrebbe essere necessario impostare la scalabilità automatica del numero minimo di istanze su un valore superiore a 2.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Confronto tra le funzionalità di SKU v1 e SKU v2

La tabella seguente confronta le funzionalità disponibili con ogni SKU.

| Funzionalità                                           | SKU v1   | SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Scalabilità automatica                                       |          | &#x2713; |
| Ridondanza della zona                                   |          | &#x2713; |
| Indirizzo VIP statico                                        |          | &#x2713; |
| Controller di ingresso del servizio Azure Kubernetes |          | &#x2713; |
| Integrazione dell'insieme di credenziali delle chiavi di Azure                       |          | &#x2713; |
| Riscrittura delle intestazioni HTTP(S)                           |          | &#x2713; |
| Routing basato su URL                                 | &#x2713; | &#x2713; |
| Hosting di più siti                             | &#x2713; | &#x2713; |
| Reindirizzamento del traffico                               | &#x2713; | &#x2713; |
| WAF (Web application firewall)                    | &#x2713; | &#x2713; |
| Regole personalizzate di WAF                                  |          | &#x2713; |
| Terminazione TLS (Transport Layer Security)/SSL (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| Crittografia TLS end-to-end                         | &#x2713; | &#x2713; |
| Affinità di sessione                                  | &#x2713; | &#x2713; |
| Pagine di errore personalizzate                                | &#x2713; | &#x2713; |
| Supporto per WebSocket                                 | &#x2713; | &#x2713; |
| Supporto HTTP/2                                    | &#x2713; | &#x2713; |
| Esaurimento delle connessioni                               | &#x2713; | &#x2713; |

> [!NOTE]
> Lo SKU v2 con scalabilità automatica supporta ora [probe di integrità predefiniti](application-gateway-probe-overview.md#default-health-probe) per monitorare l'integrità di tutte le risorse all'interno del pool back-end ed evidenziare i membri back-end considerati non integri. Il probe di integrità predefinito viene configurato automaticamente per i backend in cui non sono configurati probe personalizzati. Per altre informazioni, vedere [Probe di integrità nel gateway applicazione](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>Differenze rispetto a SKU v1

Questa sezione descrive le funzionalità e le limitazioni dello SKU v2 che lo differenziano dallo SKU v1.

|Differenza|Dettagli|
|--|--|
|Autenticazione del certificato|Non supportato.<br>Per altre informazioni, vedere [Panoramica di TLS end-to-end con il gateway applicazione](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Combinazione di versione 2 Standard e gateway applicazione standard nella stessa subnet|Non supportate|
|Applicazione di una route definita dall'utente alla subnet del gateway applicazione|Supportata (scenari specifici). In anteprima.<br> Per altre informazioni sugli scenari supportati, vedere [Panoramica della configurazione del gateway applicazione](configuration-infrastructure.md#supported-user-defined-routes).|
|Gruppo di sicurezza di rete per intervallo di porte in ingresso| - Da 65200 a 65535 per SKU versione 2 Standard<br>- Da 65503 a 65534 per SKU Standard.<br>Per altre informazioni, vedere la sezione [Domande frequenti](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Contatori delle prestazioni in Diagnostica di Azure|Non supportato.<br>Usare le metriche di Azure.|
|Fatturazione|Inizio della fatturazione pianificato per il 1° luglio 2019.|
|Modalità FIPS|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Integrazione di Net Watcher|Non supportato.|
|Integrazione nel Centro sicurezza di Azure|Non ancora disponibile.

## <a name="migrate-from-v1-to-v2"></a>Eseguire la migrazione dalla versione 1 alla versione 2

In PowerShell Gallery è disponibile uno script di Azure PowerShell che semplifica la migrazione dallo SKU v1 del gateway applicazione /WAF allo SKU v2 con scalabilità automatica. Lo script consente di copiare la configurazione dal gateway v1. La migrazione del traffico rimane di responsabilità dell'utente. Per altre informazioni, vedere [Eseguire la migrazione del gateway applicazione Azure da v1 a v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Indirizzare il traffico Web con il gateway applicazione di Azure - Portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione con scalabilità automatica e ridondanza della zona con un indirizzo IP virtuale riservato tramite Azure PowerShell](tutorial-autoscale-ps.md)
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md).
