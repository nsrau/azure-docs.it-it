---
title: Risolvere i problemi con hub eventi di Azure per Apache KafkaTroubleshoot issues with Azure Event Hubs for Apache Kafka
description: Questo articolo illustra come risolvere i problemi con Hub eventi di Azure per Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606730"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guida alla risoluzione dei problemi di Apache Kafka per gli hub eventi
In questo articolo vengono forniti suggerimenti per la risoluzione dei problemi che possono verificarsi quando si utilizzano hub eventi per Apache Kafka. 

## <a name="server-busy-exception"></a>Eccezione Server occupato
È possibile ricevere l'eccezione Server Occupato a causa della limitazione Kafka. Con i client AMQP, Hub eventi restituisce immediatamente **un'eccezione Server occupato** al momento della limitazione del servizio. È equivalente a un messaggio "riprova più tardi". In Kafka, i messaggi vengono ritardati prima di essere completati. La lunghezza del ritardo viene `throttle_time_ms` restituita in millisecondi come nella risposta produce/fetch. Nella maggior parte dei casi, queste richieste ritardate non vengono registrate come eccezioni ServerBusy nei dashboard di Hub eventi. Al contrario, `throttle_time_ms` il valore della risposta deve essere utilizzato come indicatore che la velocità effettiva ha superato la quota di provisioning.

Se il traffico è eccessivo, il servizio ha il seguente comportamento:

- Se il ritardo della richiesta produce supera il timeout della richiesta, Hub eventi restituisce il codice di errore **Violazione della modifica.**
- Se il ritardo della richiesta di recupero supera il timeout della richiesta, Event Hubs registra la richiesta come limitata e risponde con un set vuoto di record e senza codice di errore.

[I cluster dedicati](event-hubs-dedicated-overview.md) non dispongono di meccanismi di limitazione delle richieste. È possibile utilizzare tutte le risorse del cluster.

## <a name="no-records-received"></a>Nessun record ricevuto
Potresti vedere i consumatori non ricevere alcun record e ribilanciare costantemente. In questo scenario, gli utenti non ottengono alcun record e ribilanciano costantemente. Non ci sono eccezioni o errori quando si verifica, ma i registri Kafka mostrerà che i consumer sono bloccati cercando di riconnettersi al gruppo e assegnare partizioni. Ci sono alcune possibili cause:

- Assicurarsi che `request.timeout.ms` il valore consigliato sia almeno 60000 e `session.timeout.ms` il valore consigliato di 30000. La presenza di queste impostazioni troppo basse potrebbe causare timeout dei consumer, che causano quindi ribilanciamenti (che causano più timeout, che causano un maggiore ribilanciamento e così via) 
- Se la configurazione corrisponde a tali valori consigliati e il ribilanciamento costante continua è necessario, è possibile aprire un problema (assicurarsi di includere l'intera configurazione nel problema in modo che sia possibile eseguire il debug)!

## <a name="compressionmessage-format-version-issue"></a>Problema di versione del formato compressione/messaggio
Kafka supporta la compressione e Event Hubs for Kafka attualmente no. Gli errori che menzionano una versione `The message format version on the broker does not support the request.`in formato messaggio (ad esempio, ) sono causati quando un client tenta di inviare messaggi Kafka compressi ai nostri broker.

Se sono necessari dati compressi, la compressione dei dati prima di inviarli ai broker e decompressi dopo la ricezione è una soluzione valida. Il corpo del messaggio è solo una matrice di byte per il servizio, pertanto la compressione/decompressione lato client non causerà problemi.

## <a name="unknownserverexception"></a>UnknownServerException (eccezione per server sconosciuto)
È possibile ricevere un UnknownServerException dalle librerie client Kafka simile all'esempio seguente: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Apri un ticket con il supporto Microsoft.  La registrazione a livello di debug e i timestamp delle eccezioni in UTC sono utili per il debug del problema. 

## <a name="other-issues"></a>Altri problemi
Controllare gli elementi seguenti se si verificano problemi durante l'utilizzo di Kafka negli hub eventi.

- Traffico di **blocco del firewall:** assicurarsi che la porta **9093** non sia bloccata dal firewall.
- **TopicAuthorizationException** - Le cause più comuni di questa eccezione sono:TopicAuthorizationException - The most common causes of this exception are:
    - Un errore di battitura nella stringa di connessione nel file di configurazione oppure
    - Tentativo di utilizzare Hub eventi per Kafka in uno spazio dei nomi di livello Basic.Trying to use Event Hubs for Kafka on a Basic tier namespace. Gli hub eventi per Kafka sono supportati solo per gli spazi dei nomi standard e del [livello dedicato.](https://azure.microsoft.com/pricing/details/event-hubs/)
- **Kafka versione mismatch** - Event Hubs for Kafka Ecosystems supporta Kafka versioni 1.0 e successive. Alcune applicazioni che usano Kafka versione 0.10 e successive potrebbero occasionalmente funzionare a causa della compatibilità con le versioni precedenti del protocollo Kafka, ma è consigliabile non usare versioni API precedenti. Kafka versioni 0.9 e precedenti non supportano i protocolli SASL necessari e non possono connettersi agli hub eventi.
- **Strane codifiche sulle intestazioni AMQP quando si utilizza con Kafka:** quando si inviano eventi a un hub eventi tramite AMQP, tutte le intestazioni di payload AMQP vengono serializzate nella codifica AMQP. I consumer Kafka non deserializzano le intestazioni da AMQP. Per leggere i valori delle intestazioni, decodificare manualmente le intestazioni AMQP. In alternativa, è possibile evitare di utilizzare le intestazioni AMQP se si sa che si sta consumando tramite il protocollo Kafka. Per altre informazioni, vedere [questo problema in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Autenticazione SASL:** ottenere la collaborazione del framework con il protocollo di autenticazione SASL richiesto dagli hub eventi può essere più difficile di quanto soddisfi l'occhio. Verificare se è possibile risolvere i problemi di configurazione usando le risorse del framework sull'autenticazione SASL. 

## <a name="limits"></a>Limiti
Apache Kafka vs. Event Hubs Kafka. Per la maggior parte, gli hub eventi per gli ecosistemi Kafka hanno le stesse impostazioni predefinite, proprietà, codici di errore e comportamento generale di Apache Kafka. Le istanze in cui questi due differiscono in modo esplicito (o in cui Hub eventi impone un limite diverso da Kafka) sono elencate di seguito:

- La lunghezza massima `group.id` della proprietà è di 256 caratteri
- La dimensione `offset.metadata.max.bytes` massima di è 1024 byte
- Offset commits are throttled at 4 calls/second per partition with a max internal log size of 1 MB


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida per gli sviluppatori di Apache Kafka per gli hub eventi](apache-kafka-developer-guide.md)
- [Guida alla migrazione di Apache Kafka per gli hub eventi](apache-kafka-migration-guide.md)
- [Domande frequenti - Hub di eventi per Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Configurazioni consigliate](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
