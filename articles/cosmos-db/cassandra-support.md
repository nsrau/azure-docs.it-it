---
title: Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB
description: Informazioni sul supporto delle funzionalità di Apache Cassandra nell'API Cassandra di Azure Cosmos DB
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 89e8a6a2abfc38c497be646bd70910895f92588f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489320"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Funzionalità di Apache Cassandra supportate dall'API Cassandra di Azure Cosmos DB 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API Cassandra di Azure Cosmos DB tramite i [driver](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) client open source Cassandra compatibili con il [protocollo di trasmissione](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) CQL Binary Protocol v4. 

Usando l'API Cassandra di Azure Cosmos DB, è possibile sfruttare i vantaggi delle API Apache Cassandra, nonché le funzionalità aziendali offerte da Azure Cosmos DB. Le funzionalità aziendali includono [distribuzione globale](distribute-data-globally.md), [partizionamento di scalabilità automatica orizzontale](cassandra-partitioning.md), garanzie di disponibilità e latenza, crittografia dei dati inattivi, backup e molto altro ancora.

## <a name="cassandra-protocol"></a>Protocollo Cassandra 

L'API Cassandra di Azure Cosmos DB è compatibile con l'API Cassandra Query Language (CQL) v3.11 (compatibile con la versione 2.x). Comandi CQL supportati, strumenti, limitazioni ed eccezioni sono elencate di seguito. I driver client che identificano questi protocolli dovrebbero essere in grado di collegarsi all'API Cassandra di Cosmos DB.

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

|Comando  |Supportato |
|---------|---------|
| ascii  | Sì |
| bigint  | Sì |
| blob  | Sì |
| boolean  | Sì |
| counter  | Sì |
| Data  | Sì |
| decimal  | Sì |
| double  | Sì |
| float  | Sì |
| bloccato  | Sì |
| inet  | Sì |
| INT  | Sì |
| list  | Sì |
| set  | Sì |
| SMALLINT  | Sì |
| text  | Sì |
| time  | Sì |
|  timestamp  | Sì |
| timeuuid  | Sì |
| TINYINT  | Sì |
| tupla  | Sì |
| uuid  | Sì |
| varchar  | Sì |
| varint  | Sì |
| tuple | Sì | 
| udts  | Sì |
| map | Sì |

Per la dichiarazione del tipo di dati è supportato il tipo statico.

## <a name="cql-functions"></a>Funzioni CQL

API Cassandra di Azure Cosmos DB supporta i tipi di funzioni CQL seguenti:

|Comando  |Supportato |
|---------|---------|
| Token * | Sì |
| ttl | Sì |
| writetime | Sì |
| Cast | No |

\* L'API Cassandra supporta il token come proiezione/selettore e consente solo token(pk) sul lato sinistro di una clausola WHERE. Ad esempio, `WHERE token(pk) > 1024` è supportato, ma `WHERE token(pk) > token(100)` non lo è.


Funzioni di aggregazione:

|Comando  |Supportato |
|---------|---------|
| Min | Sì |
| max | Sì |
| avg | Sì |
| count | Sì |

Funzioni di conversione BLOB:
 
|Comando  |Supportato |
|---------|---------|
| typeAsBlob(value)   | Sì |
| blobAsType(value) | Sì |


Funzioni UUID e timeuuid:
 
|Comando  |Supportato |
|---------|---------|
| dateOf()  | Sì |
| now()  | Sì |
| minTimeuuid()  | Sì |
| unixTimestampOf()  | Sì |
| toDate(timeuuid)  | Sì |
| toTimestamp(timeuuid)  | Sì |
| toUnixTimestamp(timeuuid)  | Sì |
| toDate(timestamp)  | Sì |
| to UnixTimestamp(timestamp)  | Sì |
| toTimestamp(date)  | Sì |
| toUnixTimestamp(date) | Sì |


  
## <a name="cql-commands"></a>Comandi CQL

Azure Cosmos DB supporta i comandi di database seguenti per tutti gli account API Cassandra.

