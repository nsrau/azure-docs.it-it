---
title: Risolvere i problemi relativi a hub eventi di Azure per Apache Kafka
description: Questo articolo illustra come risolvere i problemi relativi a hub eventi di Azure per Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: c2403fd51729ef8809b9a70383ad6f9fd91e52b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322688"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Guida alla risoluzione dei problemi di Apache Kafka per hub eventi
Questo articolo fornisce suggerimenti per la risoluzione dei problemi che possono verificarsi quando si usano Hub eventi per Apache Kafka. 

## <a name="server-busy-exception"></a>Eccezione server occupato
È possibile che venga generata un'eccezione di server occupato a causa della limitazione Kafka. Con i client AMQP, Hub eventi restituisce immediatamente un'eccezione **Server occupato** al momento della limitazione del servizio. Equivale a un messaggio "Riprova più tardi". In Kafka i messaggi vengono posticipati prima del completamento. La lunghezza del ritardo viene restituita in millisecondi come `throttle_time_ms` nella risposta di produzione/recupero. Nella maggior parte dei casi, queste richieste ritardate non vengono registrate come eccezioni ServerBusy nei dashboard di hub eventi. Al contrario, il valore della risposta `throttle_time_ms` deve essere usato come indicatore che la velocità effettiva ha superato la quota di cui è stato effettuato il provisioning.

Se il traffico è eccessivo, il servizio presenta il comportamento seguente:

- Se il ritardo della richiesta di produzione supera il timeout della richiesta, Hub eventi restituisce il codice di errore di **violazione dei criteri** .
- Se il ritardo della richiesta di recupero supera il timeout della richiesta, Hub eventi registra la richiesta come limitata e risponde con un set di record vuoto e nessun codice di errore.

I [cluster dedicati](event-hubs-dedicated-overview.md) non hanno meccanismi di limitazione delle richieste. Si è liberi di utilizzare tutte le risorse del cluster.

## <a name="no-records-received"></a>Nessun record ricevuto
È possibile che gli utenti non possano ottenere record e ribilanciarli costantemente. In questo scenario, i consumer non ottengono record e si ribilanciano costantemente. Non vi sono eccezioni o errori quando si verificano, ma i registri Kafka indicheranno che i consumer sono bloccati tentando di partecipare di nuovo al gruppo e di assegnare le partizioni. Esistono alcune possibili cause:

- Verificare che sia almeno `request.timeout.ms` il valore consigliato di 60000 e che `session.timeout.ms` sia almeno il valore consigliato di 30000. La presenza di queste impostazioni può causare timeout dei consumer, che quindi provocano i ribilanciamenti (che quindi generano più timeout, che provocano un maggiore ribilanciamento e così via) 
- Se la configurazione corrisponde ai valori consigliati e si continua a visualizzare il ribilanciamento costante, è possibile aprire un problema (assicurarsi di includere l'intera configurazione nel problema per consentire il debug).

## <a name="compressionmessage-format-version-issue"></a>Problema relativo alla versione del formato di compressione/messaggio
Kafka supporta attualmente la compressione e hub eventi per Kafka. Gli errori che citano una versione del formato del messaggio (ad esempio, `The message format version on the broker does not support the request.` ) vengono causati quando un client tenta di inviare messaggi Kafka compressi ai broker.

Se i dati compressi sono necessari, la compressione dei dati prima di inviarli ai broker e la decompressione dopo la ricezione costituisce una soluzione alternativa valida. Il corpo del messaggio è semplicemente una matrice di byte per il servizio, quindi la compressione/decompressione lato client non provocherà alcun problema.

## <a name="unknownserverexception"></a>UnknownServerException
È possibile ricevere un UnknownServerException da librerie client Kafka simili all'esempio seguente: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Aprire un ticket con il supporto tecnico Microsoft.  La registrazione a livello di debug e i timestamp delle eccezioni in formato UTC sono utili per il debug del problema. 

## <a name="other-issues"></a>Altri problemi
Se si riscontrano problemi durante l'uso di Kafka negli hub eventi, controllare gli elementi seguenti.

- **Traffico di blocco del firewall** : assicurarsi che la porta **9093** non sia bloccata dal firewall.
- **TopicAuthorizationException** : di seguito sono riportate le cause più comuni di questa eccezione:
    - Errore di digitazione nella stringa di connessione nel file di configurazione o
    - Tentativo di usare hub eventi per Kafka in uno spazio dei nomi di livello Basic. Hub eventi per Kafka è [supportato solo per gli spazi dei nomi di livello standard e dedicato](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Versione di Kafka non corrispondente** : gli hub eventi per gli ecosistemi Kafka supportano le versioni 1,0 e successive di Kafka. Alcune applicazioni che usano Kafka versione 0,10 e successive potrebbero talvolta funzionare a causa della compatibilità con le versioni precedenti del protocollo Kafka, ma è consigliabile evitare di usare le versioni precedenti dell'API. Kafka versioni 0,9 e versioni precedenti non supportano i protocolli SASL richiesti e non possono connettersi a hub eventi.
- **Codifiche strane nelle intestazioni AMQP quando** si utilizza Kafka: quando si inviano eventi a un hub eventi tramite AMQP, tutte le intestazioni di payload AMQP vengono serializzate nella codifica AMQP. I consumer Kafka non deserializzano le intestazioni da AMQP. Per leggere i valori di intestazione, decodificare manualmente le intestazioni AMQP. In alternativa, è possibile evitare di usare le intestazioni AMQP se si è certi che si utilizzerà tramite il protocollo Kafka. Per altre informazioni, vedere [questo problema di GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Autenticazione SASL** : il Framework per collaborare con il protocollo di autenticazione SASL richiesto dagli hub di eventi può essere più difficile rispetto a quello che soddisfa l'occhio. Vedere se è possibile risolvere i problemi di configurazione usando le risorse del Framework nell'autenticazione SASL. 

## <a name="limits"></a>Limiti
Apache Kafka rispetto a hub eventi Kafka. Nella maggior parte dei casi, gli hub eventi per gli ecosistemi Kafka hanno gli stessi valori predefiniti, le stesse proprietà, i codici di errore e il comportamento generale che Apache Kafka. Le istanze in cui queste due differenze sono esplicitamente (o in cui Hub eventi impone un limite a Kafka non) sono elencate di seguito:

- La lunghezza massima della `group.id` proprietà è di 256 caratteri
- La dimensione massima di `offset.metadata.max.bytes` è 1024 byte
- I commit offset sono limitati a 4 chiamate al secondo per partizione con una dimensione massima del log interno di 1 MB


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida per gli sviluppatori Apache Kafka per hub eventi](apache-kafka-developer-guide.md)
- [Guida alla migrazione di Apache Kafka per hub eventi](apache-kafka-migration-guide.md)
- [Domande frequenti-Hub eventi per Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Configurazioni consigliate](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
