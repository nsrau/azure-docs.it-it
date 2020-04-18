---
title: Domande frequenti - Hub di eventi di Azure per Apache Kafka
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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606743"
---
# <a name="frequently-asked-questions---event-hubs-for-apache-kafka"></a>Domande frequenti - Hub di eventi per Apache Kafka 
Questo articolo fornisce le risposte ad alcune delle domande frequenti sulla migrazione agli hub eventi per Apache Kafka.

## <a name="do-you-run-apache-kafka"></a>Gestite Apache Kafka?

No.  Eseguiamo operazioni API Kafka sull'infrastruttura Event Hubs.  Poiché esiste una stretta correlazione tra la funzionalità Apache Kafka e Event Hubs AMQP (ovvero, produrre, ricevere, gestire, così via), siamo in grado di portare l'affidabilità nota degli hub di eventi nello spazio Kafka PaaS.

## <a name="event-hubs-consumer-group-vs-kafka-consumer-group"></a>Gruppo di consumer Event Hubs e gruppo di consumer Kafka
Qual è la differenza tra un gruppo di consumer Event Hub e un gruppo di consumer Kafka negli hub eventi? I gruppi di consumer Kafka negli hub eventi sono completamente distinti dai gruppi di consumer standard degli hub eventi.

**Gruppi di consumer di Hub eventi**

- Vengono gestiti con le operazioni di creazione, recupero, aggiornamento ed eliminazione (CRUD) tramite modelli di portale, SDK o Azure Resource Manager.They are Managed with create, retrieve, update, and delete (CRUD) operations via portal, SDK, or Azure Resource Manager templates. I gruppi di consumer hub eventi non possono essere creati automaticamente.
- Sono entità figlio di un hub eventi. Significa che lo stesso nome di gruppo di consumer può essere riutilizzato tra hub eventi nello stesso spazio dei nomi perché sono entità separate.
- Non vengono utilizzati per l'archiviazione degli offset. L'utilizzo di AMQP orchestrato viene eseguito usando l'archiviazione offset esterna, ad esempio Archiviazione di Azure.Orchestrated AMQP consumption is done using external offset storage, for example, Azure Storage.

**Gruppi di consumatori kafka**

- Sono creati automaticamente.  I gruppi Kafka possono essere gestiti tramite le API del gruppo di consumatori Kafka.
- Possono archiviare gli offset nel servizio Hub eventi.
- Vengono utilizzati come chiavi in quello che è effettivamente un archivio chiave-valore offset. Per una coppia `group.id` `topic-partition`univoca di e , viene archiviato un offset in Archiviazione di Azure (replica 3x). Gli utenti di Hub eventi non comportano costi di archiviazione aggiuntivi derivanti dall'archiviazione degli offset Kafka. Gli scostamenti sono manipolabili tramite le API del gruppo di consumer Kafka, ma gli *account* di archiviazione di offset non sono direttamente visibili o manipolabili per gli utenti dell'hub eventi.  
- Si estendono su uno spazio dei nomi. L'utilizzo dello stesso nome di gruppo Kafka per più applicazioni su più argomenti significa che tutte le applicazioni e i relativi client Kafka verranno ribilanciati ogni volta che solo una singola applicazione deve essere ribilanciata.  Scegli saggiamente i nomi dei tuoi gruppi.
- Sono completamente distinti dai gruppi di consumer di Hub eventi. **Non è** necessario utilizzare '$Default', né è necessario preoccuparsi che i client Kafka interferiscano con i carichi di lavoro AMQP.
- Non sono visualizzabili nel portale di Azure.They aren't viewable in the Azure portal. Le informazioni sui gruppi di consumatori sono accessibili tramite le API Kafka.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi e Hub eventi per Kafka, vedere gli articoli seguenti:  

- [Guida per gli sviluppatori di Apache Kafka per gli hub eventi](apache-kafka-developer-guide.md)
- [Guida alla migrazione di Apache Kafka per gli hub eventi](apache-kafka-migration-guide.md)
- [Guida alla risoluzione dei problemi di Apache Kafka per gli hub eventi](apache-kafka-troubleshooting-guide.md)
- [Configurazioni consigliate](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)

