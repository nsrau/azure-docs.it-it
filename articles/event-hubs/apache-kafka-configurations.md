---
title: Configurazioni consigliate per client Apache Kafka-Hub eventi di Azure
description: Questo articolo fornisce le configurazioni Apache Kafka consigliate per i client che interagiscono con hub eventi di Azure per Apache Kafka.
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: f9a03d1d3433461a575b32cd69893408a8b0ef97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097668"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Configurazioni consigliate per i client di Apache Kafka
Ecco le configurazioni consigliate per l'uso di hub eventi di Azure da Apache Kafka applicazioni client. 

## <a name="java-client-configuration-properties"></a>Proprietà di configurazione del client Java

### <a name="producer-and-consumer-configurations"></a>Configurazioni Producer e consumer

Proprietà | Valori consigliati | Intervallo consentito | Note
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (approssimativa) | < 240000 | Può essere abbassato per prelevare le modifiche dei metadati prima.
`connections.max.idle.ms`   | 180000 | < 240000 | Azure chiude l'inattività TCP in ingresso > 240.000 ms, che può comportare l'invio di connessioni inattive (visualizzate come batch scaduti a causa del timeout di invio).

### <a name="producer-configurations-only"></a>Solo configurazioni Producer
Le configurazioni del produttore sono disponibili [qui](https://kafka.apache.org/documentation/#producerconfigs).

Proprietà | Valori consigliati | Intervallo consentito | Note
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | Se vengono inviate richieste di dimensioni maggiori di 1.046.528 byte, il servizio chiuderà le connessioni.  *Questo valore **deve** essere modificato e causerà problemi in scenari con velocità effettiva elevata.*
`retries` | > 0 | | Potrebbe essere necessario aumentare il valore di delivery.timeout.ms. vedere la documentazione.
`request.timeout.ms` | 30000.. 60000 | > 20000| Per impostazione predefinita, il valore predefinito è pari a almeno 20.000 ms.  *Mentre le richieste con valori di timeout inferiori sono accettate, il comportamento del client non è garantito.*
`metadata.max.idle.ms` | 180000 | > 5000 | Controlla per quanto tempo il producer memorizza nella cache i metadati per un argomento inattivo. Se il tempo trascorso dall'ultimo prodotto di un argomento supera la durata di inattività dei metadati, i metadati dell'argomento vengono dimenticati e l'accesso successivo forza una richiesta di recupero dei metadati.
`linger.ms` | > 0 | | Per gli scenari con velocità effettiva elevata, il valore Linger deve essere uguale al valore massimo tollerabile per sfruttare i vantaggi dell'invio in batch.
`delivery.timeout.ms` | | | Impostare in base alla formula ( `request.timeout.ms`  +  `linger.ms` ) * `retries` .
`enable.idempotence` | false | | Idempotenza attualmente non supportato.
`compression.type` | `none` | | La compressione non è attualmente supportata.

### <a name="consumer-configurations-only"></a>Solo configurazioni utente
Le configurazioni del consumer sono disponibili [qui](https://kafka.apache.org/documentation/#consumerconfigs).

Proprietà | Valori consigliati | Intervallo consentito | Note
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 è il valore predefinito e non deve essere modificato.
`session.timeout.ms` | 30000 |6000.. 300000| Iniziare con 30000, aumentare se si osserva il ribilanciamento frequente a causa di heartbeat mancanti.


## <a name="librdkafka-configuration-properties"></a>Proprietà di configurazione di librdkafka
Il `librdkafka` file di configurazione principale ([collegamento](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) contiene descrizioni estese per le proprietà riportate di seguito.

### <a name="producer-and-consumer-configurations"></a>Configurazioni Producer e consumer

Proprietà | Valori consigliati | Intervallo consentito | Note
---|---:|-----:|---
`socket.keepalive.enable` | true | | Necessaria se si prevede che la connessione sia inattiva.  Azure chiuderà l'inattività TCP in ingresso > 240.000 ms.
`metadata.max.age.ms` | ~ 180000| < 240000 | Può essere abbassato per prelevare le modifiche dei metadati prima.

### <a name="producer-configurations-only"></a>Solo configurazioni Producer

Proprietà | Valori consigliati | Intervallo consentito | Note
---|---:|-----:|---
`retries` | > 0 | | Il valore predefinito è 2. È consigliabile usare questo valore. 
`request.timeout.ms` | 30000.. 60000 | > 20000| Per impostazione predefinita, il valore predefinito è pari a almeno 20.000 ms.  `librdkafka` il valore predefinito è 5000, che può risultare problematico. *Mentre le richieste con valori di timeout inferiori sono accettate, il comportamento del client non è garantito.*
`partitioner` | `consistent_random` | Vedere la documentazione di librdkafka | `consistent_random` è il valore predefinito e migliore.  Le chiavi vuote e null sono gestite idealmente nella maggior parte dei casi.
`enable.idempotence` | false | | Idempotenza attualmente non supportato.
`compression.codec` | `none` || La compressione non è attualmente supportata.

### <a name="consumer-configurations-only"></a>Solo configurazioni utente

Proprietà | Valori consigliati | Intervallo consentito | Note
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 è il valore predefinito e non deve essere modificato.
`session.timeout.ms` | 30000 |6000.. 300000| Iniziare con 30000, aumentare se si osserva il ribilanciamento frequente a causa di heartbeat mancanti.


## <a name="further-notes"></a>Ulteriori note

Controllare la tabella seguente di scenari comuni di errore correlati alla configurazione.

Sintomi | Problema | Soluzione
----|---|-----
Errori di commit offset a causa del ribilanciamento | Il consumer è in attesa troppo a lungo tra le chiamate al polling () e il servizio sta avviando il consumo fuori dal gruppo. | Sono disponibili diverse opzioni: <ul><li>aumenta il timeout della sessione</li><li>Riduci dimensioni batch messaggi per velocizzare l'elaborazione</li><li>migliorare la parallelizzazione di elaborazione per evitare il blocco di consumer. polling ()</li></ul> L'applicazione di una combinazione dei tre è probabilmente più saggia.
Eccezioni di rete con velocità effettiva elevata | Si sta usando Java client + default max. Request. size?  Le richieste potrebbero essere troppo grandi. | Vedere le configurazioni Java precedenti.

## <a name="next-steps"></a>Passaggi successivi
Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](..//azure-resource-manager/management/azure-subscription-service-limits.md) per le quote e i limiti di tutti i servizi Azure. 
