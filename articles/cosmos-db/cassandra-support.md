---
title: Funzionalità e comandi di Apache Cassandra supportati dall'API Cassandra di Azure Cosmos DB
description: Informazioni sul supporto delle funzionalità di Apache Cassandra nell'API Cassandra di Azure Cosmos DB
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: e3de78bdf38a326498b984dc2a9f8eaa42233d22
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091284"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API Cassandra di Azure Cosmos DB tramite i [driver](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) client open source Cassandra del [protocollo di trasmissione](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) Cassandra Query Language (CQL) v4. 

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


## <a name="cassandra-query-language-limits"></a>Limiti di Cassandra Query Language

API Cassandra di Azure Cosmos DB non ha nessun limite sulle dimensioni dei dati archiviati in una tabella. Possono essere archiviati centinaia di terabyte o petabyte di dati, a patto che siano rispettati i limiti della chiave di partizione. Allo stesso modo ogni entità o riga equivalente è privo di eventuali limiti sul numero di colonne, tuttavia le dimensioni totali dell'entità non devono superare 2 MB.

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
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**UNIX/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>Comandi CQL

Azure Cosmos DB supporta i comandi di database seguenti per tutti gli account API Cassandra.

* CREA KEYSPACE 
* CREA TABELLA 
* MODIFICA TABELLA 
* USA 
* INSERT 
* SELECT 
* AGGIORNAMENTO 
* BATCH - sono supportati solo i comandi registrati 
* DELETE

Quando eseguite tramite SDK compatibili con CQLV4, tutte le operazioni crud restituiranno informazioni aggiuntive errore, unità richiesta utilizzate, ID attività. I comandi di cancellazione e aggiornamento devono essere gestiti tenendo in considerazione la governance delle risorse, per evitare un uso eccessivo delle risorse fornite. 
* Nota: se specificato, il valore di gc_grace_seconds deve essere zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Mapping coerente 

API Cassandra di Azure Cosmos DB consente di scegliere la coerenza per le operazioni di lettura. Tutte le operazioni di scrittura, indipendentemente dalla coerenza dell'account, sono sempre scritte con contratti di servizio di prestazione di scrittura.

## <a name="permission-and-role-management"></a>Gestione di ruoli e privilegi

Azure Cosmos DB supporta il controllo degli accessi in base al ruolo e chiavi/password di lettura/scrittura e sola lettura ottenibili tramite il [portale di Azure](https://portal.azure.com. Azure Cosmos DB non supporta ancora utenti e ruoli per attività di piano dati. 

## <a name="planned-support"></a>Supporto pianificato 
* Uso congiunto di timestamp e TTL  
* Il nome dell'area nel comando Crea keyspace viene attualmente ignorato. La distribuzione dei dati viene implementata nella piattaforma Cosmos DB sottostante ed esposta tramite il portale o PowerShell per l'account. 





## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [creazione di un account, database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java

