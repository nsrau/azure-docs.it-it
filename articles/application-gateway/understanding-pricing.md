---
title: Informazioni sui prezzi-gateway applicazione Azure
description: Questo articolo descrive il processo di fatturazione per applicazione Azure gateway e il firewall applicazione Web per gli SKU da V1 a V2
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: conceptual
ms.custom: references_regions
ms.date: 09/01/2020
ms.author: azhussai
ms.openlocfilehash: 1d88379726cfb6c4218c38b9ccc87005609a9aba
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460746"
---
# <a name="understanding-pricing-for-azure-application-gateway-and-web-application-firewall"></a>Informazioni sui prezzi per applicazione Azure gateway e Web Application Firewall

>[!NOTE]
>I prezzi indicati in questo articolo sono esempi e sono solo a scopo illustrativo. Per informazioni sui prezzi in base all'area geografica, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

Applicazione Azure gateway è una soluzione di bilanciamento del carico di livello 7, che consente la distribuzione di applicazioni Web scalabili, a disponibilità elevata e sicura in Azure.

Al gateway applicazione non sono associati costi iniziali o costi di terminazione.
Verranno addebitate solo le risorse di cui è stato eseguito il pre-provisioning e utilizzate in base al consumo orario effettivo. I costi associati al gateway applicazione sono suddivisi in due componenti: costi fissi e costi variabili. I costi effettivi in ogni componente variano in base allo SKU usato.

Questo articolo descrive i costi associati a ogni SKU ed è consigliabile che gli utenti utilizzino questo documento per la pianificazione e la gestione dei costi associati al gateway applicazione Azure.

## <a name="v1-skus"></a>SKU V1

Il gateway applicazione standard e gli SKU WAF V1 vengono fatturati come una combinazione di:

* Costi fissi

    Costo basato sul tempo in cui viene effettuato il provisioning e l'esecuzione di un particolare tipo di gateway applicazione/WAF per le richieste di elaborazione.
    Il componente a costo fisso prende in considerazione i fattori seguenti:
    * Livello: gateway applicazione standard o WAF
    * Dimensioni-piccolo, medio & grande
    * Conteggio istanze-numero di istanze da distribuire

    Per N istanze, i costi associati saranno N * costo di un'istanza di un particolare livello (standard & WAF) & dimensione (piccola, media & grande).

* Costo variabile

    Costo basato sulla quantità di dati elaborati dal gateway applicazione/WAF. Sia i byte di richiesta che quelli di risposta elaborati dal gateway applicazione verranno considerati per la fatturazione.

Costo totale = costo fisso + costo variabile

### <a name="standard-application-gateway"></a>Gateway applicazione standard

Costo fisso & costo variabile verrà calcolato in base al tipo di gateway applicazione.
La tabella seguente illustra i prezzi di esempio basati su uno snapshot dei prezzi degli Stati Uniti orientali e sono destinati solo a scopo illustrativo.

#### <a name="fixed-cost-east-us-region-pricing"></a>Costo fisso (prezzi dell'area Stati Uniti orientali)

|              Tipo di gateway applicazione             |  Costi ($/ora)  |
| ------------------------------------------------- | ---------------|
|                     Piccola                         |    $0,025      |
|                     Media                        |    $ 0,07       |
|                     Grande                         |    $0,32       |

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

#### <a name="variable-cost-east-us-region-pricing"></a>Costo variabile (prezzi dell'area Stati Uniti orientali)

|              Dati elaborati             |  Piccolo ($/GB)  |  Media ($/GB) |  Grande ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Primi 10 TB/mese                       |     $ 0,008     |      Livello gratuito      |     Livello gratuito      |
| Successivi 30 TB (10 – 40 TB)/mese             |     $ 0,008     |     $0,007     |     Livello gratuito      |
| Oltre 40 TB/mese                        |     $ 0,008     |     $0,007     |     $0,0035   |

Per ulteriori informazioni sui prezzi in base all'area geografica, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

### <a name="waf-v1"></a>WAF V1

Costo fisso & costi variabili verranno calcolati in base al tipo di gateway applicazione di cui è stato effettuato il provisioning.