|Comando  |Supportato |
|---------|---------|
| ALLOW FILTERING | Sì |
| ALTER KEYSPACE | N/d (servizio PaaS, replica gestita internamente)|
| ALTER MATERIALIZED VIEW | No |
| ALTER ROLE | No |
| MODIFICA TABELLA | Sì |
| ALTER TYPE | No |
| ALTER USER | No |
| BATCH | Sì (solo batch non registrato)|
| COMPACT STORAGE | N/d (servizio PaaS) |
| CREATE AGGREGATE | No | 
| CREATE CUSTOM INDEX (SASI) | No |
| CREATE INDEX | Sì (senza [specificare il nome di indice](cassandra-secondary-index.md) e gli indici delle chiavi di clustering o raccolta FROZEN completa non supportata) |
| CREATE FUNCTION | No |
| CREATE KEYSPACE (impostazioni di replica ignorate) | Sì |
| CREATE MATERIALIZED VIEW | No |
| CREA TABELLA | Sì |
| CREATE TRIGGER | No |
| CREATE TYPE | Sì |
| CREATE ROLE | No |
| CREATE USER (deprecato in Apache Cassandra nativo) | No |
| DELETE | Sì |
| DELETE (transazioni leggere con IF CONDITION)| Sì |
| DISTINCT | No |
| DROP AGGREGATE | No |
| .DROP FUNCTION | No |
| DROP INDEX | Sì |
| DROP KEYSPACE | Sì |
| DROP MATERIALIZED VIEW | No |
| DROP ROLE | No |
| DROP TABLE | Sì |
| DROP_TRIGGER | No | 
| DROP TYPE | Sì |
| DROP USER (deprecato in Apache Cassandra nativo) | No |
| GRANT | No |
| INSERT | Sì |
| INSERT (transazioni leggere con IF CONDITION)| Sì |
| LIST PERMISSIONS | No |
| LIST ROLES | No |
| LIST USERS (deprecato in Apache Cassandra nativo) | No |
| REVOKE | No |
| SELECT | Sì |
| SELECT (transazioni leggere con IF CONDITION)| No |
| UPDATE | Sì |
| UPDATE (transazioni leggere con IF CONDITION)| No |
| TRUNCATE | No |
| USE | Sì |

## <a name="json-support"></a>Supporto JSON
|Comando  |Supportato |
|---------|---------|
| SELECT JSON | Sì |
| INSERT JSON | Sì |
| fromJson() | No |
| toJson() | No |


## <a name="cassandra-api-limits"></a>Limiti dell'API Cassandra

API Cassandra di Azure Cosmos DB non ha nessun limite sulle dimensioni dei dati archiviati in una tabella. Possono essere archiviati centinaia di terabyte o petabyte di dati, a patto che siano rispettati i limiti della chiave di partizione. In modo analogo, ogni entità o riga equivalente non prevede limiti per il numero di colonne. La dimensione totale dell'entità non deve tuttavia superare i 2 MB. I dati per chiave di partizione non possono superare i 20 GB, come in tutte le altre API.

## <a name="tools"></a>Strumenti 

API Cassandra di Azure Cosmos DB è una piattaforma di servizi gestiti. Non richiede alcun sovraccarico di gestione o utilità come Garbage Collector, Java Virtual Machine(JVM) e nodetool per gestire il cluster. Supporta strumenti come cqlsh che utilizza la compatibilità binaria CQLv4. 

* Esplora dati del portale di Azure, metriche, diagnostica log, PowerShell e interfaccia della riga di comando sono altri meccanismi supportati per gestire l'account.

## <a name="hosted-cql-shell-preview"></a>Shell CQL ospitata (anteprima)

