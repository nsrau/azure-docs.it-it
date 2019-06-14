---
title: Informazioni sulle quote e sulle limitazioni dell'hub IoT di Azure | Documentazione Microsoft
description: "Guida per gli sviluppatori: descrizione delle quote che si applicano all'hub IoT e del comportamento di limitazione previsto."
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.openlocfilehash: ea9bea8b314d00db87ad7addacc49a976e0da08e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550477"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Riferimento - Quote e limitazioni dell'hub IoT

## <a name="quotas-and-throttling"></a>Quote e limitazioni

Ogni sottoscrizione di Azure può avere al massimo 50 hub IoT e al massimo un hub gratuito.

Il provisioning di ogni hub IoT viene eseguito con un determinato numero di unità in un livello specifico. Il livello e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare. La dimensione dei messaggi usata per calcolare che la quota giornaliera è 0,5 kB per un hub di livello gratuito e 4 kB per tutti gli altri livelli. Per altre informazioni, vedere [Prezzi di Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

Il livello determina anche le limitazioni che l'hub IoT applica alle operazioni.

## <a name="operation-throttles"></a>Limitazioni per le operazioni

Limitazioni per le operazioni sono limitazioni di frequenza applicate negli intervalli al minuto e consentono di evitare abusi. Poiché sono soggetti a [conformazione del traffico](#traffic-shaping).

La tabella seguente mostra le limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Nessuna, B1 e S1 | B2 e S2 | B3 e S3 | 
| -------- | ------- | ------- | ------- |
| [Operazioni del registro delle identità](#identity-registry-operations-throttle) (creare, recuperare, elencare, aggiornare ed eliminare) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unit (5000/min/unità) |
| [Nuove connessioni del dispositivo](#device-connections-throttle) (questo limite si applica alla velocità del _nuove connessioni_, non il numero totale di connessioni) | Più di 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24 nuove connessioni/sec, ma si hanno almeno 100 nuove conessioni/sec tra le unità. Con nove unità S1 si otterranno 108 nuove connessioni/sec (9\*12) tra le unità. | 120 nuove connessioni/sec/unità | nuove connessioni/sec/unità 6.000 |
| Inoltri dal dispositivo al cloud | Più di 100/sec o 12/sec/unità <br/> Ad esempio, due unità S1 sono 2\*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9\*12) tra le unità. | 120/sec/unità | 6\.000/sec/unità |
| Inoltri dal cloud al dispositivo<sup>1</sup> | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unit (5000/min/unità) |
| Ricezioni dal cloud al dispositivo<sup>1</sup> <br/> (solo quando il dispositivo usa HTTPS)| 16.67/sec/unit (1000/min/unità) | 16.67/sec/unit (1000/min/unità) | 833.33/sec/unit (50.000/min/unità) |
| Caricamento di file | 1,67 notifice caricamento file/sec/unità (100/min/unità) | 1,67 notifice caricamento file/sec/unità (100/min/unità) | caricamento delle notifiche/sec/unità file 83,33 (5000/min/unità) |
| Metodi diretti<sup>1</sup> | 160 KB/sec/unità<sup>2</sup> | 480 KB/sec/unità<sup>2</sup> | 24 MB/sec/unità<sup>2</sup> | 
| Query | 20/min/unità | 20/min/unità | 1000/min/unità |
| Letture di dispositivi e moduli gemelli <sup>1</sup> | 100/sec | Più di 100/sec o 10/sec/unità | 500/sec/unità |
| Aggiornamenti di dispositivi e moduli gemelli <sup>1</sup> | 50/sec | Più di 50/sec o 5/sec/unità | 250/sec/unità |
| Operazioni dei processi<sup>1</sup> <br/> (creazione, aggiornamento, elenco, eliminazione) | 1,67/sec/unità (100/min/unità) | 1,67/sec/unità (100/min/unità) | 83.33/sec/unit (5000/min/unità) |
| Operazioni dei dispositivi dei processi<sup>1</sup> <br/> (aggiornamento del dispositivo gemello, chiamata del metodo diretto) | 10/sec | Più di 10/sec o 1/sec/unità | 50/sec/unità |
| Configurazioni e distribuzioni Edge<sup>1</sup> <br/> (creazione, aggiornamento, elenco, eliminazione) | 0,33/sec/unità (20/min/unità) | 0,33/sec/unità (20/min/unità) | 0,33/sec/unità (20/min/unità) |
| Velocità di avvio del flusso dispositivo<sup>1</sup> | 5 nuovi flussi/sec | 5 nuovi flussi/sec | 5 nuovi flussi/sec |
| Numero massimo di dispositivi connessi contemporaneamente flussi<sup>1</sup> | 50 | 50 | 50 |
| Trasferimento dei dati di flusso massima del dispositivo<sup>1</sup> (aggregare volume al giorno) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>Questa funzionalità non è disponibile nel livello di base dell'hub IoT. Per altre informazioni, vedere [How to choose the right IoT Hub](iot-hub-scaling.md) (Come scegliere il livello dell'hub IoT corretto). <br/><sup>2</sup>la limitazione della dimensione del contatore è di 4 KB.

### <a name="traffic-shaping"></a>Conformazione del traffico

Per consentire il traffico di picco, l'IoT Hub accetta le richieste di sopra la limitazione per un periodo di tempo limitato. Le prime di queste richieste vengono elaborate immediatamente. Tuttavia, se il numero di richieste persiste violare la limitazione, l'IoT Hub viene avviato inserendo le richieste in una coda ed elaborati alla tariffa di limite. Viene chiamato questo effetto *conformazione del traffico*. Inoltre, le dimensioni di questa coda sono limitata. Se la violazione di limite continua, alla fine la coda si riempie, e viene avviato l'IoT Hub rifiuta le richieste con `429 ThrottlingException`. 

Ad esempio, si usa un dispositivo simulato per inviare messaggi da dispositivo a cloud 200 al secondo all'IoT Hub S1 (che ha un limite delle trasmissioni D2C 100/sec). Per il primo minuto o due, i messaggi vengono elaborati immediatamente. Tuttavia, poiché il dispositivo continua a inviare più messaggi di quanti il limite di velocità, l'IoT Hub inizia a elaborare solo 100 messaggi al secondo e inserisce il resto in una coda. Si avvia notando un aumento della latenza. Infine, iniziare a ottenere `429 ThrottlingException` come la coda si riempie e il "numero di errori di limitazione" nella [le metriche dell'IoT Hub](iot-hub-metrics.md) inizia ad aumentare.

### <a name="identity-registry-operations-throttle"></a>Limitare le operazioni del Registro di sistema delle identità

Operazioni del registro delle identità dispositivo sono deve essere utilizzato in fase di esecuzione in Gestione dei dispositivi e gli scenari di provisioning. L'operazione di lettura o aggiornamento di un numero elevato di identità dei dispositivi è supportata tramite i [processi di importazione ed esportazione](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Limitare le connessioni ai dispositivi

La limitazione delle *connessioni del dispositivo* determina la frequenza con cui possono essere stabilite nuove connessioni del dispositivo con un hub IoT. La limitazione delle *connessioni del dispositivo* non determina il numero massimo di dispositivi connessi contemporaneamente. La limitazione di frequenza delle *connessioni del dispositivo* dipende dal numero di unità di cui viene effettuato il provisioning per l'hub IoT.

Ad esempio, se si acquista una singola unità S1, si ottiene un limite di 100 connessioni al secondo. Pertanto, per connettere 100, 000 dispositivi, sono necessari almeno 1000 secondi (circa 16 minuti). Tuttavia, è consentito un numero di dispositivi connessi simultaneamente pari al numero di dispositivi registrati nel registro delle identità.


## <a name="other-limits"></a>Altri limiti

L'hub IoT applica altri limiti operativi:

| Operazione | Limite |
| --------- | ----- |
| Dispositivi | Il numero massimo di dispositivi che è possibile connettersi a un singolo hub IoT è pari a 1.000.000. L'unico modo per aumentare questo limite è contattare [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).| 
| URI per il caricamento di file | 10\.000 URI di firma di accesso condiviso possono essere generati per un account di archiviazione in una sola volta. <br/> 10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Processi<sup>1</sup> | Numero massimo di processi simultaneo è 1 (per il livello gratuito e S1), 5 (per S2) e 10 (per S3). Tuttavia, max simultaneo [processi di importazione/esportazione di dispositivo](iot-hub-bulk-identity-mgmt.md) è 1 per tutti i livelli. <br/>Cronologia dei processi verrà conservata fino a 30 giorni. |
| Altri endpoint | Agli hub SKU a pagamento possono essere associati 10 endpoint aggiuntivi. Agli hub SKU gratuiti può essere associato solo un endpoint aggiuntivo. |
| Regole di routing dei messaggi | Agli hub SKU a pagamento possono essere associate 100 regole di routing. Agli hub SKU gratuiti possono essere associate cinque regole di routing. |
| Messaggistica da dispositivo a cloud | Dimensioni massime dei messaggi 256 KB |
| Messaggistica da cloud a dispositivo<sup>1</sup> | La dimensione massima dei messaggi è 64 KB. Il numero massimo di messaggi in sospeso è 50. |
| Metodo diretto<sup>1</sup> | La dimensione massima del payload del metodo diretto è 128 KB. |
| Configurazioni automatiche dei dispositivi<sup>1</sup> | 100 configurazioni per hub SKU a pagamento. 20 configurazioni per hub SKU gratuito. |
| Distribuzioni Edge automatiche<sup>1</sup> | 20 moduli per distribuzione. 100 distribuzioni per hub SKU a pagamento. 20 distribuzioni per hub SKU gratuito. |
| Dispositivi gemelli<sup>1</sup> | Le dimensioni massime per ogni sezione dell'elemento gemello (tag, proprietà desiderate, proprietà segnalate) sono pari a 8 kB |

<sup>1</sup>Questa funzionalità non è disponibile nel livello di base dell'hub IoT. Per altre informazioni, vedere [How to choose the right IoT Hub](iot-hub-scaling.md) (Come scegliere il livello dell'hub IoT corretto).

## <a name="increasing-the-quota-or-throttle-limit"></a>L'aumento del limite di quota o limitazione

In qualsiasi momento, è possibile aumentare le quote o limiti da limitare [aumentando il numero di unità sottoposte a provisioning in un hub IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Latenza

L'hub IoT punta a fornire bassa latenza per tutte le operazioni. Tuttavia, a causa delle condizioni della rete e altri fattori imprevedibili non può garantire una latenza di determinati. Quando si progetta la soluzione, è necessario:

* Evitare di fare ipotesi sulla latenza massima di qualsiasi operazione dell'hub IoT.
* Eseguire il provisioning dell'hub IoT nell'area di Azure più vicina ai dispositivi.
* Considerare l'eventualità di usare Azure IoT Edge per eseguire operazioni sensibili alla latenza sul dispositivo o su un gateway vicino al dispositivo.

Più unità dell'hub IoT influiscono sulla limitazione come descritto in precedenza, ma non forniscono alcuna prestazione di latenza aggiuntiva o garanzia.

In caso di incremento imprevisto della latenza dell'operazione, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passaggi successivi

Per un'analisi approfondita del comportamento della limitazione dell'hub IoT, vedere il post di blog [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/) (Limitazione dell'hub IoT).

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint hub IoT](iot-hub-devguide-endpoints.md)
