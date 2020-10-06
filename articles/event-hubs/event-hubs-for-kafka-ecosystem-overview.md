---
title: Usare Hub eventi dall'app Apache Kafka - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni sul supporto di Apache Kafka in Hub eventi di Azure.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 5c49f8f87d8d399cda33a332f7464ed340ae3a0f
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761499"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Usare Hub eventi di Azure da applicazioni Apache Kafka
Hub eventi fornisce un endpoint compatibile con le API Apache Kafka® Producer e consumer che possono essere usate dalla maggior parte delle applicazioni client Apache Kafka esistenti in alternativa all'esecuzione di un cluster Apache Kafka. Hub eventi supporta i client API Producer e consumer di Apache Kafka alla versione 1,0 e successive.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Vantaggi di Hub eventi per Kafka

Hub eventi per Apache Kafka funzionalità offre un protocollo basato su Hub eventi di Azure, compatibile con il protocollo con Apache Kafka client compilati per Apache Kafka Server 1,0 e versioni successive e supporta sia per la lettura che per la scrittura in hub eventi, equivalenti a Apache Kafka argomenti. 

È spesso possibile usare l'endpoint Kafka di hub eventi dalle applicazioni senza modifiche al codice rispetto all'installazione di Kafka esistente e modificare solo la configurazione: aggiornare la stringa di connessione nelle configurazioni in modo che punti all'endpoint Kafka esposto dall'hub eventi anziché puntare al cluster Kafka. È quindi possibile iniziare a trasmettere gli eventi dalle applicazioni che usano il protocollo Kafka negli Hub eventi. 

Dal punto di vista concettuale, Kafka e hub eventi sono molto simili: si tratta di log partizionati compilati per il flusso dei dati, in base ai quali il client controlla quale parte del log mantenuto desidera leggere. Nella tabella seguente vengono associati i concetti tra Kafka e Hub eventi.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Associazione di concetti tra Kafka e Hub eventi

| Concetto di Kafka | Concetto di Hub eventi|
| --- | --- |
| Cluster | Spazio dei nomi |
| Argomento | Hub eventi |
| Partition | Partition|
| Gruppo di consumer | Gruppo di consumer |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Differenze principali tra Apache Kafka e hub eventi