La tabella seguente illustra i prezzi di esempio basati su uno snapshot dei prezzi degli Stati Uniti orientali e sono solo a scopo illustrativo.

#### <a name="fixed-cost-east-us-region-pricing"></a>Costo fisso (prezzi dell'area Stati Uniti orientali)

|              Tipo di gateway applicazione             |  Costi ($/ora)  |
| ------------------------------------------------- | ---------------|
|                     Piccola                         |       N/D       |
|                     Media                        |     $0,126     |
|                     Grande                         |     $0,448     |

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

#### <a name="variable-cost-east-us-region-pricing"></a>Costo variabile (prezzi dell'area Stati Uniti orientali)

|              Dati elaborati             |  Piccolo ($/GB)  |  Media ($/GB) |  Grande ($/GB) |
| --------------------------------------- | -------------- | -------------- | ------------- |
| Primi 10 TB/mese                       |     $ 0,008     |      Livello gratuito      |     Livello gratuito      |
| Successivi 30 TB (10 – 40 TB)/mese             |     $ 0,008     |     $0,007     |     Livello gratuito      |
| Oltre 40 TB/mese                        |     $ 0,008     |     $0,007     |     $0,0035   |

Per ulteriori informazioni sui prezzi in base all'area geografica, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Trasferimenti di dati in uscita: i dati usciti dai Data Center di Azure dai gateway applicazione verranno addebitati in base alle [tariffe di trasferimento dati](https://azure.microsoft.com/pricing/details/bandwidth/)standard.

### <a name="example-1-a--standard-application-gateway-with-1-instance-count"></a>Esempio 1 (a): gateway applicazione standard con un numero di istanze

Si supponga di avere effettuato il provisioning di un gateway applicazione standard di tipo medio con 1 istanza ed elabora 500 GB in un mese. I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

Prezzo fisso = $0,07 * 730 (ore) = $51,1 i prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

Costi variabili = gratuito (il livello medio non prevede costi per i primi 10 TB elaborati al mese) costi totali = $51,1 + 0 = $51,1 

> [!NOTE]
> Per supportare scenari a disponibilità elevata, è necessario configurare almeno 2 istanze per gli SKU V1. Vedere [SLA per il gateway applicazione](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)

### <a name="example-1-b--standard-application-gateway-with--1-instance-count"></a>Esempio 1 (b): numero di istanze del gateway applicazione standard con > 1

Si supponga di avere effettuato il provisioning di un gateway applicazione standard di tipo medio con cinque istanze ed elabora 500 GB in un mese. I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

Prezzo fisso = 5 (numero di istanze) * $0,07 * 730 (ore) = $255,5 i prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

Costi variabili = gratuito (il livello medio non prevede costi per i primi 10 TB elaborati al mese) costi totali = $255,5 + 0 = $255,5 

### <a name="example-2--waf-application-gateway"></a>Esempio 2: gateway applicazione WAF

Si supponga di avere effettuato il provisioning di un gateway applicazione standard di piccole dimensioni e un gateway applicazione WAF di tipo Large per i primi 15 giorni del mese. Il gateway applicazione Small elabora 15 TB nel periodo in cui è attivo e il gateway applicazione WAF di grandi dimensioni elabora 100 TB nel periodo di tempo in cui è attivo. I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue: 

###### <a name="small-instance-standard-application-gateway"></a>Gateway applicazione standard Small instance

24 ore * 15 giorni = 360 ore

Prezzo fisso = $0,025 * 360 (ore) = $9

Costi variabili = 10 * 1000 * $0.008/GB + 5 * 1000 * $0.008/GB = $120

Costi totali = $9 + $120 = $129

###### <a name="large-instance-waf-application-gateway"></a>Gateway applicazione WAF per istanze large
24 ore * 15 giorni = 360 ore

Prezzo fisso = $0,448 * 360 (ore) = $161,28

Costi variabili = 60 * 1000 * $0.0035/GB = $210 (il livello elevato non prevede costi per i primi 40 TB elaborati al mese)

