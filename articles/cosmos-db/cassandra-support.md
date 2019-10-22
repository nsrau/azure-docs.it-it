---
title: Funzionalità e comandi di Apache Cassandra supportati dall'API Cassandra di Azure Cosmos DB
description: Informazioni sul supporto delle funzionalità di Apache Cassandra nell'API Cassandra di Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 66a972e66c35cdd5b8dedceefbe3dbd008380da9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327147"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API Cassandra di Azure Cosmos DB tramite i [driver](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) client open source Cassandra del [protocollo di trasmissione](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) Cassandra Query Language (CQL) v4. 

Usando l'API Cassandra di Azure Cosmos DB, è possibile sfruttare i vantaggi delle API Apache Cassandra, nonché le funzionalità aziendali offerte da Azure Cosmos DB. Le funzionalità aziendali includono [distribuzione globale](distribute-data-globally.md), [partizionamento di scalabilità automatica orizzontale](partition-data.md), garanzie di disponibilità e latenza, crittografia dei dati inattivi, backup e molto altro ancora.

## <a name="cassandra-protocol"></a>Protocollo Cassandra 

L'API Cassandra di Azure Cosmos DB è compatibile con la versione CQL **v4**. Comandi CQL supportati, strumenti, limitazioni ed eccezioni sono elencate di seguito. I driver client che identificano questi protocolli dovrebbero essere in grado di collegarsi all'API Cassandra di Cosmos DB.

## <a name="cassandra-driver"></a>Driver Cassandra

Le seguenti versioni di driver Cassandra sono supportate dall'API Cassandra di Azure Cosmos DB:

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Tipi di dati CQL 

API Cassandra di Azure Cosmos DB supporta i tipi di dati CQL seguenti:

* ascii  
* bigint  
* BLOB  
* boolean  
* counter  
* date  
* decimal  
* Double  
* float  
* bloccato  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tupla  
* uuid  
* varchar  
* varint  
* tuple  
* udts  
* map  

## <a name="cql-functions"></a>Funzioni CQL

API Cassandra di Azure Cosmos DB supporta i tipi di funzioni CQL seguenti:

* token  
* Funzioni di aggregazione
  * min, max, media, conteggio
* Funzioni di conversione BLOB 
  * typeAsBlob(value)  
  * blobAsType(value)
* Funzioni UUID e timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * to UnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Limiti dell'API Cassandra

API Cassandra di Azure Cosmos DB non ha nessun limite sulle dimensioni dei dati archiviati in una tabella. Possono essere archiviati centinaia di terabyte o petabyte di dati, a patto che siano rispettati i limiti della chiave di partizione. Allo stesso modo ogni entità o riga equivalente non prevede limiti sul numero di colonne, tuttavia le dimensioni totali dell'entità non devono superare 2 MB. I dati per chiave di partizione non possono superare 10 GB, come per tutte le altre API.

## <a name="tools"></a>Strumenti 

API Cassandra di Azure Cosmos DB è una piattaforma di servizi gestiti. Non richiede alcun sovraccarico di gestione o utilità come Garbage Collector, Java Virtual Machine(JVM) e nodetool per gestire il cluster. Supporta strumenti come cqlsh che utilizza la compatibilità binaria CQLv4. 

* Esplora dati del portale di Azure, metriche, diagnostica log, PowerShell e cli sono altri meccanismi supportati per gestire l'account.

## <a name="cql-shell"></a>Shell CQL  

Il servizio di utilità della riga di comando CQLSH viene fornito con Apache Cassandra 3.1.1 e funziona in modo eccellente con le seguenti variabili di ambiente abilitate:

Prima di eseguire i comando seguenti [aggiungere un certificato radice Baltimore all'archivio cacerts](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store). 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**UNIX/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>Comandi CQL

Azure Cosmos DB supporta i comandi di database seguenti per tutti gli account API Cassandra.

* CREATE KEYSPACE (le impostazioni di replica per questo comando vengono ignorate)
* CREA TABELLA 
* MODIFICA TABELLA 
* USA 
* INSERT 
* SELECT 
* AGGIORNAMENTO 
* BATCH - sono supportati solo i comandi registrati 
* DELETE

Se eseguite tramite SDK compatibili con CQLV4, tutte le operazioni CRUD restituiranno informazioni aggiuntive su errore e unità richiesta utilizzate. I comandi di eliminazione e aggiornamento devono essere gestiti tenendo in considerazione la governance delle risorse, per evitare l'uso delle unità elaborate di cui è stato effettuato il provisioning. 
* Nota: se specificato, il valore di gc_grace_seconds deve essere zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapping coerente 

API Cassandra di Azure Cosmos DB consente di scegliere la coerenza per le operazioni di lettura.  Il mapping di coerenza è descritto in maniera dettagliata [qui](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping).

## <a name="permission-and-role-management"></a>Gestione di ruoli e privilegi

Azure Cosmos DB supporta il controllo degli accessi in base al ruolo per il provisioning, la rotazione delle chiavi, la visualizzazione delle metriche e le chiavi/password di lettura/scrittura e sola lettura ottenibili tramite il [portale di Azure](https://portal.azure.com). Azure Cosmos DB non supporta i ruoli per le attività CRUD.

## <a name="keyspace-and-table-options"></a>Opzioni di Keyspace e tabella

Le opzioni del comando "Create Keyspace" relative a nome di area, classe, fattore di replica e data center vengono attualmente ignorate. Il sistema usa il metodo di replica della [distribuzione globale](https://docs.microsoft.com/en-us/azure/cosmos-db/global-dist-under-the-hood) sottostante di Azure Cosmos DB per aggiungere le aree. Se è necessaria la presenza di dati di più aree, è possibile abilitarla a livello di account con PowerShell, l'interfaccia della riga di comando o il portale. Per altre informazioni, vedere l'articolo su [come aggiungere aree](how-to-manage-database-account.md#addremove-regions-from-your-database-account). L'opzione durable_writes non può essere disabilitata perché Azure Cosmos DB assicura che ogni scrittura sia durevole. In ogni area Azure Cosmos DB replica i dati attraverso il set di repliche, che è composto da 4 repliche e la cui [configurazione](global-dist-under-the-hood.md) non può essere modificata.
 
Tutte le opzioni vengono ignorate durante la creazione della tabella, ad eccezione di except gc_grace_seconds, che deve essere impostata su zero.
Keyspace e tabella includono un'altra opzione, denominata "cosmosdb_provisioned_throughput", con un valore minimo di 400 UR/s. Le unità elaborate di Keyspace possono essere condivise tra più tabelle e sono utili per gli scenari in cui nessuna tabella utilizza quelle di cui è stato effettuato il provisioning. Il comando Alter Table consente di cambiare le unità elaborate con provisioning tra le aree. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>Utilizzo dei criteri di ripetizione delle connessioni di Cassandra

Azure Cosmos DB è un sistema governato da risorse. Questo significa che è possibile eseguire un determinato numero di operazioni in un dato secondo in base alle unità richieste utilizzate dalle operazioni. Se un'applicazione supera tale limite in un dato secondo, la frequenza delle richieste viene limitata e verranno generate eccezioni. L'API Cassandra di Azure Cosmos DB converte queste eccezioni in errori di overload per il protocollo nativo Cassandra. Per assicurarsi che l'applicazione sia in grado di intercettare e ripetere le richieste in caso di limitazione della frequenza, sono disponibili le estensioni [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions). Sei si usano altri SDK per accedere all'API Cassandra in Azure Cosmos DB, creare un criterio per ripetere le connessioni in caso di tali eccezioni.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [creazione di un account, database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java

