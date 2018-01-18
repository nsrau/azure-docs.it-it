---
title: Connessione di Apache Spark ad Azure Cosmos DB | Microsoft Docs
description: "Usare questa esercitazione per ottenere informazioni sul connettore Spark per Azure Cosmos DB, che consente di connettere Apache Spark ad Azure Cosmos DB per eseguire attività di data science e aggregazioni distribuite nel sistema di database multi-tenant con distribuzione globale di Microsoft progettato per il cloud."
keywords: Apache Spark
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: denlee
ms.openlocfilehash: 4ba8a53f2018727cc4fa225b2d4ce14d9f1d7467
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Velocizzare l'analisi di Big Data in tempo reale con il connettore Spark per Azure Cosmos DB

Il connettore Spark per Azure Cosmos DB consente ad Azure Cosmos DB di fungere da origine di input o sink di output per i processi Apache Spark. Connettendo [Spark](http://spark.apache.org/) ad [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), è possibile risolvere più velocemente problemi di data science in rapida evoluzione usando Azure Cosmos DB per salvare in modo permanente i dati ed eseguire query su di essi in modo rapido. Il connettore Spark per Azure Cosmos DB usa in modo efficiente gli indici gestiti nativi di Azure Cosmos DB. Gli indici consentono colonne aggiornabili in fase di analisi e propagazione del filtraggio in base al predicato per i dati distribuiti a livello globale in rapida evoluzione, che spaziano da scenari IoT (Internet delle cose) a scenari di data science e analisi.

Per altre informazioni, vedere questo video con il Principal Program Manager di Azure Cosmos DB, Denny Lee. 

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

Per usare Spark GraphX e le API Graph Gremlin di Azure Cosmos DB, vedere [Azure Cosmos DB: eseguire analisi dei grafi con Spark e Apache TinkerPop Gremlin](spark-connector-graph.md).

## <a name="download"></a>Download

Per iniziare, scaricare il connettore Spark per Azure Cosmos DB dal repository [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) in GitHub.

## <a name="connector-components"></a>Componenti del connettore

Sono i seguenti:

* [Azure Cosmos DB](http://documentdb.com) consente ai clienti di effettuare il provisioning e ridimensionare in modo elastico la velocità effettiva e le risorse di archiviazione in un numero qualsiasi di aree geografiche. Il servizio offre:
   * [Distribuzione globale](distribute-data-globally.md) e scalabilità orizzontale chiavi in mano
   * Latenza garantita a una sola cifra al 99° percentile
   * [Più modelli di coerenza ben definiti](consistency-levels.md)
   * Disponibilità elevata garantita con funzionalità multihosting
   * Tutte le funzionalità sono supportate da [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db) completi leader del settore.

* [Apache Spark](http://spark.apache.org/) è un potente motore di elaborazione open source incentrato su velocità, semplicità d'uso e analisi avanzata.

* [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) per poter distribuire Apache Spark nel cloud per distribuzioni cruciali usando [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versioni supportate ufficialmente:

| Componente | Versione |
|---------|-------|
|Apache Spark|2.0.2, 2.1.0, 2.2.0|
| Scala| 2.10, 2.11|
| Azure Cosmos DB SQL Java SDK | 1.14.0, 1.15.0 |

Questo articolo consente di eseguire alcuni semplici esempi con Python (tramite pyDocumentDB) e le interfacce di Scala.

Per la connessione di Apache Spark e Azure Cosmos DB sono disponibili due approcci:
- Usare pyDocumentDB mediante [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python).
- Creare un connettore Spark basato su Java per Azure Cosmos DB usando [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Implementazione di pyDocumentDB
La versione corrente di [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) consente di connettere Spark ad Azure Cosmos DB, come illustrato nel diagramma seguente:

![Flusso di dati da Spark ad Azure Cosmos DB tramite pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Flusso di dati dell'implementazione di pyDocumentDB

Il flusso di dati è il seguente:

1. Il nodo master Spark si connette al nodo del gateway Azure Cosmos DB tramite pyDocumentDB. Un utente specifica solo le connessioni di Spark e Azure Cosmos DB. Le connessioni ai rispettivi nodi master e del gateway sono trasparenti per l'utente.
2. Il nodo del gateway esegue la query su Azure Cosmos DB, dove la query viene successivamente eseguita sulle partizioni della raccolta nei nodi dati. La risposta a queste query viene inviata di nuovo al nodo del gateway e il set di risultati viene restituito al nodo master Spark.
3. Le query successive, ad esempio su un frame di dati Spark, vengono inviate ai nodi di lavoro Spark per l'elaborazione.

La comunicazione tra Spark e Azure Cosmos DB è limitata al nodo master Spark e ai nodi del gateway Azure Cosmos DB.  Le query vengono eseguite alla velocità consentita dal livello di trasporto tra i due nodi.

### <a name="install-pydocumentdb"></a>Installare pyDocumentDB
È possibile installare pyDocumentDB sul nodo del driver usando **pip**, ad esempio:

```bash
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Connettere Spark ad Azure Cosmos DB tramite pyDocumentDB
La semplicità del trasporto di comunicazione rende relativamente semplice l'esecuzione di una query da Spark ad Azure Cosmos DB con pyDocumentDB.

Il frammento di codice seguente mostra come usare pyDocumentDB in un contesto Spark.

```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Come indicato nel frammento di codice:

* Azure Cosmos DB Python SDK (`pyDocumentDB`) contiene tutti i parametri di connessione necessari. Il parametro relativo alle posizioni preferite determina ad esempio la replica di lettura e l'ordine di priorità.
*  Importare le librerie necessarie e configurare **masterKey** e **host** per creare il *client* Azure Cosmos DB (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>Eseguire query Spark tramite pyDocumentDB
Gli esempi seguenti usano l'istanza di Azure Cosmos DB creata nel frammento precedente con le chiavi di sola lettura specificate. Il frammento di codice seguente si connette alla raccolta **airports.codes** nell'account DoctorWho specificato in precedenza ed esegue una query per estrarre le città con aeroporto nello stato di Washington.

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

Dopo l'esecuzione della query tramite **query**, il risultato è un oggetto **query_iterable.QueryIterable** che viene convertito in un elenco Python. Un elenco Python può essere convertito facilmente in un frame di dati Spark usando il codice seguente:

```python
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Perché usare pyDocumentDB per connettere Spark ad Azure Cosmos DB?
La connessione di Spark ad Azure Cosmos DB mediante pyDocumentDB viene usata in genere negli scenari in cui:

* Si vuole usare Python.
* Si restituisce un set di risultati relativamente piccolo da Azure Cosmos DB a Spark. Si noti che il set di dati sottostante in Azure Cosmos DB può essere piuttosto grande. Si stanno applicando filtri, ovvero eseguendo filtri in base a predicati, sull'origine Azure Cosmos DB.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Connettore Spark per Azure Cosmos DB

Il connettore Spark per Azure Cosmos DB usa [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java) e sposta i dati tra i nodi di lavoro Spark e Azure Cosmos DB, come illustrato nel diagramma seguente:

![Flusso di dati nel connettore Spark per Azure Cosmos DB](./media/spark-connector/spark-connector.png)

Il flusso di dati è il seguente:

1. Il nodo master Spark si connette al nodo del gateway Azure Cosmos DB per ottenere la mappa delle partizioni. Un utente specifica solo le connessioni di Spark e Azure Cosmos DB. Le connessioni ai rispettivi nodi master e del gateway sono trasparenti per l'utente.
2. Queste informazioni vengono restituite al nodo master Spark.  A questo punto sarà possibile analizzare la query per determinare le partizioni e le relative posizioni in Azure Cosmos DB a cui è necessario accedere.
3. Queste informazioni vengono trasmesse ai nodi di lavoro Spark.
4. I nodi di lavoro Spark si connettono direttamente alle partizioni Azure Cosmos DB per estrarre i dati e restituiscono i dati alle partizioni Spark nei nodi di lavoro Spark.

La comunicazione tra Spark e Azure Cosmos DB è notevolmente più veloce perché lo spostamento dei dati avviene tra i nodi di lavoro Spark e i nodi dati (partizioni) Azure Cosmos DB.

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>Creare il connettore Spark per Azure Cosmos DB
Attualmente il progetto del connettore usa Maven. Per creare il connettore senza le dipendenze, è possibile eseguire:
```
mvn clean package
```
È anche possibile scaricare le versioni più recenti del file JAR dalla cartella [*releases*](https://github.com/Azure/azure-cosmosdb-spark/releases).

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Includere il file JAR di Spark per Azure Cosmos DB
Prima dell'esecuzione del codice, è necessario includere il file JAR di Spark per Azure Cosmos.  Se si usa **spark-shell**, è possibile includere il file JAR usando l'opzione **--jars**.  

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0-uber.jar
```

Se si vuole eseguire il file JAR senza dipendenze, usare il codice seguente:

```bash
spark-shell --master $master --jars /$location/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar,/$location/azure-documentdb-1.14.0.jar,/$location/azure-documentdb-rx-0.9.0-rc2.jar,/$location/json-20140107.jar,/$location/rxjava-1.3.0.jar,/$location/rxnetty-0.4.20.jar 
```

Se si usa un servizio notebook, come Azure HDInsight Jupyter, è possibile usare i comandi **spark magic**:

```
%%configure
{ "name":"Spark-to-Cosmos_DB_Connector", 
  "jars": ["wasb:///example/jars/1.0.0/azure-cosmosdb-spark_2.1.0_2.11-1.0.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-1.14.0.jar", "wasb:///example/jars/1.0.0/azure-documentdb-rx-0.9.0-rc2.jar", "wasb:///example/jars/1.0.0/json-20140107.jar", "wasb:///example/jars/1.0.0/rxjava-1.3.0.jar", "wasb:///example/jars/1.0.0/rxnetty-0.4.20.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

Il comando **jars** consente di includere i due file JAR necessari per **azure-cosmosdb-spark** (il file stesso e Azure DocumentDB Java SDK) ed escludere **scala-reflect** in modo da non interferire con le chiamate Livy (notebook Jupyter > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Connettere Spark ad Azure Cosmos DB con il connettore
Anche se il trasporto di comunicazione è un po' più complesso, l'esecuzione di una query da Spark ad Azure Cosmos DB con il connettore è notevolmente più veloce.

Il frammento di codice seguente illustra come usare il connettore in una sessione di Spark. Per esempi di Python, vedere il [repository GitHub](https://github.com/Azure/azure-cosmosdb-spark) `azure-cosmosdb-spark`.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val baseConfig = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(baseConfig)
coll.createOrReplaceTempView("c")
```

Come indicato nel frammento di codice:

- **azure-cosmosdb-spark** contiene tutti i parametri di connessione necessari, che includono le posizioni preferite. È ad esempio possibile scegliere la replica di lettura e l'ordine di priorità.
- Importare semplicemente le librerie necessarie e configurare masterKey e host per creare il client Azure Cosmos DB.

### <a name="execute-spark-queries-via-the-connector"></a>Eseguire query Spark tramite il connettore

L'esempio seguente usa l'istanza di Azure Cosmos DB creata nel frammento precedente con le chiavi di sola lettura specificate. Il frammento di codice seguente consente la connessione alla raccolta DepartureDelays.flights_pcoll (nell'account DoctorWho specificato in precedenza) ed esegue una query per estrarre le informazioni sui ritardi dei voli in partenza da Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>Vantaggi dell'implementazione del connettore Spark per Azure Cosmos DB

La connessione di Spark ad Azure Cosmos DB con il connettore viene usata in genere negli scenari in cui:

* Si vuole usare Python e/o Scala.
* La quantità di dati da trasferire tra Apache Spark e Azure Cosmos DB è elevata.

Per informazioni sulle differenze a livello di prestazioni delle query, vedere la [wiki sulle esecuzioni di test di query](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Esempio di aggregazione distribuita
Questa sezione fornisce alcuni esempi di come è possibile eseguire analisi e aggregazioni distribuite combinando Apache Spark e Azure Cosmos DB. Azure Cosmos DB supporta già le aggregazioni, come illustrato nel post di blog sulle [aggregazioni su scala globale con Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Ecco come passare a un livello superiore con Apache Spark.

Si noti che queste aggregazioni fanno riferimento al [notebook del connettore Spark per Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Eseguire la connessione ai dati di esempio sui voli
Queste aggregazioni di esempio accedono ad alcuni dati sulle prestazioni dei voli archiviati nel database Azure Cosmos DB **DoctorWho**. Per connettersi al database, è necessario usare il frammento di codice seguente:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Con questo frammento si eseguirà anche una query di base che trasferisce il set di dati filtrato da Azure Cosmos DB a Spark, che potrà eseguire aggregazioni distribuite. In questo caso la richiesta è relativa ai voli in partenza da Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

I risultati seguenti sono stati generati eseguendo le query dal servizio notebook Jupyter.  Si noti che tutti i frammenti di codice sono generici e non specifici di un servizio.

### <a name="running-limit-and-count-queries"></a>Esecuzione di query LIMIT e COUNT
Come avviene in genere in SQL/Spark SQL, è possibile iniziare con una query **LIMIT**:

![Query LIMIT Spark](./media/spark-connector/spark-sql-query.png)

La query successiva è una semplice e rapida query **COUNT**:

![Query COUNT Spark](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>Query GROUP BY
In questo set successivo è possibile eseguire facilmente query **GROUP BY** sul database Azure Cosmos DB:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Grafico della query GROUP BY Spark](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Query DISTINCT, ORDER BY
Di seguito una query **DISTINCT, ORDER BY**:

![Grafico della query GROUP BY Spark](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Continuare l'analisi dei dati relativi ai voli
Per continuare l'analisi di questi dati, è possibile usare le query di esempio seguenti:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Prime 5 destinazioni (città) con ritardi in partenza da Seattle
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Grafico dei ritardi massimi Spark](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calcolare i ritardi medi per città di destinazione dei voli in partenza da Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Grafico dei ritardi medi Spark](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Passaggi successivi

Se ancora non lo si è fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) ed esplorare le risorse aggiuntive nel repository:

* [Esempi di aggregazioni distribuite](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Notebook e script di esempio](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È possibile anche esaminare la [guida ad Apache Spark SQL, DataFrame e set di dati](http://spark.apache.org/docs/latest/sql-programming-guide.html) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
