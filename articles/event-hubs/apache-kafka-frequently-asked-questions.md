---
title: Domande frequenti-Hub eventi di Azure per Apache Kafka
description: Questo articolo risponde a domande frequenti sul supporto di hub eventi di Azure per Apache Kafka client non coperti altrove.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ca54bf314d795b88b727ddb648f3e1e74133fd3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061462"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Domande frequenti-Hub eventi per Apache Kafka 
Questo articolo fornisce le risposte ad alcune delle domande frequenti sulla migrazione a hub eventi per Apache Kafka.

## <a name="does-azure-event-hubs-run-on-apache-kafka"></a>Hub eventi di Azure viene eseguito in Apache Kafka?

No. Hub eventi di Azure è un broker a più livelli nativo del cloud con supporto per più protocolli sviluppati e gestiti da Microsoft e non usa codice Apache Kafka. Uno dei protocolli supportati è il protocollo RPC Kafka per le API consumer e producer del client Kafka. Hub eventi funziona con molte applicazioni Kafka esistenti. Per ulteriori informazioni, vedere [Hub eventi per Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). Poiché i concetti di Apache Kafka e di hub eventi di Azure sono molto simili, ma non identici, è possibile offrire agli utenti l'affidabilità senza corrispondenza degli hub eventi di Azure con investimenti Apache Kafka esistenti. 

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Gruppo di consumer di hub eventi e gruppo di consumer Kafka
Qual è la differenza tra un gruppo di consumer di hub eventi e un gruppo di consumer Kafka negli hub eventi? I gruppi di consumer Kafka negli hub eventi sono completamente distinti dai gruppi di consumer di hub eventi standard.

**Gruppi di consumer di hub eventi**

- Sono gestite con operazioni di creazione, recupero, aggiornamento ed eliminazione (CRUD) tramite il portale, l'SDK o i modelli Azure Resource Manager. I gruppi di consumer di hub eventi non possono essere creati in modo autocreato.
- Si tratta di entità figlio di un hub eventi. Significa che lo stesso nome del gruppo di consumer può essere riusato tra hub eventi nello stesso spazio dei nomi perché si tratta di entità separate.
- Non vengono utilizzati per l'archiviazione degli offset. Il consumo di AMQP orchestrato viene eseguito usando l'archiviazione offset esterna, ad esempio archiviazione di Azure.

**Gruppi di consumer Kafka**

- Vengono creati in un momento.  I gruppi Kafka possono essere gestiti tramite le API del gruppo di consumer Kafka.
- Possono archiviare gli offset nel servizio Hub eventi.
- Vengono usati come chiavi in un archivio chiave-valore di offset. Per una coppia univoca di `group.id` e `topic-partition` , viene archiviato un offset in archiviazione di Azure (replica 3x). Gli utenti di hub eventi non incorrono costi di archiviazione aggiuntivi dall'archiviazione degli offset Kafka. Gli offset sono manipolabile tramite le API del gruppo di consumer Kafka, ma gli *account* di archiviazione offset non sono direttamente visibili o manipolabile per gli utenti dell'hub eventi.  
- Si estendono su uno spazio dei nomi. L'uso dello stesso nome di gruppo Kafka per più applicazioni in più argomenti significa che tutte le applicazioni e i relativi client Kafka verranno ribilanciati ogni volta che è necessario ribilanciare una sola applicazione.  Scegliere i nomi dei gruppi in modo oculato.
- Sono completamente distinti dai gruppi di consumer di hub eventi. **Non** è necessario usare ' $default ' né preoccuparsi dei client Kafka che interferiscono con i carichi di lavoro AMQP.
- Non sono visualizzabili nella portale di Azure. Le informazioni sul gruppo di consumer sono accessibili tramite le API Kafka.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida per gli sviluppatori Apache Kafka per hub eventi](apache-kafka-developer-guide.md)
- [Guida alla migrazione di Apache Kafka per hub eventi](apache-kafka-migration-guide.md)
- [Guida alla risoluzione dei problemi di Apache Kafka per hub eventi](apache-kafka-troubleshooting-guide.md)
- [Configurazioni consigliate](apache-kafka-configurations.md)

