---
title: Usare Hub eventi dall'app Apache Kafka - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sul supporto di Apache Kafka in Hub eventi di Azure.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 6dcbf0ad0f6678d892c5c02446cac09b4325384c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283651"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Usare Hub eventi di Azure da applicazioni Apache Kafka
Hub eventi fornisce un endpoint Kafka che può essere usato dalle applicazioni Kafka esistenti come alternativa all'esecuzione del proprio cluster Kafka. Hub eventi supporta il [protocollo Apache Kafka 1.0 e versioni successive](https://kafka.apache.org/documentation/) e funziona con le applicazioni Kafka esistenti, tra cui MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vantaggi di Hub eventi per Kafka

Hub eventi per la funzionalità di Kafka fornisce un head di protocollo basato su Hub eventi di Azure con compatibilità binaria con le versioni di Kafka 1.0 e successive per la lettura e la scrittura negli argomenti Kafka. È possibile iniziare a usare l'endpoint di Kafka dalle applicazioni senza alcuna modifica del codice, ma con una modifica minima della configurazione. Aggiornare la stringa di connessione nelle configurazioni in modo che punti all'endpoint di Kafka esposto dall'hub eventi invece di puntare al cluster Kafka. È quindi possibile iniziare a trasmettere gli eventi dalle applicazioni che usano il protocollo Kafka negli Hub eventi. Questa integrazione supporta anche i framework, quali [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), che è attualmente in anteprima. 

Concettualmente Kafka e Hub eventi sono quasi identici: sono entrambi log partizionati creati per lo streaming dei dati. Nella tabella seguente vengono associati i concetti tra Kafka e Hub eventi.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Associazione di concetti tra Kafka e Hub eventi

| Concetto di Kafka | Concetto di Hub eventi|
| --- | --- |
| Cluster | Spazio dei nomi |
| Argomento | Hub eventi |
| Partition | Partition|
| Gruppo di consumer | Gruppo di consumer |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Differenze principali tra Kafka e Hub eventi