Costi totali = $161,28 + $210 = $371,28

## <a name="v2-skus"></a>SKU V2  

Gli SKU del gateway applicazione V2 e WAF V2 supportano la scalabilità automatica e garantiscono la disponibilità elevata per impostazione predefinita.

### <a name="key-terms"></a>Termini chiave

##### <a name="capacity-unit"></a>Unità di capacità 
Unità di capacità è la misura dell'utilizzo della capacità per un gateway applicazione tra più parametri.

Una singola unità di capacità è costituita dai parametri seguenti:
* 2500 connessioni permanenti
* velocità effettiva 2,22 Mbps
* 1 unità di calcolo

Se uno di questi parametri viene superato, è necessario un altro numero di unità di capacità, anche se gli altri due parametri non superano i limiti di questa unità di capacità singola.
Il parametro con l'utilizzo più elevato tra i tre precedenti verrà usato internamente per il calcolo delle unità di capacità, che a sua volta viene fatturato.

##### <a name="compute-unit"></a>Unità di calcolo
Unità di calcolo è la misura della capacità di calcolo usata. I fattori che influiscono sul consumo delle unità di calcolo sono connessioni TLS/sec, calcoli di riscrittura URL e elaborazione di regole WAF. Il numero di richieste che possono essere gestite da un'unità di calcolo dipende da diversi criteri, ad esempio le dimensioni della chiave del certificato TLS, l'algoritmo di scambio delle chiavi, le riscritture delle intestazioni e in caso di dimensioni della richiesta in ingresso WAF.

Unità di calcolo nel dettaglio:
* Standard_v2: ogni unità di calcolo è in grado di effettuare circa 50 connessioni al secondo con il certificato TLS della chiave RSA a 2048 bit.

* WAF_v2: ogni unità di calcolo può supportare circa 10 richieste simultanee al secondo per una combinazione di traffico 70-30%, con il 70% di richieste inferiore a 2 KB GET/POST e le rimanenti impostate su un valore superiore. Le prestazioni di Web Application Firewall (WAF) non sono attualmente interessate dalle dimensioni della risposta.

##### <a name="instance-count"></a>Conteggio istanze 
Il pre-provisioning delle risorse per gli SKU del gateway applicazione V2 è definito in termini di conteggio delle istanze. Ogni istanza garantisce almeno 10 unità di capacità in termini di capacità di elaborazione. La stessa istanza potrebbe supportare più di 10 unità di capacità per diversi modelli di traffico, a seconda dei parametri dell'unità di capacità.

La scala e i limiti definiti manualmente impostati per la scalabilità automatica (minima o massima) vengono impostati in termini di numero di istanze. La scala impostata manualmente per il numero di istanze e il numero minimo di istanze nella configurazione di scalabilità automatica riserva 10 unità di capacità/istanza. Il valore di questo CUs riservato verrà fatturato purché il gateway applicazione sia attivo indipendentemente dal consumo effettivo delle risorse. Se il consumo effettivo supera la soglia di 10 unità di capacità/istanza, verranno fatturate unità di capacità aggiuntive nel componente variabile.

Gli SKU V2 vengono fatturati in base al consumo e costituiscono due parti:

* Costi fissi

    Costo basato sul tempo di provisioning del gateway applicazione V2/WAF V2 e disponibile per l'elaborazione delle richieste. In questo modo si garantisce la disponibilità elevata, anche se 0 istanze sono riservate specificando 0 nel numero minimo di istanze come parte della scalabilità automatica. 
    
    Il costo fisso include anche il costo associato all'indirizzo IP pubblico collegato al gateway applicazione.

    Il numero di istanze in esecuzione in un determinato momento non è considerato come fattore per i costi fissi per gli SKU V2. I costi fissi per l'esecuzione di un Standard_V2 (o WAF_V2) sarebbero uguali all'ora, indipendentemente dal numero di istanze in esecuzione nella stessa area di Azure.

* Costi delle unità di capacità 

    Costo basato sul numero di unità di capacità riservate o utilizzate anche come richiesto per l'elaborazione delle richieste in ingresso.  I costi basati sul consumo vengono calcolati ogni ora.

