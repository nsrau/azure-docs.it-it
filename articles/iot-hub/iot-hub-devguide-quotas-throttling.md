---
title: Informazioni sulle quote e sulle limitazioni dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: descrizione delle quote che si applicano all'hub IoT e del comportamento di limitazione previsto."
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284694"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Riferimento - Quote e limitazioni dell'hub IoT

Questo articolo illustra le quote per un hub IoT e fornisce informazioni che consentono di comprendere il funzionamento della limitazione delle richieste.

## <a name="quotas-and-throttling"></a>Quote e limitazioni

Ogni sottoscrizione di Azure può avere al massimo 50 hub IoT e al massimo un hub gratuito.

Il provisioning di ogni hub IoT viene eseguito con un determinato numero di unità in un livello specifico. Il livello e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare. La dimensione dei messaggi usata per calcolare che la quota giornaliera è 0,5 kB per un hub di livello gratuito e 4 kB per tutti gli altri livelli. Per altre informazioni, vedere [Prezzi di Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

Il livello determina anche le limitazioni che l'hub IoT applica alle operazioni.

### <a name="iot-plug-and-play"></a>Plug and Play IoT

Durante l'anteprima pubblica, i dispositivi Plug and Play IoT invieranno messaggi separati per ogni interfaccia, il che potrebbe aumentare il numero di messaggi conteggiati per la quota dei messaggi.

## <a name="operation-throttles"></a>Limitazioni per le operazioni

Le limitazioni operative sono limitazioni di frequenza applicate in intervalli di minuti e hanno lo scopo di prevenire l'abuso. Sono anche soggetti a [traffic shaping](#traffic-shaping).

La tabella seguente mostra le limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Nessuna, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Operazioni del registro di identità](#identity-registry-operations-throttle) (creazione, recupero, elenco, aggiornamento, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unità (5.000/min/unità) |
| [Nuove connessioni](#device-connections-throttle) di dispositivi (questo limite si applica alla frequenza delle _nuove connessioni,_ non al numero totale di connessioni) | Più di 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24 nuove connessioni/sec, ma si hanno almeno 100 nuove conessioni/sec tra le unità. Con nove unità S1 si otterranno 108 nuove connessioni/sec (9\*12) tra le unità. | 120 nuove connessioni/sec/unità | 6.000 nuove connessioni/sec/unità |
| Inoltri dal dispositivo al cloud | Maggiore di 100 operazioni di invio/sec o 12 operazioni di invio/sec/unità <br/> Ad esempio, due unità\*S1 sono 2 12 e 24/sec, ma sono presenti almeno 100 operazioni di invio/sec tra le unità. Con nove unità S1, hai 108 operazioni\*di invio/sec (9 12) tra le tue unità. | 120 operazioni di invio/sec/unità | 6.000 operazioni di invio/sec/unità |
| Inoltri dal cloud al dispositivo<sup>1</sup> | 1.67 operazioni di invio/sec/unità (100 messaggi/min/unità) | 1.67 operazioni di invio/sec/unità (100 operazioni di invio/min/unità) | 83.33 operazioni di invio/sec/unità (5.000 operazioni di invio/min/unità) |
| Ricezioni dal cloud al dispositivo<sup>1</sup> <br/> (solo quando il dispositivo usa HTTPS)| 16,67 operazioni di ricezione/sec/unità (1.000 operazioni di ricezione/min/unità) | 16,67 operazioni di ricezione/sec/unità (1.000 operazioni di ricezione/min/unità) | 833.33 operazioni di ricezione/sec/unità (50.000 operazioni di ricezione/min/unità) |
| Caricamento di file | 1.67 avvii di caricamento file/sec/unità (100/min/unit) | 1.67 avvii di caricamento file/sec/unità (100/min/unit) | 83.33 avvii di caricamento file/sec/unità (5.000/min/unità) |
| Metodi diretti<sup>1</sup> | 160 KB/sec/unità<sup>2</sup> | 480 KB/sec/unità<sup>2</sup> | 24 MB/sec/unità<sup>2</sup> | 
| Query | 20/min/unità | 20/min/unità | 1.000/min/unità |
| Letture di dispositivi e moduli gemelli <sup>1</sup> | 100/sec | Superiore di 100/sec o 10/sec/unità | 500/sec/unità |
| Aggiornamenti di dispositivi e moduli gemelli <sup>1</sup> | 50/sec | Superiore di 50/sec o 5/sec/unità | 250/sec/unità |
| Operazioni dei processi<sup>1</sup> <br/> (creazione, aggiornamento, elenco, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unità (5.000/min/unità) |
| Operazioni dei dispositivi dei processi<sup>1</sup> <br/> (aggiornamento del dispositivo gemello, chiamata del metodo diretto) | 10/sec | Più di 10/sec o 1/sec/unità | 50/sec/unità |
| Configurazioni e distribuzioni Edge<sup>1</sup> <br/> (creazione, aggiornamento, elenco, eliminazione) | 0,33/sec/unità (20/min/unità) | 0,33/sec/unità (20/min/unità) | 0,33/sec/unità (20/min/unità) |
| Frequenza di avvio flusso dispositivo<sup>1</sup> | 5 nuovi flussi/sec | 5 nuovi flussi/sec | 5 nuovi flussi/sec |
| Numero massimo di flussi di dispositivi connessi contemporaneamente<sup>1</sup> | 50 | 50 | 50 |
| Trasferimento massimo dei dati del flusso di dispositivo 1 (volume aggregato al giorno)Maximum device stream data transfer<sup>1</sup> (aggregate volume per day) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>Questa funzionalità non è disponibile nel livello di base dell'hub IoT. Per altre informazioni, vedere [How to choose the right IoT Hub](iot-hub-scaling.md) (Come scegliere il livello dell'hub IoT corretto). <br/><sup>2 Il</sup> nome del sistema La dimensione del misuratore di limitazione è 4 KB.

### <a name="throttling-details"></a>Dettagli di limitazione

* La dimensione del contatore determina gli incrementi utilizzati dal limite di limitazione. Se il payload della chiamata diretta è compreso tra 0 e 4 KB, viene conteggiato come 4 KB. È possibile effettuare fino a 40 chiamate al secondo per unità prima di raggiungere il limite di 160 KB/sec/unità.

   Analogamente, se il payload è compreso tra 4 e 8 KB, ogni chiamata ha un costo di 8 KB ed è possibile effettuare fino a 20 chiamate al secondo per unità prima di raggiungere il limite massimo.

   Infine, se la dimensione del payload è compresa tra 156 KB e 160 KB, sarà possibile effettuare solo 1 chiamata al secondo per unità nell'hub prima di raggiungere il limite di 160 KB/sec/unità.

*  Per *le operazioni del dispositivo Jobs (aggiornamento gemello, richiamo metodo diretto)* per il livello S2, 50/sec/unità si applica solo quando si richiamano metodi utilizzando i processi. Se si richiamano direttamente i metodi diretti, viene applicato il limite di limitazione originale di 24 MB/sec/unità (per S2).

*  **Quota** è il numero aggregato di messaggi che è possibile inviare all'hub *al giorno.* È possibile trovare il limite di quota dell'hub nella colonna **Numero totale di messaggi/giorno** nella pagina dei prezzi dell'hub [IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Le limitazioni da cloud a dispositivo e da dispositivo a cloud determinano la *velocità* massima alla quale è possibile inviare messaggi, ovvero il numero di messaggi indipendentemente dai blocchi da 4 KB. Ogni messaggio può essere fino a 256 KB, ovvero la dimensione massima del [messaggio.](iot-hub-devguide-quotas-throttling.md#other-limits)

*  È consigliabile limitare le chiamate in modo da non raggiungere/superare i limiti di limitazione. Se raggiungi il limite, l'hub IoT risponde con il codice di errore 429 e il client deve eseguire il back-off e riprovare. Questi limiti sono per hub (o in alcuni casi per hub/unità). Per ulteriori informazioni, vedere Gestire la connettività e i modelli di [ripetizione dei tentativi di messaggistica/riprova affidabili](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Modellazione del traffico

Per gestire il traffico burst, l'hub IoT accetta richieste al di sopra della limitazione per un periodo di tempo limitato. Le prime di queste richieste vengono elaborate immediatamente. Tuttavia, se il numero di richieste continua a violare la limitazione, l'hub IoT inizia a inserire le richieste in una coda ed elaborato alla frequenza limite. Questo effetto è chiamato *traffic shaping*. Inoltre, la dimensione di questa coda è limitata. Se la violazione dell'acceleratore continua, alla fine la coda si riempie e l'hub IoT inizia a rifiutare le richieste con `429 ThrottlingException`.

Ad esempio, si usa un dispositivo simulato per inviare 200 messaggi da dispositivo a cloud al secondo all'hub IoT S1 (che ha un limite di 100/sec inviati d2C). Per il primo minuto o due, i messaggi vengono elaborati immediatamente. Tuttavia, poiché il dispositivo continua a inviare più messaggi rispetto al limite di limitazione, l'hub IoT inizia a elaborare solo 100 messaggi al secondo e mette il resto in una coda. Si inizia a notare una maggiore latenza. Alla fine, `429 ThrottlingException` si inizia a ottenere come la coda si riempie, e il "numero di errori di limitazione" nelle [metriche di Hub IoT](iot-hub-metrics.md) inizia ad aumentare.

### <a name="identity-registry-operations-throttle"></a>Limitazione delle operazioni del Registro di sistema delle identità

Le operazioni del Registro di sistema relative all'identità del dispositivo sono destinate all'uso in fase di esecuzione negli scenari di gestione e provisioning dei dispositivi. L'operazione di lettura o aggiornamento di un numero elevato di identità dei dispositivi è supportata tramite i [processi di importazione ed esportazione](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Connessioni del dispositivo acceleratore

La limitazione delle *connessioni del dispositivo* determina la frequenza con cui possono essere stabilite nuove connessioni del dispositivo con un hub IoT. La limitazione delle *connessioni del dispositivo* non determina il numero massimo di dispositivi connessi contemporaneamente. La limitazione di frequenza delle *connessioni del dispositivo* dipende dal numero di unità di cui viene effettuato il provisioning per l'hub IoT.

Ad esempio, se si acquista una singola unità S1, si ottiene un limite di 100 connessioni al secondo. Pertanto, per collegare 100.000 dispositivi, ci vogliono almeno 1.000 secondi (circa 16 minuti). Tuttavia, è consentito un numero di dispositivi connessi simultaneamente pari al numero di dispositivi registrati nel registro delle identità.

## <a name="other-limits"></a>Altri limiti

L'hub IoT applica altri limiti operativi:

| Operazione | Limite |
| --------- | ----- |
| Dispositivi | Il numero totale di dispositivi più i moduli che possono essere registrati in un singolo hub IoT è limitato a 1.000.000. L'unico modo per aumentare questo limite consiste nel contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).|
| Caricamenti di file | 10 caricamenti simultanei di file per dispositivo. |
| Processi<sup>1</sup> | Il numero massimo di processi simultanei è 1 (gratuito e S1), 5 (per S2) e 10 (per S3). Tuttavia, il numero massimo di processi di [importazione/esportazione](iot-hub-bulk-identity-mgmt.md) dei dispositivi simultanei è 1 per tutti i livelli. <br/>La cronologia dei processi viene mantenuta fino a 30 giorni. |
| Altri endpoint | Agli hub SKU a pagamento possono essere associati 10 endpoint aggiuntivi. Agli hub SKU gratuiti può essere associato solo un endpoint aggiuntivo. |
| Query di routing dei messaggi | Gli hub SKU a pagamento possono avere 100 query di routing. Gli hub SKU gratuiti possono avere cinque query di routing. |
| Arricchimenti dei messaggi | Gli hub SKU a pagamento possono avere fino a 10 arricchimenti di messaggi. Gli hub SKU gratuiti possono avere fino a 2 arricchimenti di messaggi.|
| Messaggistica da dispositivo a cloud | Dimensioni massime dei messaggi 256 KB |
| Messaggistica da cloud a dispositivo<sup>1</sup> | La dimensione massima dei messaggi è 64 KB. Il numero massimo di messaggi in sospeso per il recapito è 50 per dispositivo. |
| Metodo diretto<sup>1</sup> | La dimensione massima del payload del metodo diretto è 128 KB. |
| Configurazioni automatiche di dispositivi e moduli<sup>1</sup> | 100 configurazioni per hub SKU a pagamento. 20 configurazioni per hub SKU gratuito. |
| Distribuzioni automatiche di IoT Edge<sup>1</sup> | 50 moduli per distribuzione. 100 distribuzioni (incluse le distribuzioni a più livelli) per hub SKU a pagamento. 10 distribuzioni per hub SKU gratuito. |
| Dispositivi gemelli<sup>1</sup> | La dimensione massima delle proprietà desiderate e le sezioni delle proprietà segnalate sono 32 KB ciascuna. La dimensione massima della sezione dei tag è 8 KB. |
| Criteri di accesso condivisi | Il numero massimo di criteri di accesso condiviso è 16 |

<sup>1</sup>Questa funzionalità non è disponibile nel livello di base dell'hub IoT. Per altre informazioni, vedere [How to choose the right IoT Hub](iot-hub-scaling.md) (Come scegliere il livello dell'hub IoT corretto).

## <a name="increasing-the-quota-or-throttle-limit"></a>Aumento della quota o del limite di velocità

In qualsiasi momento, è possibile aumentare le quote o limitare i limiti [aumentando il numero di unità di cui è stato eseguito il provisioning in un hub IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Latenza

L'hub IoT punta a fornire bassa latenza per tutte le operazioni. Tuttavia, a causa delle condizioni di rete e di altri fattori imprevedibili, non può garantire una certa latenza. Quando si progetta la soluzione, è necessario:

* Evitare di fare ipotesi sulla latenza massima di qualsiasi operazione dell'hub IoT.
* Eseguire il provisioning dell'hub IoT nell'area di Azure più vicina ai dispositivi.
* Considerare l'eventualità di usare Azure IoT Edge per eseguire operazioni sensibili alla latenza sul dispositivo o su un gateway vicino al dispositivo.

Più unità dell'hub IoT influiscono sulla limitazione come descritto in precedenza, ma non forniscono alcuna prestazione di latenza aggiuntiva o garanzia.

In caso di incremento imprevisto della latenza dell'operazione, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passaggi successivi

Per un'analisi approfondita del comportamento della limitazione dell'hub IoT, vedere il post del blog [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) (Limitazione dell'hub IoT).

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT](iot-hub-devguide-endpoints.md)
