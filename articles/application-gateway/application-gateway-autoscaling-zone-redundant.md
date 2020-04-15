---
title: Gateway applicazione con scalabilità automatica e ridondanza della zona versione 2
description: Questo articolo introduce il Standard_v2 dell'applicazione Azure e WAF_v2 SKU, che include la scalabilità automatica e le funzionalità con ridondanza di zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 7feb0f00c5431048d19d4ad6cb3860f6eb8ed052
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312713"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Gateway applicazione con scalabilità automatica e ridondanza della zona versione 2 

Il gateway applicazione e il Web Application Firewall (WAF) sono disponibili anche in uno SKU Standard_v2 e WAF_v2. Lo SKU v2 offre miglioramenti delle prestazioni e aggiunge il supporto per le nuove funzionalità critiche, ad esempio la scalabilità automatica, la ridondanza delle zone e il supporto per gli indirizzi VIP statici. Le funzionalità esistenti nello SKU Standard e WAF continuano a essere supportate nel nuovo SKU v2, con alcune eccezioni elencate nella sezione [di confronto.](#differences-with-v1-sku)

Il nuovo SKU v2 include i seguenti miglioramenti:

- **Scalabilità automatica**: le distribuzioni del gateway applicazione o WAF nella SKU con scalabilità automatica possono passare a un piano superiore o inferiore in base alle modifiche dei modelli di carico del traffico. La scalabilità automatica elimina anche la necessità di scegliere un numero di istanze o le dimensioni della distribuzione durante il provisioning. Questo SKU offre una vera elasticità. Nello SKU Standard_v2 e WAF_v2, il gateway applicazione può funzionare sia in capacità fissa (abilitata di scalabilità automatica) che in modalità abilitata per la scalabilità automatica. La modalità di capacità fissa è utile per gli scenari con carichi di lavoro coerenti e prevedibili. La modalità di scalabilità automatica è utile nelle applicazioni che visualizzano la varianza nel traffico delle applicazioni.
- **Ridondanza della zona:** una distribuzione di un gateway applicazione o WAF può estendersi su più zone di disponibilità, eliminando la necessità di eseguire il provisioning di istanze separate del gateway applicazione in ogni zona con Gestione traffico. È possibile scegliere una singola zona o più zone in cui vengono distribuite le istanze del gateway applicazione, in modo da renderlo più resiliente agli errori di zona. Il pool di back-end per le applicazioni può analogamente essere distribuito tra zone di disponibilità.

  La ridondanza di zona è disponibile solo dove sono disponibili zone di Azure.zone redundancy is available only where Azure zones are available. In altre aree, sono supportate tutte le altre funzionalità. Per altre informazioni, vedere Che cosa sono le zone di [disponibilità in Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP statico:** lo SKU del gateway applicazione v2 supporta esclusivamente il tipo VIP statico. In questo modo si garantisce che l'indirizzo VIP associato al gateway applicazione non venga modificato per il ciclo di vita della distribuzione, anche dopo un riavvio.  Non esiste un indirizzo VIP statico nella versione 1, pertanto è necessario usare l'URL del gateway applicazione anziché l'indirizzo IP per il routing dei nomi di dominio ai servizi app tramite il gateway applicazione.
- **Riscrittura intestazione:** il gateway applicazione consente di aggiungere, rimuovere o aggiornare le intestazioni di richiesta e risposta HTTP con SKU v2. Per altre informazioni, vedere Riscrivere le intestazioni HTTP con il gateway applicazioneFor more information, see [Rewrite HTTP headers with Application Gateway](rewrite-http-headers.md)
- **Integrazione dell'insieme**di credenziali delle chiavi: il gateway applicazione v2 supporta l'integrazione con Key Vault per i certificati server collegati ai listener abilitati per HTTPS. Per ulteriori informazioni, vedere [Terminazione TLS con certificati dell'insieme](key-vault-certs.md)di credenziali delle chiavi .
- Controller di ingresso del servizio **Azure Kubernetes:** il controller di ingresso del gateway applicazione v2 consente di usare il gateway applicazione di Azure come ingresso per un servizio Azure Kubernetes (AKS) noto come cluster AKS. Per ulteriori informazioni, vedere [Che cos'è il controller](ingress-controller-overview.md)di ingresso del gateway applicazione? .
- **Miglioramenti delle prestazioni**: lo SKU v2 offre fino a 5 volte migliori prestazioni di offload TLS rispetto allo SKU Standard/WAF.
- **Tempi di distribuzione e aggiornamento più rapidi** Lo SKU v2 fornisce tempi di distribuzione e aggiornamento più rapidi rispetto allo SKU Standard/WAF. Sono incluse anche le modifiche alla configurazione WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Aree supportate

Il Standard_v2 e WAF_v2 SKU è disponibile nelle seguenti regioni: Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Stati Uniti occidentali, Stati Uniti occidentali 2, Stati Uniti orientali, Stati Uniti orientali, Stati Uniti centrali, Europa settentrionale, Europa occidentale, Asia sud-orientale, Francia centrale, Regno Unito, Giappone orientale, Giappone ovest, Australia orientale, Australia sud-orientale, Brasile, Canada centrale, Canada orientale, Asia orientale, Corea centrale, Corea del Sud , Regno Unito Meridionale, India centrale, India occidentale, India meridionale.

## <a name="pricing"></a>Prezzi

Con lo SKU v2, il modello di determinazione dei prezzi è guidato dal consumo e non è più collegato ai conteggi o alle dimensioni delle istanze. Il prezzo dello SKU v2 è due componenti:

- **Prezzo fisso:** si tratta di un prezzo orario (o orario parziale) per il provisioning di un gateway Standard_v2 o WAF_v2. Si prega di notare che 0 istanze minime aggiuntive garantisce ancora un'elevata disponibilità del servizio che è sempre incluso con prezzo fisso.
- **Prezzo unitario capacità:** si tratta di un costo basato sul consumo che viene addebitato in aggiunta al costo fisso. Anche la carica dell'unità di capacità viene calcolata ogni ora o parzialmente. Esistono tre dimensioni per unità di capacità: unità di calcolo, connessioni permanenti e velocità effettiva. L'unità di calcolo è una misura della capacità del processore consumata. I fattori che influiscono sull'unità di calcolo sono le connessioni TLS al secondo, i calcoli di riscrittura URL e l'elaborazione delle regole WAF. La connessione permanente è una misura delle connessioni TCP stabilite al gateway applicazione in un determinato intervallo di fatturazione. La velocità effettiva è media Megabits/sec elaborata dal sistema in un determinato intervallo di fatturazione.  La fatturazione viene eseguita a livello di unità di capacità per qualsiasi elemento al di sopra del numero di istanze riservate.

Ogni unità di capacità è composta al massimo da: 1 unità di calcolo, o 2500 connessioni persistenti o velocità effettiva 2,22 Mbps.

Guida all'unità di calcolo:

- **Standard_v2-** Ogni unità di calcolo è in grado di circa 50 connessioni al secondo con certificato TLS chiave RSA 2048 bit.
- **WAF_v2-** Ogni unità di calcolo può supportare circa 10 richieste simultanee al secondo per una combinazione di traffico 70-30% con richieste del 70% inferiori a 2 KB GET/POST e rimanendo superiore. Le prestazioni WAF non sono attualmente influenzate dalle dimensioni della risposta.

> [!NOTE]
> Ogni istanza può attualmente supportare circa 10 unità di capacità.
> Il numero di richieste che un'unità di calcolo può gestire dipende da vari criteri, ad esempio la dimensione della chiave del certificato TLS, l'algoritmo di scambio delle chiavi, le riscritture di intestazione e in caso di dimensioni della richiesta in ingresso WAF. È consigliabile eseguire test dell'applicazione per determinare la frequenza delle richieste per unità di calcolo. Sia l'unità di capacità che l'unità di calcolo verranno rese disponibili come metrica prima dell'inizio della fatturazione.

La tabella seguente mostra i prezzi di esempio e sono solo a scopo illustrativo.

**Prezzi negli Stati Uniti orientali**:

|              Nome SKU                             | Prezzo fisso (z/ora)  | Prezzo unitario capacità (z/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Per ulteriori informazioni sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-gateway/). 

**Esempio 1**

Viene eseguito il provisioning di un Standard_v2 del gateway applicazione senza scalabilità automatica in modalità di ridimensionamento manuale con capacità fissa di cinque istanze.

Prezzo fisso: 744 (ore) - 0,20 USD - 148,8 USD <br>
Unità di capacità: 744 (ore) pari a 10 unità di capacità per istanza, 5 istanze, 0,008 USD per unità di unità di capacità, 297,6 USD

Prezzo totale: 148,8 USD, 297,6 USD, 446,4 USD

**Esempio 2**

Viene eseguito il provisioning di un gateway applicazione standard_v2 per un mese, con zero istanze minime, e durante questo periodo riceve 25 nuove connessioni TLS/sec, in media il trasferimento di dati a 8,88 Mbps. Supponendo che le connessioni siano di breve durata, il prezzo sarebbe:

Prezzo fisso: 744 (ore) - 0,20 USD - 148,8 USD

Prezzo unitario di capacità: 744(ore) - Max (25/50 unità di calcolo per le connessioni/sec, 8,88/2,22 unità di capacità per la velocità effettiva)

Prezzo totale: 148,8 USD 23,81 USD e 172,61 USD

Come si può vedere, vengono fatturate solo quattro unità di capacità, non per l'intera istanza. 

> [!NOTE]
> La funzione Max restituisce il valore più grande in una coppia di valori.


**Esempio 3**

Viene eseguito il provisioning di un standard_v2 del gateway applicazione per un mese, con un minimo di cinque istanze. Supponendo che non ci sia traffico e che le connessioni siano di breve durata, il prezzo sarebbe:

Prezzo fisso: 744 (ore) - 0,20 USD - 148,8 USD

Prezzo unitario di capacità: 744(ore) - Max (unità di calcolo 0/50 per le connessioni/sec, 0/2,22 unità di capacità per la velocità effettiva) - 0,008 USD 744 x 50,008 USD per 297,60 USD

Prezzo totale: 148,80 USD 297,60 USD

In questo caso, ti viene addebitato l'intero caso delle cinque istanze anche se non c'è traffico.

**Esempio 4**

Viene eseguito il provisioning di un standard_v2 del gateway applicazione per un mese, con un minimo di cinque istanze, ma questa volta è presente una media di trasferimento dati di 125 mbps e 25 connessioni TLS al secondo. Supponendo che non ci sia traffico e che le connessioni siano di breve durata, il prezzo sarebbe:

Prezzo fisso: 744 (ore) - 0,20 USD - 148,8 USD

Prezzo unitario di capacità: 744(ore) - Max (25/50 unità di calcolo per le connessioni/sec, 125/2,22 unità di capacità per la velocità effettiva) - 0,008 USD, 744 USD 57 , 0,008 USD, 339,26 USD

Prezzo totale: 148,80 USD, 339,26 USD, 488,06 USD

In questo caso, vengono fatturate le cinque istanze complete, più sette unità di capacità (ovvero 7/10 di un'istanza).  

**Esempio 5**

Viene eseguito il provisioning di un WAF_v2 del gateway applicazione per un mese. Durante questo periodo, riceve 25 nuove connessioni TLS/sec, media di 8,88 Mbps di trasferimento dati ed esegue 80 richieste al secondo. Supponendo che le connessioni siano di breve durata e che il calcolo dell'unità di calcolo per l'applicazione supporti 10 richieste al secondo per unità di calcolo, il prezzo sarà:Assuming connections are short-lived, and that compute unit calculation for the application supports 10 RPS per compute unit, your price would be:

Prezzo fisso: 744 (ore) - 0,36 USD e 267,84 USD

Prezzo unitario di capacità: 744(ore) - Max (unità di calcolo Max(25/50 per le connessioni/sec, 80/10 WAF RPS), 8,88/2,22 unità di capacità per la velocità effettiva)

Prezzo totale: 266,84 USD, 85,71 USD E 353,55 USD

> [!NOTE]
> La funzione Max restituisce il valore più grande in una coppia di valori.

## <a name="scaling-application-gateway-and-waf-v2"></a>Scalabilità del gateway applicazione e della rete WAF v2

Il gateway applicazione e WAF possono essere configurati per la scalabilità in due modalità:Application Gateway and WAF can be configured to scale in two modes:

- **Scalabilità automatica:** con la scalabilità automatica abilitata, il gateway applicazione e gli SKU WAF v2 aumentano o aumentano in base ai requisiti di traffico dell'applicazione. Questa modalità offre una migliore elasticità all'applicazione ed elimina la necessità di indovinare le dimensioni del gateway applicazione o il numero di istanze. Questa modalità consente inoltre di risparmiare sui costi non richiedendo al gateway di essere eseguito con la massima capacità di provisioning per il carico massimo di traffico previsto. È necessario specificare un numero minimo e facoltativamente massimo di istanze. La capacità minima garantisce che il gateway applicazione e WAF v2 non scendano al di sotto del numero minimo di istanze specificato, anche in assenza di traffico. Ogni istanza viene conteggiata come 10 unità di capacità riservate aggiuntive. Lo zero non indica alcuna capacità riservata ed è puramente automatico in natura. Si prega di notare che zero istanze minime aggiuntive garantisce ancora un'elevata disponibilità del servizio che è sempre incluso con prezzo fisso. Facoltativamente, è anche possibile specificare un numero massimo di istanze, in modo da garantire che il gateway applicazione non venga ridimensionato oltre il numero di istanze specificato. Continuerai a essere fatturato per la quantità di traffico servita dal gateway. I conteggi delle istanze possono variare da 0 a 125. Il valore predefinito per il numero massimo di istanze è 20 se non specificato.
- **Manuale:** in alternativa, è possibile scegliere La modalità manuale in cui il gateway non viene ridimensionato automaticamente. In questa modalità, se è presente più traffico rispetto a ciò che il gateway applicazione o WAF può gestire, potrebbe causare la perdita di traffico. Con la modalità manuale, è obbligatorio specificare il numero di istanze. Il numero di istanze può variare da 1 a 125 istanze.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Confronto delle funzionalità tra Lo SKU v1 e lo SKU v2Feature comparison between v1 SKU and v2 SKU

Nella tabella seguente vengono confrontate le funzionalità disponibili con ogni SKU.

|                                                   | SKU v1   | SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Scalabilità automatica                                       |          | &#x2713; |
| Ridondanza della zona                                   |          | &#x2713; |
| Indirizzo VIP statico                                        |          | &#x2713; |
| Controller in ingresso del servizio Azure Kubernetes (AKS) Azure Kubernetes Service (AKS) Ingress controller |          | &#x2713; |
| Integrazione dell'insieme di credenziali delle chiavi di Azure                       |          | &#x2713; |
| Riscrivere le intestazioni HTTP(S)                           |          | &#x2713; |
| Routing basato su URL                                 | &#x2713; | &#x2713; |
| Hosting di più siti                             | &#x2713; | &#x2713; |
| Reindirizzamento del traffico                               | &#x2713; | &#x2713; |
| WAF (Web application firewall)                    | &#x2713; | &#x2713; |
| Regole personalizzate di WAF                                  |          | &#x2713; |
| Terminazione TLS (Transport Layer Security)/Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| Crittografia TLS end-to-end                         | &#x2713; | &#x2713; |
| Affinità di sessione                                  | &#x2713; | &#x2713; |
| Pagine di errore personalizzate                                | &#x2713; | &#x2713; |
| Supporto per WebSocket                                 | &#x2713; | &#x2713; |
| Supporto HTTP/2                                    | &#x2713; | &#x2713; |
| Esaurimento delle connessioni                               | &#x2713; | &#x2713; |

> [!NOTE]
> Lo SKU di scalabilità automatica v2 supporta ora i probe di [integrità predefiniti](application-gateway-probe-overview.md#default-health-probe) per monitorare automaticamente l'integrità di tutte le risorse nel pool back-end ed evidenziare i membri back-end considerati non integri. Il probe di integrità predefinito viene configurato automaticamente per i back-end che non dispongono di alcuna configurazione probe personalizzata. Per altre informazioni, vedere probe di [integrità nel gateway applicazione](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Differenze con lo SKU v1

|Differenza|Dettagli|
|--|--|
|Autenticazione del certificato|Non supportato.<br>Per ulteriori informazioni, vedere [Panoramica di TLS end-to-end con gateway applicazione](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Combinazione di versione 2 Standard e gateway applicazione standard nella stessa subnet|Non supportate|
|Route definita dall'utente nella subnet del gateway applicazioneUser-Defined Route (UDR) on Application Gateway subnet|Supportato (scenari specifici). In anteprima.<br> Per ulteriori informazioni sugli scenari supportati, vedere [Panoramica](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)della configurazione del gateway applicazione .|
|Gruppo di sicurezza di rete per intervallo di porte in ingresso| - Da 65200 a 65535 per SKU versione 2 Standard<br>- Da 65503 a 65534 per SKU Standard.<br>Per altre informazioni, vedere la sezione [Domande frequenti](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Contatori delle prestazioni in Diagnostica di Azure|Non supportato.<br>Usare le metriche di Azure.|
|Fatturazione|L'inizio della fatturazione è previsto per il 1 luglio 2019.|
|Modalità FIPS|Attualmente non supportati.|
|Modalità solo bilanciamento del carico interno|Non supportato attualmente. Le modalità pubblica e con bilanciamento del carico interno insieme non sono supportate.|
|Integrazione di Netwatcher|Non supportato.|
|Integrazione nel Centro sicurezza di Azure|Non ancora disponibile.

## <a name="migrate-from-v1-to-v2"></a>Eseguire la migrazione dalla versione 1 alla versione 2

Uno script di Azure PowerShell è disponibile nella raccolta di PowerShell per facilitare la migrazione dal gateway applicazione/WAF v1 allo SKU di scalabilità automatica v2. Questo script consente di copiare la configurazione dal gateway v1. La migrazione del traffico è ancora responsabilità dell'utente. Per altre informazioni, vedere Eseguire la migrazione del gateway applicazione di Azure dalla versione 1 alla versione 2.For more information, see [Migrate Azure Application Gateway from v1 to v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: Indirizzare il traffico Web con un gateway applicazione di Azure - Portale di Azure](quick-create-portal.md)
- [Creare un gateway applicazione con scalabilità automatica e ridondanza della zona con un indirizzo IP virtuale riservato tramite Azure PowerShell](tutorial-autoscale-ps.md)
- Altre informazioni sul [gateway applicazione](overview.md).
- Altre informazioni su [Firewall di Azure](../firewall/overview.md).
