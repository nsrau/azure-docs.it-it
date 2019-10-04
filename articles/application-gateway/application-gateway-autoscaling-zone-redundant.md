---
title: Scalabilità automatica e gateway applicazione con ridondanza della zona V2
description: Questo articolo presenta lo SKU applicazione Azure Standard_v2 e WAF_v2, che include la scalabilità automatica e le funzionalità con ridondanza della zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: b97dab0f41915ac6193c35cad9a6af812b16fd4a
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104878"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Scalabilità automatica e gateway applicazione con ridondanza della zona V2 

Il gateway applicazione e il Web Application Firewall (WAF) sono disponibili anche in uno SKU Standard_v2 e WAF_v2. Lo SKU V2 offre miglioramenti delle prestazioni e aggiunge il supporto per le nuove funzionalità critiche, come la scalabilità automatica, la ridondanza della zona e il supporto per gli indirizzi VIP statici. Le funzionalità esistenti con lo SKU standard e WAF continuano a essere supportate nella nuova SKU V2, con alcune eccezioni elencate nella sezione di [confronto](#differences-with-v1-sku) .

Il nuovo SKU v2 include i miglioramenti seguenti:

- **Ridimensionamento automatico**: le distribuzioni del gateway applicazione o WAF nello SKU con ridimensionamento automatico possono passare a un piano superiore o inferiore in base alle modifiche dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Questo SKU offre una vera elasticità. Nello SKU Standard_v2 e WAF_v2 il gateway applicazione può funzionare sia con la capacità fissa (scalabilità automatica disabilitata) sia con la modalità abilitata per la scalabilità automatica. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. La modalità di scalabilità automatica è utile nelle applicazioni che visualizzano la varianza nel traffico delle applicazioni.
- **Ridondanza della zona**: Un gateway applicazione o una distribuzione WAF può estendersi su più zone di disponibilità, eliminando la necessità di eseguire il provisioning di istanze separate del gateway applicazione in ogni zona con gestione traffico. È possibile scegliere una singola zona o più zone in cui vengono distribuite le istanze del gateway applicazione, in modo da renderla più resiliente agli errori di zona. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.

  La ridondanza della zona è disponibile solo se sono disponibili zone di Azure. In altre aree sono supportate tutte le altre funzionalità. Per altre informazioni, vedere [Informazioni sulle zone di disponibilità di Azure](../availability-zones/az-overview.md#services-support-by-region)
- **Indirizzo VIP statico**: Lo SKU del gateway applicazione V2 supporta il tipo di indirizzo VIP statico in modo esclusivo. In questo modo si assicura che l'indirizzo VIP associato al gateway applicazione non cambi per il ciclo di vita della distribuzione, anche dopo un riavvio.  Non esiste un indirizzo VIP statico nella versione 1, quindi è necessario usare l'URL del gateway applicazione anziché l'indirizzo IP per il routing del nome di dominio ai servizi app tramite il gateway applicazione.
- **Riscrittura intestazione**: Il gateway applicazione consente di aggiungere, rimuovere o aggiornare intestazioni di richiesta e risposta HTTP con lo SKU V2. Per altre informazioni, vedere [riscrivere le intestazioni HTTP con il gateway applicazione](rewrite-http-headers.md)
- **Integrazione di Key Vault (anteprima)** : Il gateway applicazione V2 supporta l'integrazione con Key Vault (in anteprima pubblica) per i certificati server collegati ai listener abilitati per HTTPS. Per ulteriori informazioni, vedere [terminazione SSL con Key Vault Certificates](key-vault-certs.md).
- **Controller di ingresso del servizio Azure Kubernetes (anteprima)** : Il controller di ingresso del gateway applicazione v2 consente l'uso del gateway applicazione Azure come ingresso per un servizio Azure Kubernetes (AKS) noto come cluster AKS. Per ulteriori informazioni, vedere la [pagina della documentazione](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Miglioramenti delle prestazioni**: Lo SKU V2 offre prestazioni di offload SSL migliori a 5 volte rispetto allo SKU standard/WAF.
- **Tempo di distribuzione e aggiornamento più rapido** Lo SKU V2 offre tempi di distribuzione e aggiornamento più veloci rispetto allo SKU standard/WAF. Sono incluse anche le modifiche della configurazione di WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Aree supportate

Lo SKU Standard_v2 e WAF_v2 è disponibile nelle aree geografiche seguenti: Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale, Europa occidentale, Asia sudorientale, Francia centrale, Regno Unito occidentale, Giappone orientale, Giappone occidentale, Australia orientale, Australia sudorientale, Canada centrale, Canada orientale, Asia orientale, Corea Centrale, Corea meridionale, India meridionale, Regno Unito meridionale, India centrale, India occidentale, India meridionale.

## <a name="pricing"></a>Prezzi

Con lo SKU V2, il modello di determinazione dei prezzi è determinato dal consumo e non è più associato ai conteggi o alle dimensioni delle istanze. I prezzi dello SKU V2 hanno due componenti:

- **Prezzo fisso** : prezzo orario (o ora parziale) per il provisioning di un gateway Standard_v2 o WAF_v2.
- **Prezzo unitario di capacità** : costo in base al consumo addebitato in aggiunta al costo fisso. Gli addebiti relativi alle unità di capacità vengono calcolati anche su base oraria o parzialmente oraria. Sono disponibili tre dimensioni per le unità di capacità, ovvero unità di calcolo, connessioni persistenti e unità elaborate. L'unità di calcolo è una misura relativa alla capacità di processore utilizzata. I fattori che influiscono sull'unità di calcolo sono connessioni TLS/sec, calcoli di riscrittura URL e elaborazione di regole WAF. La connessione persistente è una misura delle connessioni TCP stabilite al gateway applicazione in un determinato intervallo di fatturazione. La velocità effettiva è media megabit/sec elaborati dal sistema in un determinato intervallo di fatturazione.

Ogni unità di capacità è costituita al massimo da: 1 unità di calcolo o 2500 connessioni permanenti o velocità effettiva di 2,22 Mbps.

Linee guida per le unità di calcolo:

- **Standard_v2** : ogni unità di calcolo è in grado di raggiungere circa 50 connessioni al secondo con il certificato TLS della chiave RSA a 2048 bit.
- **WAF_v2** : ogni unità di calcolo può supportare circa 10 richieste simultanee al secondo per la combinazione di traffico 70-30% con richieste 70% inferiori a 2 KB get/post e rimanenti superiori. Le prestazioni di WAF non sono attualmente interessate dalle dimensioni della risposta.

> [!NOTE]
> Ogni istanza può attualmente supportare circa 10 unità di capacità.
> Il numero di richieste che possono essere gestite da un'unità di calcolo dipende da diversi criteri, ad esempio le dimensioni della chiave del certificato TLS, l'algoritmo di scambio delle chiavi, le riscritture delle intestazioni e in caso di dimensioni della richiesta in ingresso WAF. Si consiglia di eseguire test dell'applicazione per determinare la frequenza delle richieste per ogni unità di calcolo. L'unità di capacità e l'unità di calcolo saranno rese disponibili come metrica prima dell'avvio della fatturazione.

Nella tabella seguente vengono illustrati i prezzi di esempio e sono solo a scopo illustrativo.

**Prezzi negli Stati Uniti orientali**:

|              Nome SKU                             | Prezzo fisso ($/ora)  | Prezzo unitario di capacità ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Per ulteriori informazioni sui prezzi, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/). La fatturazione è prevista per l'inizio il 1 ° luglio 2019.

**Esempio 1**

Viene eseguito il provisioning di un Standard_v2 del gateway applicazione senza scalabilità automatica in modalità di ridimensionamento manuale con capacità fissa di cinque istanze.

Prezzo fisso = 744 (ore) * $0,20 = $148,8 <br>
Unità di capacità = 744 (ore) * 10 unità di capacità per istanza * 5 istanze * $0,008 per ora unità di capacità = $297,6

Prezzo totale = $148,8 + $297,6 = $446,4

**Esempio 2**

Viene eseguito il provisioning di un standard_v2 del gateway applicazione per un mese e durante questo periodo vengono ricevute 25 nuove connessioni SSL/sec, ovvero la media del trasferimento dati 8,88 Mbps. Supponendo che le connessioni siano di breve durata, il prezzo sarà:

Prezzo fisso = 744 (ore) * $0,20 = $148,8

Unità di capacità prezzo = 744 (ore) * Max (25/50 unità di calcolo per le connessioni/sec, unità di capacità 8.88/2.22 per la velocità effettiva) * $0,008 = 744 * 4 * 0,008 = $23,81

Prezzo totale = $148.8 + 23.81 = $172,61

> [!NOTE]
> La funzione max restituisce il valore più grande in una coppia di valori.

**Esempio 3**

Viene eseguito il provisioning di un WAF_v2 del gateway applicazione per un mese. Durante questo periodo, riceve 25 nuove connessioni SSL/sec, la media del trasferimento dati 8,88 Mbps e la richiesta 80 al secondo. Supponendo che le connessioni siano di breve durata e che il calcolo dell'unità di calcolo per l'applicazione supporti 10 RPS per ogni unità di calcolo, il prezzo sarà:

Prezzo fisso = 744 (ore) * $0,36 = $267,84

Prezzo unitario di capacità = 744 (ore) * Max (unità di calcolo massima (25/50 per le connessioni/sec, 80/10 RPS WAF), unità di capacità 8.88/2.22 per la velocità effettiva) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Prezzo totale = $267,84 + $85,71 = $353,55

> [!NOTE]
> La funzione max restituisce il valore più grande in una coppia di valori.

## <a name="scaling-application-gateway-and-waf-v2"></a>Ridimensionamento del gateway applicazione e WAF V2

Il gateway applicazione e WAF possono essere configurati per la scalabilità in due modalità:

- **Scalabilità** automatica: con la scalabilità automatica abilitata, il gateway applicazione e gli SKU WAF V2 aumentano o riducono in base ai requisiti del traffico delle applicazioni. Questa modalità offre una migliore elasticità all'applicazione ed elimina la necessità di indovinare la dimensione o il numero di istanze del gateway applicazione. Questa modalità consente anche di risparmiare sui costi senza richiedere che il gateway venga eseguito al massimo della capacità con provisioning per il carico di traffico massimo previsto. È necessario specificare un numero minimo e facoltativo massimo di istanze. La capacità minima garantisce che il gateway applicazione e WAF V2 non scendano al di sotto del numero minimo di istanze specificato, anche in assenza di traffico. Ogni istanza viene conteggiata come 10 unità di capacità riservate aggiuntive. 0 indica che non è disponibile alcuna capacità riservata ed è esclusivamente la scalabilità automatica. Si noti che 0 istanze aggiuntive minime garantiscono comunque una disponibilità elevata del servizio sempre incluso con prezzo fisso. Facoltativamente, è anche possibile specificare un numero massimo di istanze, che garantisce che il gateway applicazione non venga ridimensionato oltre il numero specificato di istanze. Continuerà a essere addebitata la quantità di traffico servito dal gateway. Il numero di istanze può essere compreso tra 0 e 125. Il valore predefinito per il numero massimo di istanze è 20 se non è specificato. 
- **Manuale** : in alternativa, è possibile scegliere la modalità manuale in cui il gateway non esegue la scalabilità automatica. In questa modalità, se è presente più traffico rispetto a quello che può essere gestito dal gateway applicazione o da WAF, potrebbe verificarsi una perdita di traffico. Con la modalità manuale, specificare il numero di istanze è obbligatorio. Il numero di istanze può variare da 1 a 125.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Confronto tra le funzionalità dello SKU V1 e dello SKU V2

Nella tabella seguente vengono confrontate le funzionalità disponibili in ogni SKU.

|                                                   | SKU V1   | SKU V2   |
| ------------------------------------------------- | -------- | -------- |
| Ridimensionamento automatico                                       |          | &#x2713; |
| Ridondanza della zona                                   |          | &#x2713; |
| Indirizzo VIP statico                                        |          | &#x2713; |
| Controller di ingresso del servizio Azure Kubernetes (AKS) |          | &#x2713; |
| Integrazione di Azure Key Vault                       |          | &#x2713; |
| Riscrivi intestazioni HTTP (S)                           |          | &#x2713; |
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
> Lo SKU di scalabilità automatica V2 supporta ora i [Probe di integrità predefiniti](application-gateway-probe-overview.md#default-health-probe) per monitorare automaticamente l'integrità di tutte le risorse nel pool back-end ed evidenziare i membri back-end considerati non integri. Il probe di integrità predefinito viene configurato automaticamente per i backend che non dispongono di alcuna configurazione Probe personalizzata. Per altre informazioni, vedere [Probe di integrità nel gateway applicazione](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Differenze con lo SKU V1

|Differenza|Dettagli|
|--|--|
|Autenticazione del certificato|Non supportati.<br>Per altre informazioni, vedere [Panoramica di SSL end-to-end con il gateway applicazione](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinazione di versione 2 Standard e gateway applicazione standard nella stessa subnet|Non supportate|
|Applicazione di una route definita dall'utente alla subnet del gateway applicazione|Non supportate|
|Gruppo di sicurezza di rete per intervallo di porte in ingresso| - Da 65200 a 65535 per SKU versione 2 Standard<br>- Da 65503 a 65534 per SKU Standard.<br>Per altre informazioni, vedere la sezione [Domande frequenti](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Contatori delle prestazioni in Diagnostica di Azure|Non supportati.<br>Usare le metriche di Azure.|
|Fatturazione|Fatturazione pianificata per l'avvio il 1 ° luglio 2019.|
|Modalità FIPS|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Integrazione di Netwatcher|Non supportati.|
|Integrazione del Centro sicurezza di Azure|Non ancora disponibile.

## <a name="migrate-from-v1-to-v2"></a>Eseguire la migrazione dalla versione 1 alla versione 2

In PowerShell Gallery è disponibile uno script Azure PowerShell che consente di eseguire la migrazione dal gateway applicazione V1/WAF allo SKU di scalabilità automatica V2. Questo script consente di copiare la configurazione dal gateway V1. La migrazione del traffico è ancora responsabilità dell'utente. Per altre informazioni, vedere [eseguire la migrazione di applicazione Azure gateway da V1 a V2](migrate-v1-v2.md).

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Indirizzare il traffico Web con il gateway applicazione di Azure - Portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione con scalabilità automatica e ridondanza della zona con un indirizzo IP virtuale riservato tramite Azure PowerShell](tutorial-autoscale-ps.md)
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md).
