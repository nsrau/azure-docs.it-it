---
title: Hub eventi di Azure per Apache Kafka | Microsoft Docs
description: Panoramica e introduzione a Hub eventi di Azure con supporto per Kafka
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/16/2018
ms.author: bahariri
ms.openlocfilehash: b1a046fdb62926d32c05bab5a1616b250911865b
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49352574"
---
# <a name="azure-event-hubs-for-apache-kafka-preview"></a>Hub eventi di Azure per Apache Kafka (anteprima)

Hub eventi fornisce un endpoint Kafka che può essere usato dalle applicazioni Kafka esistenti come alternativa all'esecuzione del proprio cluster Kafka. Hub eventi supporta il [protocollo Apache Kafka 1.0 e versioni successive](https://kafka.apache.org/documentation/) e funziona con le applicazioni Kafka esistenti, tra cui MirrorMaker. 

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vantaggi di Hub eventi per Kafka

Hub eventi per la funzionalità di Kafka fornisce un head di protocollo basato su Hub eventi di Azure con compatibilità binaria con le versioni di Kafka 1.0 e successive per la lettura e la scrittura negli argomenti Kafka. È possibile iniziare a usare l'endpoint di Kafka dalle applicazioni senza alcuna modifica del codice, ma con una modifica minima della configurazione. Aggiornare la stringa di connessione nelle configurazioni in modo che punti all'endpoint di Kafka esposto dall'hub eventi invece di puntare al cluster Kafka. È quindi possibile iniziare a trasmettere gli eventi dalle applicazioni che usano il protocollo Kafka negli Hub eventi. 

Concettualmente, Kafka e Hub eventi sono quasi identici: sono entrambi log partizionati creati per lo streaming dei dati. Nella tabella seguente vengono associati i concetti tra Kafka e Hub eventi.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Associazione di concetti tra Kafka e Hub eventi

| Concetto di Kafka | Concetto di Hub eventi|
| --- | --- |
| HDInsight | Spazio dei nomi |
| Argomento | Hub eventi |
| Partition | Partition|
| Gruppo di consumer | Gruppo di consumer |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Differenze principali tra Kafka e Hub eventi

Mentre [Apache Kafka](https://kafka.apache.org/) è un software, che è possibile eseguire ogni volta che si vuole, Hub eventi è un servizio cloud simile ad Archiviazione BLOB di Azure. Non ci sono server o reti da gestire, né broker da configurare. Si crea uno spazio dei nomi, vale a dire un nome di dominio completo in cui sono presenti gli argomenti, e quindi si creano gli hub eventi o gli argomenti all'interno di tale spazio dei nomi. Per altre informazioni su Hub eventi e sugli spazi dei nomi, vedere [Funzionalità di Hub eventi?](event-hubs-features.md#namespace). In quanto servizio cloud, Hub eventi usa un singolo indirizzo IP virtuale stabile come endpoint, in modo che i client non debbano necessariamente disporre di informazioni sui broker o sui computer all'interno di un cluster. 

Il numero di istanze di Hub eventi è determinato dal numero di unità elaborate acquistate, dove ogni unità elaborata dà diritto a 1 MB al secondo, o 1000 eventi al secondo in ingresso. Per impostazione predefinita, Hub eventi ridimensiona il numero di unità elaborate quando si raggiunge il limite con la funzionalità di [aumento automatico](event-hubs-auto-inflate.md); questa funzionalità può essere usata anche con Hub eventi per la funzionalità di Kafka. 

### <a name="security-and-authentication"></a>Sicurezza e autenticazione

Hub eventi di Azure richiede SSL o TLS per tutte le comunicazioni e usa le firme di accesso condiviso per l'autenticazione. Questo requisito vale anche per un endpoint Kafka in Hub eventi. Per la compatibilità con Kafka, Hub eventi usa SASL PLAIN per l'autenticazione e SASL SSL per la sicurezza del trasporto. Per altre informazioni sulla sicurezza in Hub eventi, vedere [Sicurezza e autenticazione di Hub eventi](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Altre funzionalità di Hub eventi disponibili per Kafka

Hub eventi per la funzionalità di Kafka consente di scrivere con un protocollo e leggere con un altro, in modo che i producer Kafka correnti possano continuare a pubblicare tramite Kafka, ed è possibile aggiungere lettori con Hub eventi, ad esempio Analisi di flusso di Azure o Funzioni di Azure. Inoltre, le funzionalità di Hub eventi come [Acquisizione](event-hubs-capture-overview.md) e [Ripristino di emergenza geografico](event-hubs-geo-dr.md) possono essere usate anche con Hub eventi per la funzionalità di Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Funzionalità non supportate nell'anteprima

Per l'anteprima pubblica dell'integrazione di Hub eventi per Kafka, non sono supportate le funzionalità di Kafka seguenti:

*   Producer idempotente
*   Transazione
*   Compressione
*   Conservazione basata sulle dimensioni
*   Compattazione dei log
*   Aggiunta di partizioni a un argomento esistente
*   Supporto API Kafka HTTP
*   Kafka Connect
*   Kafka Streams

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata fornita un'introduzione a Hub eventi per Kafka. Per altre informazioni, vedere i collegamenti seguenti:

* [Come creare Hub eventi con supporto per Kafka](event-hubs-create-kafka-enabled.md)
* [Trasmettere in Hub eventi dalle applicazioni Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* [Esplorare altri esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

 
 