Mentre [Apache Kafka](https://kafka.apache.org/) è un software, che è possibile eseguire ogni volta che si vuole, Hub eventi è un servizio cloud simile ad Archiviazione BLOB di Azure. Non ci sono server o reti da gestire, né broker da configurare. Si crea uno spazio dei nomi, vale a dire un nome di dominio completo in cui sono presenti gli argomenti, e quindi si creano gli hub eventi o gli argomenti all'interno di tale spazio dei nomi. Per altre informazioni su Hub eventi e sugli spazi dei nomi, vedere [Funzionalità di Hub eventi?](event-hubs-features.md#namespace). Come servizio cloud, Hub eventi usa un singolo indirizzo IP virtuale stabile come endpoint, pertanto i client non devono conoscere i broker o i computer all'interno di un cluster. 

Il numero di istanze di Hub eventi è determinato dal numero di unità elaborate acquistate, dove ogni unità elaborata dà diritto a 1 MB al secondo, o 1000 eventi al secondo in ingresso. Per impostazione predefinita, Hub eventi ridimensiona il numero di unità elaborate quando si raggiunge il limite con la funzionalità di [aumento automatico](event-hubs-auto-inflate.md); questa funzionalità può essere usata anche con Hub eventi per la funzionalità di Kafka. 

### <a name="security-and-authentication"></a>Sicurezza e autenticazione
Ogni volta che si pubblicano o utilizzano eventi da un hub eventi per Kafka, il client tenta di accedere alle risorse di Hub eventi. Si desidera assicurarsi che l'accesso alle risorse tramite un'entità autorizzata. Quando si usa il protocollo Apache Kafka con i client, è possibile impostare la configurazione per l'autenticazione e la crittografia utilizzando i meccanismi SASL. Quando si usa Hub eventi per Kafka è necessario la crittografia TLS (poiché tutti i dati in transito con Hub di eventi sono crittografati con TLS). Può essere fatto specificando l'opzione SASL_SSL nel file di configurazione. 

Hub eventi di Azure offre più opzioni per autorizzare l'accesso alle risorse protette. 

- OAuth
- Firma di accesso condiviso

#### <a name="oauth"></a>OAuth
Hub eventi si integra con Azure Active Directory (Azure AD), che fornisce un server di autorizzazione centralizzata compatibile con **OAuth** 2.0. Con Azure AD è possibile usare il controllo degli accessi in base al ruolo per concedere autorizzazioni specifiche alle identità client. È possibile utilizzare questa funzionalità con i client Kafka specificando **SASL_SSL** per il protocollo e **OAUTHBEARER** per il meccanismo. Per informazioni dettagliate sui ruoli e i livelli RBAC per l'accesso all'ambito, vedere [Autorizzare l'accesso con Azure AD.](authorize-access-azure-active-directory.md)

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Firma di accesso condiviso
Gli hub eventi forniscono inoltre le firme di **accesso condiviso (SAS, Shared Access Signatures)** per l'accesso delegato agli hub eventi per le risorse Kafka. L'autorizzazione dell'accesso tramite il meccanismo basato su token OAuth 2.0 offre una sicurezza e una maggiore facilità d'uso rispetto alla firma di accesso condiviso. I ruoli predefiniti possono inoltre eliminare la necessità di autorizzazione basata su ACL, che deve essere mantenuta e gestita dall'utente. È possibile utilizzare questa funzionalità con i client Kafka specificando **SASL_SSL** per il protocollo e **PLAIN** per il meccanismo. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Esempi 
Per **un'esercitazione** con istruzioni dettagliate per creare un hub eventi e accedervi tramite SAS o OAuth, vedere [Quickstart: Streaming di dati con hub eventi con il protocollo Kafka.](event-hubs-quickstart-kafka-enabled-event-hubs.md)

Per altri **esempi** che illustrano come usare OAuth con Event Hubs per Kafka, vedere [esempi su GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Altre funzionalità di Hub eventi disponibili per Kafka

Hub eventi per la funzionalità di Kafka consente di scrivere con un protocollo e leggere con un altro, in modo che i producer Kafka correnti possano continuare a pubblicare tramite Kafka, ed è possibile aggiungere lettori con Hub eventi, ad esempio Analisi di flusso di Azure o Funzioni di Azure. Inoltre, le funzionalità di Hub eventi come [Acquisizione](event-hubs-capture-overview.md) e [Ripristino di emergenza geografico](event-hubs-geo-dr.md) possono essere usate anche con Hub eventi per la funzionalità di Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funzioni che non sono ancora supportate 

Di seguito è riportato l'elenco delle funzionalità di Kafka che non sono ancora supportate:

*   Producer idempotente
*   Transazione
*   Compressione
*   Conservazione basata sulle dimensioni
*   Compattazione dei log
*   Aggiunta di partizioni a un argomento esistente
*   Supporto API Kafka HTTP
*   Kafka Streams

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata fornita un'introduzione a Hub eventi per Kafka. Per altre informazioni, vedere i collegamenti seguenti:

- [Come creare un hub eventiHow to create an event hub](event-hubs-create.md)
- [Trasmettere in Hub eventi dalle applicazioni Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Eseguire il mirroring di un broker Kafka in un hub eventi](event-hubs-kafka-mirror-maker-tutorial.md)
- [Connettere Apache Spark a un hub eventi](event-hubs-kafka-spark-tutorial.md)
- [Connettere Apache Flink a un hub eventi](event-hubs-kafka-flink-tutorial.md)
- [Integrazione di Kafka Connect con un hub eventi](event-hubs-kafka-connect-tutorial.md)
- [Connettere Akka Streams a un hub eventi](event-hubs-kafka-akka-streams-tutorial.md)
- [Esplorare gli esempi in GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