Sebbene [Apache Kafka](https://kafka.apache.org/) sia un software che in genere è necessario installare e usare, Hub eventi è un servizio nativo completamente gestito. Nessun server, disco o rete da gestire e monitorare e nessun broker da prendere in considerazione o configurare. Si crea uno spazio dei nomi, ovvero un endpoint con un nome di dominio completo, e quindi si creano Hub eventi (argomenti) all'interno di tale spazio dei nomi. 

Per altre informazioni su Hub eventi e sugli spazi dei nomi, vedere [Funzionalità di Hub eventi?](event-hubs-features.md#namespace). Come servizio cloud, Hub eventi usa un singolo indirizzo IP virtuale stabile come endpoint, quindi i client non devono conoscere i broker o i computer all'interno di un cluster. Anche se Hub eventi implementa lo stesso protocollo, questa differenza significa che tutto il traffico Kafka per tutte le partizioni viene indirizzato in modo prevedibile tramite questo endpoint anziché richiedere l'accesso al firewall per tutti i broker di un cluster.   

La scalabilità negli hub eventi è controllata dal numero di unità di velocità effettiva acquistate, con ogni unità di velocità effettiva che prevede un massimo di 1 megabyte al secondo o 1000 di eventi al secondo in ingresso e il doppio del volume in uscita. Hub eventi può scalare automaticamente le unità di velocità effettiva quando si raggiunge il limite di velocità effettiva se si usa la funzionalità [di aumento automatico](event-hubs-auto-inflate.md) ; Questa funzionalità funziona anche con il supporto del protocollo Apache Kafka.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Apache Kafka la soluzione più adatta per il carico di lavoro?

Dalla creazione di applicazioni con Apache Kafka, sarà utile comprendere anche che hub eventi di Azure fa parte di una flotta di servizi che include anche il [bus di servizio di Azure](../service-bus-messaging/service-bus-messaging-overview.md)e [griglia di eventi di Azure](../event-grid/overview.md). 

Anche se alcuni provider di distribuzioni commerciali di Apache Kafka possono suggerire che Apache Kafka è un punto di distribuzione unico per tutte le esigenze della piattaforma di messaggistica, la realtà è che Apache Kafka non implementa, ad esempio, il modello di coda del [consumer in competizione](/azure/architecture/patterns/competing-consumers) , non dispone del supporto per la  [pubblicazione-sottoscrizione](/azure/architecture/patterns/publisher-subscriber) a un livello che consente ai sottoscrittori di accedere ai messaggi in arrivo in base a regole valutate dal server diverse da offset semplici e non dispone di alcuna funzionalità per tenere traccia del ciclo di vita di un processo avviato da un messaggio o emarginarla messaggi errati in una coda di messaggi non recapitabili, tutti fondamentali per molti scenari di messaggistica aziendali

Per comprendere le differenze tra i modelli e il modello più adatto al servizio, vedere le opzioni di [messaggistica asincrona in Azure](/azure/architecture/guide/technology-choices/messaging) . Gli utenti Apache Kafka possono scoprire che i percorsi di comunicazione finora realizzati con Kafka possono essere realizzati con una complessità molto meno semplice e con funzionalità ancora più potenti tramite griglia di eventi o bus di servizio. 

Se sono necessarie funzionalità specifiche di Apache Kafka che non sono disponibili tramite hub eventi per Apache Kafka interfaccia o se il modello di implementazione supera le [quote di hub eventi](event-hubs-quotas.md), è anche possibile eseguire un [cluster Apache Kafka nativo in Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Sicurezza e autenticazione
Ogni volta che si pubblicano o si utilizzano eventi da un hub eventi per Kafka, il client sta tentando di accedere alle risorse di hub eventi. Si desidera assicurarsi che l'accesso alle risorse avvenga tramite un'entità autorizzata. Quando si usa Apache Kafka protocollo con i client, è possibile impostare la configurazione per l'autenticazione e la crittografia usando i meccanismi SASL. Quando si usa hub eventi per Kafka, è necessaria la crittografia TLS (poiché tutti i dati in transito con hub eventi sono crittografati con TLS). È possibile eseguire questa operazione specificando l'opzione SASL_SSL nel file di configurazione. 

Hub eventi di Azure offre più opzioni per autorizzare l'accesso alle risorse protette. 

- OAuth 2.0
- Firma di accesso condiviso

#### <a name="oauth-20"></a>OAuth 2.0
Hub eventi si integra con Azure Active Directory (Azure AD), che fornisce un server di autorizzazione centralizzato compatibile con **OAuth 2,0** . Con Azure AD, è possibile usare il controllo degli accessi in base al ruolo per concedere autorizzazioni con granularità fine alle identità del client. È possibile usare questa funzionalità con i client Kafka specificando **SASL_SSL** per il protocollo e  **OAUTHBEARER** per il meccanismo. Per informazioni dettagliate sui ruoli e sui livelli di Azure per l'accesso con ambito, vedere [autorizzare l'accesso con Azure ad](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Firma di accesso condiviso
Hub eventi fornisce anche le **firme di accesso condiviso (SAS)** per l'accesso delegato a hub eventi per le risorse Kafka. L'autorizzazione dell'accesso tramite il meccanismo basato su token OAuth 2,0 offre sicurezza e facilità d'uso superiori rispetto alla firma di accesso condiviso. I ruoli predefiniti possono anche eliminare la necessità di autorizzazione basata su ACL, che deve essere gestita e gestita dall'utente. È possibile usare questa funzionalità con i client Kafka specificando **SASL_SSL** per il protocollo e il **normale** meccanismo. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Quando si usa l'autenticazione SAS con client Kafka, le connessioni stabilite non vengono disconnesse quando viene rigenerata la chiave SAS. 

#### <a name="samples"></a>Esempi 
Per un' **esercitazione** con istruzioni dettagliate per creare un hub eventi e accedervi usando SAS o OAuth, vedere [Guida introduttiva: flusso di dati con hub eventi usando il protocollo Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Per altri **esempi** che illustrano come usare OAuth con hub eventi per Kafka, vedere gli [esempi su GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Altre funzionalità di hub eventi 

Hub eventi per Apache Kafka funzionalità è uno dei tre protocolli disponibili simultaneamente negli hub eventi di Azure, che integrano HTTP e AMQP. È possibile scrivere con uno di questi protocolli e leggere con qualsiasi altro, in modo che i produttori di Apache Kafka correnti possano continuare la pubblicazione tramite Apache Kafka, ma il lettore può trarre vantaggio dall'integrazione nativa con l'interfaccia AMQP di hub eventi, ad esempio analisi di flusso di Azure o funzioni di Azure. In modo inverso, è possibile integrare facilmente Hub eventi di Azure in reti di routing AMQP come endpoint di destinazione e leggere i dati anche attraverso Apache Kafka integrazioni.  

Inoltre, funzionalità di hub eventi come l' [acquisizione](event-hubs-capture-overview.md), che consente l'archiviazione a lungo termine estremamente conveniente tramite l'archiviazione BLOB di Azure e Azure Data Lake storage e il [ripristino di emergenza geografico](event-hubs-geo-dr.md) funziona anche con la funzionalità Hub eventi per Kafka.

## <a name="apache-kafka-feature-differences"></a>Differenze tra le funzionalità Apache Kafka 

L'obiettivo degli hub eventi per Apache Kafka è fornire l'accesso alle funzionalità dell'hub eventi di Azure alle applicazioni bloccate nell'API Apache Kafka e che altrimenti avrebbero dovuto essere supportate da un cluster Apache Kafka. 

Come spiegato in [precedenza](#is-apache-kafka-the-right-solution-for-your-workload), la flotta di messaggistica di Azure fornisce una copertura avanzata e affidabile per molti scenari di messaggistica e anche se le funzionalità seguenti non sono attualmente supportate dal supporto di hub eventi per l'API Apache Kafka, è possibile evidenziare dove e come è disponibile la funzionalità desiderata.

### <a name="transactions"></a>Transazioni

Il [bus di servizio di Azure](../service-bus-messaging/service-bus-transactions.md) offre un solido supporto delle transazioni che consente la ricezione e la risoluzione di messaggi e sessioni durante l'invio di messaggi in uscita derivanti dall'elaborazione dei messaggi a più entità di destinazione nella protezione della coerenza di una transazione. Il set di funzionalità non solo consente l'elaborazione esatta di ogni messaggio in una sequenza, ma evita anche il rischio che un altro consumer rielabori inavvertitamente gli stessi messaggi in modo analogo a quanto accade con Apache Kafka. Il bus di servizio è il servizio consigliato per i carichi di lavoro dei messaggi transazionali.

### <a name="compression"></a>Compressione

La funzionalità di [compressione](https://cwiki.apache.org/confluence/display/KAFKA/Compression) lato client di Apache Kafka comprime un batch di più messaggi in un singolo messaggio sul lato producer e decomprime il batch sul lato consumer. Il broker Apache Kafka considera il batch come un messaggio speciale.

Questa funzionalità è fondamentalmente in contrasto con il modello a più protocolli di hub eventi di Azure, che consente di recuperare singolarmente i messaggi, anche quelli inviati in batch, dal broker e tramite qualsiasi protocollo. 

Il payload di qualsiasi evento di hub eventi è un flusso di byte e il contenuto può essere compresso con un algoritmo di propria scelta. Il formato di codifica Apache avro supporta la compressione in modo nativo.

### <a name="log-compaction"></a>Compattazione log

Apache Kafka compressione dei log è una funzionalità che consente di rimuovere tutti i record, tranne l'ultimo, di ogni chiave da una partizione, in modo da trasformare un argomento di Apache Kafka in un archivio chiave-valore in cui l'ultimo valore aggiunto sostituisce quello precedente. Il modello di archivio chiave-valore, anche con aggiornamenti frequenti, è molto meglio supportato dai servizi di database come [Azure Cosmos DB](../cosmos-db/introduction.md).

La funzionalità di compressione dei log viene usata dai framework client di Kafka Connect e Kafka Streams.

### <a name="kafka-streams"></a>Kafka Streams

Kafka Streams è una libreria client per l'analisi di flusso che fa parte del progetto open source Apache Kafka, ma è separata da Apache Kafka broker di flussi di eventi. 

Il motivo più comune per cui i clienti di hub eventi di Azure chiedono il supporto dei flussi Kafka è il fatto che sono interessati al prodotto "ksqlDB" di Confluent. "ksqlDB" è un progetto di origine condiviso proprietario, [concesso in licenza](https://github.com/confluentinc/ksql/blob/master/LICENSE) , in modo che nessun fornitore "offra il software come servizio, una piattaforma distribuita come servizio, un'infrastruttura distribuita come servizio o un altro servizi online simile che compete ai prodotti o ai servizi confluenti" sia autorizzato a usare o a offrire il supporto "ksqlDB". In pratica, se si usa ksqlDB, è necessario usare Kafka autonomamente oppure è necessario usare le offerte cloud di Confluent. Le condizioni di licenza possono anche influenzare i clienti di Azure che offrono servizi per uno scopo escluso dalla licenza.

Autonomo e senza ksqlDB, i flussi Kafka hanno meno funzionalità rispetto a molti Framework e servizi alternativi, la maggior parte dei quali hanno interfacce SQL di streaming predefinite e che si integrano oggi con hub eventi di Azure:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Analisi delle sinapsi di Azure (tramite acquisizione di hub eventi)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)

I servizi e i Framework elencati possono in genere acquisire i flussi di eventi e i dati di riferimento direttamente da un set di origini diversificato tramite gli adapter. I flussi Kafka possono solo acquisire dati da Apache Kafka e i progetti di analisi sono quindi bloccati in Apache Kafka. Per usare i dati di altre origini, è necessario innanzitutto importare i dati in Apache Kafka con il Framework di Kafka Connect.

Se è necessario usare il Framework dei flussi Kafka in Azure, [Apache Kafka in HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) fornirà questa opzione. Apache Kafka su HDInsight fornisce il controllo completo su tutti gli aspetti di configurazione di Apache Kafka, integrandosi completamente con diversi aspetti della piattaforma Azure, dal posizionamento del dominio di aggiornamento o di errore all'isolamento della rete per il monitoraggio dell'integrazione. 

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stata fornita un'introduzione a Hub eventi per Kafka. Per altre informazioni, vedere [Guida per sviluppatori Apache Kafka per Hub eventi di Azure](apache-kafka-developer-guide.md).
