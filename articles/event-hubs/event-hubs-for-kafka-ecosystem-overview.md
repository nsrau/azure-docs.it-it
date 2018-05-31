---
title: Hub eventi di Azure per gli ecosistemi Kafka| Microsoft Docs
description: Panoramica e introduzione a Hub eventi di Azure con supporto per Kafka
services: event-hubs
documentationcenter: .net
author: djrosanova
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 05/07/2018
ms.author: darosa
ms.openlocfilehash: 51e32737be4eaf3c61cdbe50b82a05c205800ac4
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077607"
---
# <a name="event-hubs-for-kafka-ecosystems"></a>Hub eventi per gli ecosistemi Kafka

Hub eventi per gli ecosistemi Kafka fornisce un endpoint Kafka che può essere usato dalle applicazioni Kafka esistenti come alternativa all'esecuzione del proprio cluster Kafka. Hub eventi per l'ecosistema Kafka supporta [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) e le versioni client più recenti e può essere usato con le applicazioni Kafka esistenti, tra cui MirrorMaker. Modificare la stringa di connessione e avviare lo streaming degli eventi dalle applicazioni che usano il protocollo Kafka in Hub eventi.

## <a name="what-does-event-hubs-for-kafka-ecosystems-provide"></a>Vantaggi di Hub eventi per gli ecosistemi Kafka

Hub eventi per l'ecosistema Kafka fornisce un head di protocollo basato su Hub eventi di Azure con compatibilità binaria con le versioni di Kafka 1.0 e successive per la lettura e la scrittura negli argomenti Kafka. Concettualmente, Kafka e Hub eventi sono quasi identici: sono entrambi log partizionati creati per lo streaming dei dati. Nella tabella seguente vengono associati i concetti tra Kafka e Hub eventi.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Associazione di concetti tra Kafka e Hub eventi

| Concetto di Kafka | Concetto di Hub eventi|
| --- | --- |
| Cluster | Spazio dei nomi |
| Argomento | Hub eventi |
| Partition | Partition|
| Gruppo di consumer | Gruppo di consumer |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Differenze principali tra Kafka e Hub eventi

Mentre [Apache Kafka](https://kafka.apache.org/) è un software, che è possibile eseguire ogni volta che si vuole, Hub eventi è un servizio cloud simile ad Archiviazione BLOB di Azure. Non ci sono server o reti da gestire, né broker da configurare. Si crea uno spazio dei nomi, vale a dire un nome di dominio completo in cui sono presenti gli argomenti, e quindi si creano gli hub eventi o gli argomenti all'interno di tale spazio dei nomi. Per altre informazioni su Hub eventi e sugli spazi dei nomi, vedere [Che cos'è Hub eventi?](event-hubs-what-is-event-hubs.md). In quanto servizio cloud, Hub eventi usa un singolo indirizzo IP virtuale stabile come endpoint, in modo che i client non debbano necessariamente disporre di informazioni sui broker o sui computer all'interno di un cluster. 

Il numero di istanze di Hub eventi è determinato dal numero di unità elaborate acquistate, dove ogni unità elaborata dà diritto a 1 MB al secondo, o 1000 eventi al secondo in ingresso. Per impostazione predefinita, Hub eventi ridimensiona il numero di unità elaborate quando si raggiunge il limite con la funzionalità di [aumento automatico](event-hubs-auto-inflate.md); questa funzionalità può essere usata anche con Hub eventi per gli ecosistemi Kafka. 

### <a name="security-and-authentication"></a>Sicurezza e autenticazione

Hub eventi di Azure richiede SSL o TLS per tutte le comunicazioni e usa le firme di accesso condiviso per l'autenticazione. Questo requisito vale anche per un endpoint Kafka in Hub eventi. Per la compatibilità con Kafka, Hub eventi usa SASL PLAIN per l'autenticazione e SASL SSL per la sicurezza del trasporto. Per altre informazioni sulla sicurezza in Hub eventi, vedere [Sicurezza e autenticazione di Hub eventi](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Altre funzionalità di Hub eventi disponibili per Kafka

Hub eventi per l'ecosistema Kafka consente di scrivere con un protocollo e leggere con un altro, in modo che i producer Kafka correnti possano continuare a pubblicare tramite Kafka, ed è possibile aggiungere lettori con Hub eventi, ad esempio Analisi di flusso di Azure o Funzioni di Azure. Inoltre, le funzionalità di Hub eventi come [Acquisizione](event-hubs-capture-overview.md) e [Ripristino di emergenza geografico](event-hubs-geo-dr.md) possono essere usate anche con Hub eventi per gli ecosistemi Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Funzionalità non supportate nell'anteprima

Per l'anteprima pubblica dell'integrazione di Hub eventi per gli ecosistemi Kafka, non sono supportate le funzionalità di Kafka seguenti:

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

In questo articolo è stata fornita un'introduzione a Hub eventi per gli ecosistemi Kafka. Per altre informazioni, vedere i collegamenti seguenti:

* [Come creare Hub eventi con supporto per Kafka](event-hubs-create-kafka-enabled.md)
* [Eseguire lo streaming in Hub eventi dalle applicazioni Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Iniziare con un'[esercitazione di Hub eventi](event-hubs-dotnet-standard-getstarted-send.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)

 
 