Costi totali = costi fissi + costi di unità di capacità

> [!NOTE]
> Un'ora parziale viene fatturata come ora intera.

La tabella seguente illustra i prezzi di esempio basati su uno snapshot dei prezzi degli Stati Uniti orientali e sono solo a scopo illustrativo.

#### <a name="fixed-costs-east-us-region-pricing"></a>Costi fissi (prezzi dell'area Stati Uniti orientali)

|              SKU V2             |  Costi ($/ora)  |
| ------------------------------- | ---------------|
|            Standard_V2          |     $0,246     |
|              WAF_V2             |     $0,443     |

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

#### <a name="variable-costs-east-us-region-pricing"></a>Costi variabili (prezzi per l'area Stati Uniti orientali)

|        Unità di capacità         |  Standard_V2 ($/ora)  |  WAF_V2 ($/ora) |
| ---------------------------- | -------------------- | -------------- |
|             1 CU             |        $ 0,008        |     $0,0144    |

Per ulteriori informazioni sui prezzi in base all'area geografica, vedere la pagina relativa ai [prezzi](https://azure.microsoft.com/pricing/details/application-gateway/).

> [!NOTE]
> Trasferimenti di dati in uscita: i dati usciti dai Data Center di Azure dai gateway applicazione verranno addebitati in base alle [tariffe di trasferimento dati](https://azure.microsoft.com/pricing/details/bandwidth/)standard.

### <a name="example-1-a--manual-scaling"></a>Esempio 1 (a)-scalabilità manuale 
Si supponga di avere effettuato il provisioning di un gateway applicazione Standard_V2 con scalabilità manuale impostata su 8 istanze per l'intero mese. Durante questo periodo, riceve una media del trasferimento dati di 88,8 Mbps.

I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

1 CU può gestire la velocità effettiva di 2,22 Mbps.

CUs necessario per gestire 88,8 Mbps = 88,8/2,22 = 40 CUs 

CUs di cui è stato eseguito il pre-provisioning = 8 (numero di istanze) * 10 = 80 

Dal 80 (capacità riservata) > 40 (capacità obbligatoria), non sono necessarie altre unità di verifica. 

Prezzo fisso = $0,246 * 730 (ore) = $179,58

Costi variabili = $0,008 * 8 (unità di istanza) * 10 (unità di capacità) * 730 (ore) = $467,2

Costi totali = $179,58 + $467,2 = $646,78

![Diagramma della scala manuale 1.](./media/pricing/manual-scale-1.png)

### <a name="example-1-b--manual-scaling-with-traffic-going-beyond-provisioned-capacity"></a>Esempio 1 (b): ridimensionamento manuale con traffico che supera la capacità sottoposta a provisioning

Si supponga di avere effettuato il provisioning di un gateway applicazione Standard_V2 con scalabilità manuale impostata su 3 istanze per l'intero mese. Durante questo periodo, riceve una media del trasferimento dati di 88,8 Mbps.

I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

1 CU può gestire la velocità effettiva di 2,22 Mbps.

CUs necessario per gestire 88,8 Mbps = 88,8/2,22 = 40 

CUs di cui è stato eseguito il pre-provisioning = 3 (numero di istanze) * 10 = 30 

Dal 40 (capacità obbligatoria) > 30 (capacità riservata), sono necessari ulteriori CUs.
Il numero di CUs aggiuntivo utilizzato dipende dalla capacità disponibile disponibile in ogni istanza.

Se la capacità di elaborazione equivalente a 10 CUs aggiuntivo era disponibile per l'uso nelle tre istanze riservate.

Prezzo fisso = $0,246 * 730 (ore) = $179,58

Costi variabili = $0,008 * (3 (unità di istanza) * 10 (unità di capacità) + 5 (unità di capacità aggiuntive)) * 730 (ore) = $204,4

Costi totali = $179,58 + $204,4 = $383,98

![Diagramma della scala 2 manuale.](./media/pricing/manual-scale-2.png)

> [!NOTE]
> In caso di ridimensionamento manuale, qualsiasi richiesta aggiuntiva che superi la capacità di elaborazione massima delle istanze riservate può causare effetti sulla disponibilità dell'applicazione. In situazioni di carico elevato, le istanze riservate possono essere in grado di fornire più di 10 unità di capacità di capacità di elaborazione a seconda della configurazione e del tipo di richieste in ingresso. È tuttavia consigliabile effettuare il provisioning del numero di istanze in base ai requisiti del traffico.

### <a name="example-2--waf_v2-instance-with-autoscaling"></a>Esempio 2: WAF_V2 istanza con scalabilità automatica

Si supponga di avere effettuato il provisioning di un WAF_V2 con la scalabilità automatica abilitata e di impostare il numero minimo di istanze su 6 per l'intero mese. Il carico della richiesta ha causato la scalabilità orizzontale dell'istanza di WAF e l'uso di unità di capacità di 65 (scalabilità orizzontale di 5 unità di capacità, mentre 60 unità sono state riservate) per l'intero mese.
I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

Prezzo fisso = $0,443 * 730 (ore) = $323,39

Costi variabili = $0,0144 * 65 (unità di capacità) * 730 (ore) = $683,28

Costi totali = $323,39 + $683,28 = $1006,67

![Diagramma di scalabilità automatica 2.](./media/pricing/auto-scale-1.png)

> [!NOTE]
> Il traffico effettivo osservato per il gateway applicazione è improbabile che abbia un modello di traffico costante e il carico osservato sul gateway applicazione fluttua in base all'utilizzo effettivo.

### <a name="example-3-a--waf_v2-instance-with-autoscaling-and-0-min-scale-config"></a>Esempio 3 (a)-WAF_V2 istanza con scalabilità automatica e configurazione di scala 0 min

Si supponga di avere effettuato il provisioning di un WAF_V2 con la scalabilità automatica abilitata e di impostare il numero minimo di istanze su 0 per l'intero mese. Il carico della richiesta sul WAF è minimo, ma è costantemente presente all'ora per l'intero mese. Il carico è inferiore alla capacità di una singola unità di capacità.
I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

Prezzo fisso = $0,443 * 730 (ore) = $323,39

Costi variabili = $0,0144 * 1 (unità di capacità) * 730 (ore) = $10,512

Costi totali = $323,39 + $10,512 = $333,902

### <a name="example-3-b--waf_v2-instance-with-autoscaling-with-0-min-instance-count"></a>Esempio 3 (b): WAF_V2 istanza con scalabilità automatica con numero di istanze pari a 0 min

Si supponga di avere effettuato il provisioning di un WAF_V2 con la scalabilità automatica abilitata e di impostare il numero minimo di istanze su 0 per l'intero mese. Tuttavia, c'è 0 traffico indirizzato all'istanza di WAF per l'intero mese.
I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

Prezzo fisso = $0,443 * 730 (ore) = $323,39

Costi variabili = $0,0144 * 0 (unità di capacità) * 730 (ore) = $0

Costi totali = $323,39 + $0 = $323,39

### <a name="example-3-c--waf_v2-instance-with-manual-scaling-set-to-1-instance"></a>Esempio 3 (C)-WAF_V2 istanza con scalabilità manuale impostata su 1 istanza

Si supponga di avere effettuato il provisioning di una WAF_V2 e di impostarla sulla scalabilità manuale con il valore minimo accettabile di 1 istanza per l'intero mese. Tuttavia, c'è 0 traffico indirizzato a WAF per l'intero mese.
I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

Prezzo fisso = $0,443 * 730 (ore) = $323,39

Costi variabili = $0,0144 * 1 (conteggio istanze) * 10 (unità di capacità) * 730 (ore) = $105,12

Costi totali = $323,39 + $105,12 = $428,51

### <a name="example-4--waf_v2-with-autoscaling-capacity-unit-calculations"></a>Esempio 4: WAF_V2 con scalabilità automatica, calcoli di unità di capacità

Si supponga di avere effettuato il provisioning di un WAF_V2 con la scalabilità automatica abilitata e di impostare il numero minimo di istanze su 0 per l'intero mese. Durante questo periodo, riceve 25 nuove connessioni TLS/sec con una media del trasferimento dati di 8,88 Mbps.
I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

I prezzi mensili stimati si basano su 730 ore di utilizzo al mese.

Prezzo fisso = $0,443 * 730 (ore) = $323,39

Costi variabili = $0,0144 * 730 (ore) * {Max (25/50, 8.88/2.22)} = $23,36 (4 unità di capacità necessarie per gestire 8,88 Mbps)

Costi totali = $323,39 + $23,36 = $346,75

### <a name="example-5-a--standard_v2-with-autoscaling-time-based-calculations"></a>Esempio 5 (a): Standard_V2 con scalabilità automatica, calcoli basati sul tempo

Si supponga di avere effettuato il provisioning di un standard_V2 con la scalabilità automatica abilitata e di impostare il numero minimo di istanze su 0 e che il gateway applicazione sia attivo per 2 ore.
Durante la prima ora riceve traffico che può essere gestito da 10 unità di capacità e durante la seconda ora riceve il traffico che ha richiesto 20 unità di capacità per gestire il carico.
I costi del gateway applicazione usando i prezzi indicati in precedenza verranno calcolati come segue:

Prezzo fisso = $0,246 * 2 (ore) = $0,492

Costi variabili = $0,008 * 10 (unità di capacità) * 1 (ore) + $0,008 * 20 (unità di capacità) * 1 (ore) = $0,24

Costi totali = $0,492 + $0,24 = $0,732


## <a name="monitoring-billed-usage"></a>Monitoraggio dell'utilizzo fatturato

È possibile visualizzare la quantità di consumo per parametri diversi (unità di calcolo, velocità effettiva & connessioni permanenti) e le unità di capacità usate come parte delle metriche del gateway applicazione nella sezione **monitoraggio** .

![Diagramma della sezione metrica.](./media/pricing/metrics-1.png)

### <a name="useful-metrics-for-cost-estimation"></a>Metriche utili per la stima dei costi

* Unità di capacità correnti

    Numero di unità di capacità utilizzate per bilanciare il carico del traffico tra i tre parametri, ovvero le connessioni correnti, la velocità effettiva e l'unità di calcolo

* Unità di capacità fatturabili fisse

    Il numero minimo di unità di capacità di cui è stato effettuato il provisioning in base all'impostazione del numero minimo di istanze (un'istanza è convertita in 10 unità di capacità) nella configurazione del gateway applicazione.

