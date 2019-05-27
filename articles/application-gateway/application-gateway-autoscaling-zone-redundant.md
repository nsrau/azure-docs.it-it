---
title: La scalabilità automatica e il Gateway applicazione ridondanza della zona in Azure
description: Questo articolo vengono presentate le Standard_v2 dell'applicazione di Azure e WAF_v2 SKU, che include le funzionalità di scalabilità automatica e la ridondanza della zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/22/2019
ms.author: victorh
ms.openlocfilehash: 8e17c5e34ec3e2397c3054b1d0e0d97dbf410db2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65986881"
---
# <a name="autoscaling-and-zone-redundant-application-gateway"></a>La scalabilità automatica e il Gateway applicazione ridondanza della zona 

Il Gateway applicazione e Web Application Firewall (WAF) sono disponibili anche in un Standard_v2 e WAF_v2 SKU. Lo SKU v2 offre miglioramenti delle prestazioni e aggiunge il supporto per critici le nuove funzionalità quali la scalabilità automatica, ridondanza di zona e il supporto per i VIP statico. Le funzionalità esistenti in Standard Edition e lo SKU del WAF continuano a essere supportati nel nuovo SKU v2, con alcune eccezioni elencate nella [confronto](#differences-with-v1-sku) sezione.

Il nuovo SKU di versione 2 include i miglioramenti seguenti:

- **Ridimensionamento automatico**: le distribuzioni del gateway applicazione o WAF nello SKU con ridimensionamento automatico possono passare a un piano superiore o inferiore in base alle modifiche dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Questo SKU offre elasticità true. In Standard_v2 e WAF_v2 SKU, il Gateway applicazione può operare sia fisso della capacità (disabilitata la scalabilità automatica) sia in modalità di scalabilità automatica abilitata. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. Modalità la scalabilità automatica è utile nelle applicazioni che osserva varianza nel traffico dell'applicazione.
- **Ridondanza della zona**: Un Gateway applicazione o distribuzione di Web Application firewall può estendersi su più zone di disponibilità, eliminando la necessità di effettuare il provisioning di istanze del Gateway applicazione separate in ogni zona con gestione traffico. È possibile scegliere una o più zone in cui sono distribuite istanze del Gateway applicazione, che rende più resiliente a errori della zona. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.

  Ridondanza di zona è disponibile solo in cui sono disponibili aree di Azure. Nelle altre aree sono supportate tutte le altre funzionalità. Per altre informazioni, vedere [quali sono le zone di disponibilità in Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **Indirizzo VIP statico**: Applicazione gateway v2 SKU supporta l'indirizzo VIP statico il tipo in modo esclusivo. Ciò garantisce che l'indirizzo VIP associato al gateway applicazione non subisca modifiche per il ciclo di vita della distribuzione, anche dopo un riavvio.
- **Intestazione riscrittura**: Il Gateway applicazione consente di aggiungere, rimuovere o aggiornare le intestazioni della richiesta e risposta HTTP con SKU v2. Per altre informazioni, vedere [intestazioni riscrivere HTTP con il Gateway applicazione](rewrite-http-headers.md)
- **Integrazione di Key Vault (anteprima)**: Versione 2 di Gateway applicazione supporta l'integrazione con Key Vault (in anteprima pubblica) per i certificati server collegati di listener di traccia abilitato per HTTPS. Per altre informazioni, vedere [terminazione SSL con certificati Key Vault](key-vault-certs.md).
- **Controller di ingresso di Azure Kubernetes Service (anteprima)**: Il Controller di ingresso di Gateway applicazione v2 consente il Gateway applicazione di Azure da usare come il traffico in ingresso per un Azure Kubernetes Service (AKS) denominato Cluster AKS. Per altre informazioni, vedere la [pagina della documentazione](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Miglioramenti delle prestazioni**: La versione 2 SKU offre fino a 5 SSL migliore X ripartire le prestazioni rispetto allo SKU Standard o WAF.
- **Tempi di distribuzione e l'aggiornamento** lo SKU v2 offre tempi di distribuzione e l'aggiornamento più rapidi rispetto a Standard o WAF SKU. Ciò include anche le modifiche alla configurazione di Web Application firewall.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Aree supportate

Il Standard_v2 e WAF_v2 SKU è disponibile nelle aree seguenti: Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale, Asia sud-orientale, Francia centrale, Regno Unito occidentale, Giappone orientale, Giappone occidentale. In futuro verranno aggiunte altre aree.

## <a name="pricing"></a>Prezzi

Con lo SKU di versione 2, il modello di determinazione prezzi è dovuto al consumo e non è più collegato al numero totale di istanze o le dimensioni. I prezzi SKU v2 presenta due componenti:

- **Prezzo fisso** -si tratta di ogni ora (o meno di un'ora) prezzo per il provisioning di un Standard_v2 o WAF_v2 Gateway.
- **Prezzo unitario della capacità** -si tratta dei costi in base al consumo che viene addebitato oltre ai costi fissi. Addebito delle unità di capacità viene inoltre calcolato in ogni ora o parzialmente su base oraria. Sono disponibili tre dimensioni di unità di capacità - compute unit, le connessioni persistenti e velocità effettiva. Unità di calcolo è una misura della capacità del processore utilizzate. Fattori che influiscono sull'unità di calcolo sono connessioni TLS/sec, calcoli di riscrittura dell'URL e l'elaborazione delle regole di WAF. Connessione permanente è una misura di stabilite connessioni TCP per il gateway applicazione in un determinato intervallo di fatturazione. Velocità effettiva è medio megabit al secondo elaborate dal sistema in un determinato intervallo di fatturazione.

Ogni unità di capacità prevede al massimo: 1 di calcolo unità, o connessioni permanenti 2500 o velocità effettiva 2.22 Mbps.

Materiale sussidiario di unità di calcolo:

- **Standard_v2** -ogni unità di calcolo è in grado di circa 50 connessioni al secondo con certificato TLS chiave RSA a 2048 bit.
- **WAF_v2** : ogni calcolo unità può supportare circa 10 richieste simultanee al secondo per le richieste di combinazione di 70 a 30% del traffico con il 70% inferiore a 2 KB GET/POST e rimanente superiore. Le prestazioni di Web Application firewall non è interessata dalle dimensioni risposta attualmente.

> [!NOTE]
> Ogni istanza può supportare attualmente circa 10 unità di capacità.
> Il numero di richieste di che un'unità di calcolo grado di gestire dipende da diversi criteri, ad esempio dimensioni della chiave certificato TLS, algoritmo di scambio delle chiavi, l'operazione di riscrittura dell'intestazione e nel caso di dimensioni delle richieste in ingresso Web Application firewall. Si consiglia di che eseguire i test dell'applicazione per determinare la frequenza di richiesta per ogni unità di calcolo. Le unità di capacità e unità di calcolo saranno disponibili come una metrica prima dell'avvio di fatturazione.

**Prezzi della versione in Stati Uniti orientali**:

|              Nome SKU                             | Prezzo fisso ($/ h)  | Prezzo unitario della capacità ($/ unità di capacità-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Per altre informazioni sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-gateway/). La fatturazione è pianificata per iniziare il 1 ° luglio 2019.

**Esempio 1**

Standard_v2 un Gateway applicazione viene eseguito il provisioning senza scalabilità automatica in modalità di ridimensionamento manuale con capacità fissa pari a cinque istanze.

Fixed price = 744(hours) * $0.20 = $148.8 <br>
Unità di capacità = unità di capacità (ore) 10 744 per ogni istanza * cinque istanze * $0,008 all'ora di unità di capacità = $297.6

Prezzo totale = $148.8 + $297.6 = $446.4

**Esempio 2**

È stato effettuato il provisioning standard_v2 un Gateway applicazione per un mese e durante questo periodo riceve 25 nuovi SSL connessioni/sec, Media di trasferimento dei dati 8.88 Mbps. Supponendo che le connessioni hanno breve durata, il prezzo sarebbe:

Fixed price = 744(hours) * $0.20 = $148.8

Prezzo unitario capacità = 744(hours) * Max (unità di calcolo di 25 o 50 per le connessioni al secondo, unità di capacità 8.88/2.22 per la velocità effettiva) * $0,008 = 744 * 4 * 0,008 = $23.81

Prezzo totale = $148. 23.81 8 + = $172.61

**Esempio 3**

WAF_v2 un Gateway applicazione viene eseguito il provisioning per un mese. Durante questo periodo, riceve 25 nuovi SSL connessioni/sec, Media di trasferimento dei dati 8.88 Mbps e 80 richiesta al secondo. Supponendo che le connessioni hanno breve durata e che il calcolo di unità di calcolo per l'applicazione supporta RPS 10 per ogni unità di calcolo, il prezzo sarebbe:

Prezzo fisso = 744(hours) * $0.36 = $267.84

Prezzo unitario capacità = 744(hours) * Max (compute unit Max(25/50 for connections/sec, 80/10 WAF RPS), 8.88/2.22 unità di capacità per la velocità effettiva) * $0.0144 = 744 * 8 * 0.0144 = $85.71

Prezzo totale = $267.84 + $85.71 = $353.55

## <a name="scaling-application-gateway-and-waf-v2"></a>Ridimensionamento WAF e Gateway applicazione v2

È possibile configurare il Gateway applicazione e Web Application firewall per la scalabilità in due modalità:

- **La scalabilità automatica** : con la scalabilità automatica abilitata, il Gateway applicazione e v2 WAF SKU aumentalo o Riducilo in base ai requisiti di traffico dell'applicazione. Questa modalità offre una migliore elasticità all'applicazione ed elimina la necessità di indovinare il numero di istanze o dimensioni del gateway applicazione. Questa modalità consente anche di risparmiare sui costi non è necessaria per eseguire i gateway in base alla capacità di picco il provisioning per il carico di traffico massimo previsto. I clienti devono specificare un numero di istanze minimo e massimo facoltativamente. Capacità minima garantisce che il Gateway applicazione e WAF v2 non rientrano sotto il numero di istanze minimo specificato, anche in assenza di traffico. L'addebito per questa capacità minima anche in assenza di qualsiasi tipo di traffico. È anche possibile specificare un numero massimo di istanze, che assicura che il Gateway applicazione supporta la scalabilità oltre il numero di istanze specificato. Si continuerà a essere fatturata la quantità di traffico fornita dal Gateway. I conteggi delle istanze può variare da 0 a 125. Il valore predefinito per il numero massimo di istanze è pari a 20 se non specificato.
- **Manual** -in alternativa è possibile scegliere la modalità manuale in cui il gateway non sarà la scalabilità automatica. In questa modalità, se è presente più traffico che Gateway applicazione o Web Application firewall può essere gestito, potrebbero verificarsi una perdita di traffico. Con la modalità manuale, specifica il numero di istanza è obbligatorio. Numero di istanze può variare da 1 a 125 istanze.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Confronto delle funzionalità tra SKU v1 e v2 SKU

La tabella seguente confronta le funzionalità disponibili in ogni SKU.

|                                                   | v1 SKU   | SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Ridimensionamento automatico                                       |          | &#x2713; |
| Ridondanza di zona                                   |          | &#x2713; |
| Indirizzo VIP statico                                        |          | &#x2713; |
| Azure controller di Ingress di Kubernetes Service (AKS) |          | &#x2713; |
| Integrazione di Azure Key Vault                       |          | &#x2713; |
| Riscrivere le intestazioni HTTP (S)                           |          | &#x2713; |
| Routing basato su URL                                 | &#x2713; | &#x2713; |
| Hosting di più siti                             | &#x2713; | &#x2713; |
| Reindirizzamento del traffico                               | &#x2713; | &#x2713; |
| Web application firewall (WAF)                    | &#x2713; | &#x2713; |
| Terminazione di Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| Crittografia SSL end-to-end                         | &#x2713; | &#x2713; |
| Affinità di sessione                                  | &#x2713; | &#x2713; |
| Pagine di errore personalizzate                                | &#x2713; | &#x2713; |
| Supporto per WebSocket                                 | &#x2713; | &#x2713; |
| Supporto HTTP/2                                    | &#x2713; | &#x2713; |
| Svuotamento delle connessioni                               | &#x2713; | &#x2713; |

> [!NOTE]
> La versione 2 la scalabilità automatica supporta ora la SKU [probe di integrità predefinito](application-gateway-probe-overview.md#default-health-probe) per automaticamente a monitorare l'integrità di tutte le risorse nel pool back-end ed evidenziare i membri di back-end che vengano considerati non integri. Il probe di integrità predefinito viene configurato automaticamente per back-end che non hanno alcuna configurazione di probe personalizzato. Per altre informazioni, vedere [probe di integrità nel gateway applicazione](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Differenze con SKU v1

|Differenza|Dettagli|
|--|--|
|Autenticazione del certificato|Non supportati.<br>Per altre informazioni, vedere [Panoramica di SSL end-to-end con il gateway applicazione](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinazione di versione 2 Standard e gateway applicazione standard nella stessa subnet|Non supportate|
|Applicazione di una route definita dall'utente alla subnet del gateway applicazione|Non supportate|
|Gruppo di sicurezza di rete per intervallo di porte in ingresso| - Da 65200 a 65535 per SKU versione 2 Standard<br>- Da 65503 a 65534 per SKU Standard.<br>Per altre informazioni, vedere la sezione [Domande frequenti](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Contatori delle prestazioni in Diagnostica di Azure|Non supportati.<br>Usare le metriche di Azure.|
|Fatturazione|Fatturazione pianificata l'avvio del 1 ° luglio 2019.|
|Modalità FIPS|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Integrazione di Netwatcher|Non supportati.|
|Integrazione del centro di supporto di Azure|Non ancora disponibile.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Traffico web diretto con il Gateway applicazione di Azure - portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione con scalabilità automatica e ridondanza della zona con un indirizzo IP virtuale riservato tramite Azure PowerShell](tutorial-autoscale-ps.md)
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md).