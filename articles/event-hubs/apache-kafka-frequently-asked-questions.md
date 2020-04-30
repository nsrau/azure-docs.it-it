---
title: Domande frequenti-Hub eventi di Azure per Apache Kafka
description: Questo articolo illustra in che modo i consumer e i producer che usano protocolli diversi (AMQP, Apache Kafka e HTTPS) possono scambiare eventi quando usano Hub eventi di Azure.
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
ms.openlocfilehash: 0186b90e1d75c5dba6e1ca26e4ba079a3456cea4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606743"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Domande frequenti-Hub eventi per Apache Kafka 
Questo articolo fornisce le risposte ad alcune delle domande frequenti sulla migrazione a hub eventi per Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Eseguire Apache Kafka?

No.  Vengono eseguite le operazioni dell'API Kafka nell'infrastruttura di hub eventi.  Poiché esiste una stretta correlazione tra Apache Kafka e la funzionalità AMQP di hub eventi (ovvero produzione, ricezione, gestione e così via), è possibile ottenere l'affidabilità nota degli hub eventi nello spazio PaaS di Kafka.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Gruppo di consumer di hub eventi e gruppo di consumer Kafka
Qual è la differenza tra un gruppo di consumer di hub eventi e un gruppo di consumer Kafka negli hub eventi? I gruppi di consumer Kafka negli hub eventi sono completamente distinti dai gruppi di consumer di hub eventi standard.

**Gruppi di consumer di hub eventi**

- Sono gestite con operazioni di creazione, recupero, aggiornamento ed eliminazione (CRUD) tramite il portale, l'SDK o i modelli Azure Resource Manager. I gruppi di consumer di hub eventi non possono essere creati in modo autocreato.
- Si tratta di entità figlio di un hub eventi. Significa che lo stesso nome del gruppo di consumer può essere riusato tra hub eventi nello stesso spazio dei nomi perché si tratta di entità separate.
- Non vengono utilizzati per l'archiviazione degli offset. Il consumo di AMQP orchestrato viene eseguito usando l'archiviazione offset esterna, ad esempio archiviazione di Azure.

**Gruppi di consumer Kafka**

- Vengono creati in un momento.  I gruppi Kafka possono essere gestiti tramite le API del gruppo di consumer Kafka.
- Possono archiviare gli offset nel servizio Hub eventi.
- Vengono usati come chiavi in un archivio chiave-valore di offset. Per una coppia univoca `group.id` di `topic-partition`e, viene archiviato un offset in archiviazione di Azure (replica 3x). Gli utenti di hub eventi non incorrono costi di archiviazione aggiuntivi dall'archiviazione degli offset Kafka. Gli offset sono manipolabile tramite le API del gruppo di consumer Kafka, ma gli *account* di archiviazione offset non sono direttamente visibili o manipolabile per gli utenti dell'hub eventi.  
- Si estendono su uno spazio dei nomi. L'uso dello stesso nome di gruppo Kafka per più applicazioni in più argomenti significa che tutte le applicazioni e i relativi client Kafka verranno ribilanciati ogni volta che è necessario ribilanciare una sola applicazione.  Scegliere i nomi dei gruppi in modo oculato.
- Sono completamente distinti dai gruppi di consumer di hub eventi. **Non** è necessario usare ' $default ' né preoccuparsi dei client Kafka che interferiscono con i carichi di lavoro AMQP.
- Non sono visualizzabili nella portale di Azure. Le informazioni sul gruppo di consumer sono accessibili tramite le API Kafka.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida per gli sviluppatori Apache Kafka per hub eventi](apache-kafka-developer-guide.md)
- [Guida alla migrazione di Apache Kafka per hub eventi](apache-kafka-migration-guide.md)
- [Guida alla risoluzione dei problemi di Apache Kafka per hub eventi](apache-kafka-troubleshooting-guide.md)
- [Configurazioni consigliate](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