È possibile aprire una shell Cassandra nativa ospitata (CQLSH v 5.0.1) direttamente da Esplora dati nel [portale di Azure](data-explorer.md) o in [Azure Cosmos Explorer](https://cosmos.azure.com/). Prima di abilitare la shell CQL, è necessario [abilitare la funzionalità Notebooks](enable-notebooks.md) nell'account. Se non è già abilitata, quando si fa clic su `Open Cassandra Shell`, verrà richiesto di farlo. Per informazioni sulle aree di Azure supportate, vedere la nota evidenziata in [Abilitare i notebook per gli account di Azure Cosmos DB](enable-notebooks.md).

:::image type="content" source="./media/cassandra-support/cqlsh.png" alt-text="Aprire CQLSH&quot;:::

Per connettersi all'API Cassandra in Azure Cosmos DB, è anche possibile usare CQLSH installato in un computer locale. Questa utilità è inclusa in Apache Cassandra 3.1.1 e per usarla è sufficiente impostare le variabili di ambiente. Le sezioni seguenti includono le istruzioni per installare, configurare e connettersi all'API Cassandra in Azure Cosmos DB, in Windows o Linux, tramite CQLSH.

> [!NOTE]
> Le connessioni all'API Cassandra di Azure Cosmos DB non funzioneranno con le versioni DataStax Enterprise (DSE) di CQLSH. Assicurarsi di usare solo le versioni open source di Apache Cassandra di CQLSH quando ci si connette all'API Cassandra. 

**Windows:**

Se si usa Windows, è consigliabile abilitare il [file system Windows per Linux](/windows/wsl/install-win10#install-the-windows-subsystem-for-linux). Sarà quindi possibile seguire i comandi Linux riportati di seguito.

**UNIX/Linux/Mac:**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo &quot;deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

Se eseguite tramite un SDK compatibile con CQL v4, tutte le operazioni CRUD restituiranno informazioni aggiuntive sull'errore e sulle unità richiesta consumate. Per garantire un uso estremamente efficiente delle unità elaborate di cui è stato effettuato il provisioning, i comandi DELETE e UPDATE devono essere gestiti tenendo in considerazione la governance delle risorse.

* Nota: se specificato, il valore di gc_grace_seconds deve essere zero.

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>Mapping coerente 

API Cassandra di Azure Cosmos DB consente di scegliere la coerenza per le operazioni di lettura.  Il mapping di coerenza è descritto in maniera dettagliata [qui](./cassandra-consistency.md#mapping-consistency-levels).

## <a name="permission-and-role-management"></a>Gestione di ruoli e privilegi

Azure Cosmos DB supporta il controllo degli accessi in base al ruolo per il provisioning, la rotazione delle chiavi, la visualizzazione delle metriche e le chiavi/password di lettura/scrittura e sola lettura ottenibili tramite il [portale di Azure](https://portal.azure.com). Azure Cosmos DB non supporta i ruoli per le attività CRUD.

## <a name="keyspace-and-table-options"></a>Opzioni di Keyspace e tabella

Le opzioni del comando "Create Keyspace" relative a nome di area, classe, fattore di replica e data center vengono attualmente ignorate. Il sistema usa il metodo di replica della [distribuzione globale](global-dist-under-the-hood.md) sottostante di Azure Cosmos DB per aggiungere le aree. Se è necessaria la presenza di dati di più aree, è possibile abilitarla a livello di account con PowerShell, l'interfaccia della riga di comando o il portale. Per altre informazioni, vedere l'articolo su [come aggiungere aree](how-to-manage-database-account.md#addremove-regions-from-your-database-account). L'opzione durable_writes non può essere disabilitata perché Azure Cosmos DB assicura che ogni scrittura sia durevole. In ogni area Azure Cosmos DB replica i dati nel set di repliche composto da quattro repliche. La [configurazione](global-dist-under-the-hood.md) di questo set di repliche non può essere modificata.
 
Tutte le opzioni vengono ignorate durante la creazione della tabella, ad eccezione di gc_grace_seconds, che deve essere impostata su zero.
Keyspace e tabella includono un'altra opzione, denominata "cosmosdb_provisioned_throughput", con un valore minimo di 400 UR/s. Le unità elaborate di Keyspace possono essere condivise tra più tabelle e sono utili per gli scenari in cui nessuna tabella utilizza quelle di cui è stato effettuato il provisioning. Il comando Alter Table consente di cambiare le unità elaborate con provisioning tra le aree. 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```
## <a name="secondary-index"></a>Indice secondario
L'API Cassandra supporta indici secondari in tutti i tipi di dati, tranne i tipi di raccolta bloccata, i tipi Decimal e Variant. 

## <a name="usage-of-cassandra-retry-connection-policy"></a>Utilizzo dei criteri di ripetizione delle connessioni di Cassandra

Azure Cosmos DB è un sistema governato da risorse. Questo significa che è possibile eseguire un determinato numero di operazioni in un dato secondo in base alle unità richieste utilizzate dalle operazioni. Se un'applicazione supera tale limite in un dato secondo, la frequenza delle richieste viene limitata e verranno generate eccezioni. L'API Cassandra di Azure Cosmos DB converte queste eccezioni in errori di overload per il protocollo nativo Cassandra. Per assicurarsi che l'applicazione sia in grado di intercettare e ripetere le richieste in caso di limitazione della frequenza, sono disponibili le estensioni [Spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) e [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions). Quando ci si connette all'API Cassandra in Azure Cosmos DB, vedere anche gli esempi di codice Java per i driver Datastax [versione 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) e [versione 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4). Sei si usano altri SDK per accedere all'API Cassandra in Azure Cosmos DB, creare un criterio per ripetere le connessioni in caso di tali eccezioni.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione alla [creazione di un account, database e una tabella API Cassandra](create-cassandra-api-account-java.md) usando un'applicazione Java