* Unità di capacità stimate per la fatturazione

    Le unità di capacità stimate per la fatturazione indicano il numero di unità di capacità in base a cui viene stimata la fatturazione. Questo calcolo viene eseguito considerando il valore maggiore tra unità di capacità correnti (le unità di capacità necessarie per il bilanciamento del carico del traffico) e unità di capacità fatturabili fisse (le unità di capacità minime di cui viene mantenuto il provisioning).

Sono inoltre disponibili altre metriche, ad esempio velocità effettiva, connessioni correnti e unità di calcolo, per comprendere i colli di bottiglia e stimare il numero di unità di capacità necessarie. Informazioni dettagliate sono disponibili in [metriche del gateway applicazione](application-gateway-metrics.md)

#### <a name="example---estimating-capacity-units-being-utilized"></a>Esempio: stima delle unità di capacità in uso

**Metriche osservate:**

* Unità di calcolo = 17,38
* Velocità effettiva = 1.37 M byte/sec-10,96 Mbps
* Connessioni correnti = 123.08 k
* Unità di capacità calcolate = max (17.38, 10.96/2.22, 123.08 k/2500) = 49,232

Unità di capacità osservate in metriche = 49,23

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul funzionamento dei prezzi in applicazione Azure gateway:

* [Pagina dei prezzi di applicazione Azure gateway](https://azure.microsoft.com/pricing/details/application-gateway/)
* [Calcolatore prezzi di applicazione Azure gateway](https://azure.microsoft.com/en-us/pricing/calculator/?service=application-gateway)
