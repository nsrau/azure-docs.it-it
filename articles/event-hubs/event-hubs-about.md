---
title: Che cos'è l'hub di eventi di Azure? - un servizio di inserimento di Big Data | Microsoft Docs
description: Informazioni su Hub eventi di Azure, un servizio di Big Data streaming che inserisce milioni di eventi al secondo.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 83a1026cd9426e1b1651e0ec4f556b8babccb107
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236235"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Hub eventi di Azure - Piattaforma di streaming di Big Data e servizio di inserimento di eventi
Hub eventi di Azure è una piattaforma di streaming di Big Data e un servizio di inserimento di eventi. È in grado di ricevere ed elaborare milioni di eventi al secondo. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione.

Di seguito sono riportati alcuni scenari in cui è possibile usare Hub eventi:

- Rilevamento di anomalie (illecito/outlier)
- Registrazione di applicazioni
- Pipeline di analisi, ad esempio clickstream
- Creazione di dashboard in tempo reale
- Archiviazione di dati
- Elaborazione di transazioni
- Elaborazione di dati di telemetria dell'utente
- Streaming della telemetria dei dispositivi 

## <a name="why-use-event-hubs"></a>Vantaggi dell'uso di Hub eventi

I dati sono utili solo quando esiste un modo semplice per elaborare e ottenere in modo tempestivo informazioni dettagliate da origini dati. Hub eventi offre una piattaforma di elaborazione di flusso distribuito con bassa latenza e facile integrazione, con servizi dati e di analisi all'interno e all'esterno di Azure per creare una pipeline di Big Data completa.

Hub eventi rappresenta la "porta principale" per una pipeline di eventi, spesso denominata *inseritore eventi* nelle architetture della soluzione. Un ingestor evento è un componente o servizio che si trova tra gli autori e i consumer di eventi per separare la produzione di un flusso di eventi dal consumo di tali eventi. Hub eventi offre una piattaforma di streaming unificata con buffer basato sul tempo di conservazione, separando i producer di eventi dai consumer di eventi. 

Le sezioni seguenti descrivono le funzionalità principali del servizio Hub eventi di Azure: 

## <a name="fully-managed-paas"></a>Soluzione PaaS completamente gestita 

Hub eventi è una soluzione Platform-as-a-Service (PaaS) completamente gestita, con requisiti limitati di configurazione e gestione, consentendo ai clienti di concentrare l'attenzione sulle soluzioni aziendali. [Hub eventi per ecosistemi Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) offre l'esperienza di PaaS Kafka senza necessità di dover gestire, configurare o eseguire i cluster.

## <a name="support-for-real-time-and-batch-processing"></a>Supporto per elaborazione batch e in tempo reale

Inserimento, memorizzazione nel buffer, archiviazione ed elaborazione del flusso in tempo reale per ottenere informazioni dettagliate di utilità pratica. Hub eventi usa un [modello di consumer partizionato](event-hubs-features.md#partitions), consentendo a più applicazioni di elaborare contemporaneamente il flusso e all'utente di controllare la velocità di elaborazione.

È possibile[acquisire](event-hubs-capture-overview.md) i dati quasi in tempo reale in un [archivio BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) oppure in [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) per l'elaborazione di micro batch o la conservazione a lungo termine. È possibile ottenere questo comportamento con lo stesso flusso usato per la derivazione di analisi in tempo reale. La configurazione dell'acquisizione di dati degli eventi è una procedura rapida. Non sono previsti costi amministrativi per l'esecuzione e viene ridimensionata automaticamente con le  [unità elaborate](event-hubs-features.md#throughput-units) in Hub eventi. Hub eventi consente ai clienti di concentrarsi sull'elaborazione dei dati, invece che sull'acquisizione dei dati.

Hub eventi di Azure si integra inoltre con [Funzioni di Azure](/azure/azure-functions/) per un'architettura senza server.

## <a name="scalable"></a>Scalabile 

Con Hub eventi, è possibile iniziare con i flussi di dati in MB, per poi aumentare ai GB o TB. La funzionalità [Aumento automatico](event-hubs-auto-inflate.md) è una delle numerose opzioni disponibili per ridimensionare il numero di unità elaborate in modo da soddisfare le esigenze di utilizzo. 

## <a name="rich-ecosystem"></a>Ecosistema avanzato

[Hub eventi per ecosistemi Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) consente ai client e alle applicazioni [Apache Kafka (1.0 e versioni successive)](https://kafka.apache.org/) di comunicare con Hub eventi. Non è necessario impostare, configurare e gestire i propri cluster Kafka.
 
Con un ampio ecosistema disponibile in diversi [linguaggi (.NET, Java, Python, Go, Node. js)](https://github.com/Azure/azure-event-hubs), è possibile avviare facilmente l'elaborazione di flussi da Hub eventi. Tutte i linguaggi lato client supportati offrono un'integrazione di basso livello. L'ecosistema garantisce anche un'integrazione perfetta con servizi di Azure come Analisi di flusso di Azure e Funzioni di Azure, offrendo così la possibilità di compilare architetture senza server.

## <a name="key-architecture-components"></a>Componenti principali dell'architettura
Hub eventi contiene gli [elementi chiave](event-hubs-features.md) seguenti:

- **Producer di eventi**: qualsiasi entità che invia dati a un hub eventi. Gli autori di eventi possono pubblicare eventi usando HTTPS o AMQP 1.0 o Apache Kafka (1.0 e versioni successive)
- **Partizioni**: ogni consumer legge solo un subset specifico, o partizione, del flusso di messaggi.
- **Gruppi di consumer**: una vista (stato, posizione o offset) di un intero hub eventi. I gruppi di consumer consentono a più applicazioni di avere ognuna una visualizzazione distinta del flusso di eventi. I consumer leggono il flusso in modo indipendente in base al proprio ritmo e con offset specifici.
- **Unità elaborate**: unità di capacità pre-acquistate che controllano la capacità di velocità effettiva degli hub eventi.
- **Ricevitori di eventi**: qualsiasi entità che legge i dati dell'evento da un hub eventi. Tutti i consumer di Hub eventi si connettono tramite la sessione AMQP 1.0. Il servizio Hub eventi distribuisce gli eventi tramite una sessione non appena diventano disponibili. Tutti i consumer Kafka si connettono tramite il protocollo Kafka 1.0 e versioni successive.

La figura seguente illustra l'architettura di elaborazione del flusso di Hub eventi:

![Hub eventi](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare gli Hub eventi, vedere le esercitazioni per **inviare e ricevere eventi**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo invio)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo ricezione)](event-hubs-storm-getstarted-receive.md)


Per ulteriori informazioni su Hub eventi, vedere i seguenti articoli:

- [Panoramica delle funzionalità di Hub eventi](event-hubs-features.md)
- [Domande frequenti](event-hubs-faq.md)


