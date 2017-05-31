---
title: Connessione di Apache Spark ad Azure Cosmos DB | Microsoft Docs
description: Usare questa esercitazione per ottenere informazioni sul connettore Spark per Azure Cosmos DB, che consente di connettere Apache Spark ad Azure Cosmos DB per eseguire analisi scientifiche dei dati e aggregazioni distribuite nel sistema di database multi-tenant con distribuzione globale di Microsoft progettato per il cloud.
keywords: Apache Spark
services: cosmosdb
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: denlee
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6a5db515bec785aa29b29a3096dc20a72056bd08
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---

# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>Velocizzare l'analisi di Big Data in tempo reale con il connettore Spark per Azure Cosmos DB

Il connettore Spark per Azure Cosmos DB consente a Cosmos DB di fungere da origine di input o sink di output per i processi Apache Spark. Connettendo [Spark](http://spark.apache.org/) ad [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/), è possibile risolvere più velocemente problemi di analisi scientifica dei dati in rapida evoluzione, perché i dati possono essere rapidamente salvati in modo permanente e sottoposti a query usando Cosmos DB. Il connettore Spark per Azure Cosmos DB usa in modo efficiente gli indici gestiti nativi di Cosmos DB e supporta colonne aggiornabili durante l'analisi e la propagazione di filtri predicato su dati distribuiti a livello globale in rapida evoluzione, spaziando da IoT ad analisi scientifica dei dati e scenari di analisi. 

## <a name="download"></a>Scaricare

Per iniziare, scaricare il connettore Spark per Azure Cosmos DB (anteprima) dal repository [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark/) in GitHub.

## <a name="connector-components"></a>Componenti del connettore

Sono i seguenti:

* [Azure Cosmos DB](http://documentdb.com), il [sistema di database multi-tenant con distribuzione globale](documentdb-distribute-data-globally.md) di Microsoft progettato per il cloud. Azure Cosmos DB consente ai clienti di effettuare il provisioning e ridimensionare in modo elastico la velocità effettiva e lo spazio di archiviazione in un numero illimitato di aree geografiche. Il servizio offre bassa latenza garantita al livello p99, disponibilità elevata al 99,99% e [più modelli di coerenza ben definiti](documentdb-consistency-levels.md) agli sviluppatori.

* [Apache Spark](http://spark.apache.org/), un potente motore di elaborazione open-source incentrato su velocità, semplicità d'uso e analisi avanzata. 

* [Apache Spark in Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). È possibile distribuire Apache Spark nel cloud per le distribuzioni strategiche tramite [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Versioni supportate ufficialmente:

| Componente | Versione |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Azure DocumentDB Java SDK | 1.9.6 |
 
Questo articolo consente di eseguire alcuni semplici esempi con Python (tramite pyDocumentDB) e l'interfaccia Scala.

Per la connessione di Apache Spark e Azure Cosmos DB sono disponibili due approcci:
- Usare pyDocumentDB tramite [Azure DocumentDB Python SDK](https://github.com/Azure/azure-documentdb-python).
- Creare un connettore Spark per DocumentDB basato su Java usando [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>Implementazione di pyDocumentDB 
La versione corrente dell'[SDK di pyDocumentDB](https://github.com/Azure/azure-documentdb-python) consente di connettere Spark a Cosmos DB come illustrato nel diagramma seguente:

![Flusso di dati da Spark a Cosmos DB tramite pyCosmos DB](./media/documentdb-spark-connector/azure-documentdb-spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>Flusso di dati dell'implementazione di pyDocumentDB

Il flusso di dati è il seguente:

1. Viene stabilita la connessione dal nodo master Spark al nodo del gateway Cosmos DB tramite pyCosmos DB.  Si noti che l'utente specifica solo le connessioni di Spark e Cosmos DB e il fatto che venga stabilita la connessione ai rispettivi nodi master e del gateway è trasparente per l'utente.
2. Tramite il nodo del gateway viene eseguita la query su Cosmos DB, in cui viene successivamente eseguita sulle partizioni della raccolta nei nodi dati. La risposta a queste query viene inviata di nuovo al nodo del gateway e il set di risultati viene restituito al nodo master Spark.
3. Tutte le query successive, ad esempio in un frame di dati Spark, vengono inviate ai nodi del ruolo di lavoro Spark per l'elaborazione.

L'aspetto importante è che la comunicazione tra Spark e Cosmos DB è limitata al nodo master Spark e ai nodi del gateway Cosmos DB.  Le query vengono eseguite alla velocità del livello di trasporto compreso tra questi due nodi.

### <a name="installing-pydocumentdb"></a>Installazione di pyDocumentDB
È possibile installare pyDocumentDB sul nodo del driver usando **pip**, ad esempio:

```
pip install pyDocumentDB
```


### <a name="connecting-spark-to-cosmos-db-via-pycosmos-db"></a>Connessione di Spark a Cosmos DB tramite pyCosmos DB 
In cambio della semplicità del trasporto di comunicazione, eseguire una query da Spark a Cosmos DB con pyCosmos DB è relativamente semplice.

Il frammento di codice seguente illustra come usare pyDocumentDB all'interno di un contesto Spark.

```
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery 
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]


# Set keys to connect to Cosmos DB 
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==' 
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)
```

Come indicato nel frammento di codice:

* DocumentDB Python SDK contiene tutti i parametri di connessione necessari, tra cui i percorsi preferiti, ovvero la scelta della lettura della replica e l'ordine di priorità.
*  Importare le librerie necessarie e configurare **masterKey** e **host** per creare il *client* Cosmos DB (**pydocumentdb.document_client**).


### <a name="executing-spark-queries-via-pydocumentdb"></a>Esecuzione di query Spark tramite pyDocumentDB
Gli esempi seguenti usano l'istanza di Cosmos DB creata nel frammento precedente con le chiavi di sola lettura specificate.  Il frammento di codice seguente si connette alla raccolta **airports.codes** (nell'account DoctorWho specificato in precedenza) eseguendo una query per estrarre le città con aeroporto nello stato di Washington. 

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Cosmos DB client will use to connect to the database and collection
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

Dopo l'esecuzione della query tramite **query**, il risultato è **query_iterable.QueryIterable** che viene convertito in un elenco Python. È possibile convertire facilmente un elenco Python in un frame di dati Spark usando il codice seguente:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-documentdb"></a>Perché usare pyDocumentDB per connettere Spark a DocumentDB?
La connessione di Spark a Cosmos DB con pyCosmos DB viene usata in genere negli scenari in cui:

* Si intende usare Python.
* Si restituisce un set di risultati relativamente piccolo da DocumentDB a Spark.  Si noti che il set di dati sottostante in DocumentDB può essere piuttosto grande. Questa operazione non consiste semplicemente nell'applicare filtri, ovvero eseguire filtri in base a predicati nell'origine DocumentDB.  

## <a name="spark-to-cosmos-db-connector"></a>Connettore Spark per Cosmos DB

Il connettore Spark per Cosmos DB usa [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java) e sposta i dati tra i nodi di lavoro Spark e Cosmos DB come illustrato nel diagramma seguente:

![Flusso di dati nel connettore Spark per Cosmos DB](./media/documentdb-spark-connector/azure-documentdb-spark-connector.png)

Il flusso di dati è il seguente:

1. Viene stabilita una connessione dal nodo master Spark al nodo del gateway Cosmos DB per ottenere la mappa delle partizioni.  Si noti che l'utente specifica solo le connessioni di Spark e Cosmos DB e il fatto che venga stabilita la connessione ai rispettivi nodi master e del gateway è trasparente per l'utente.
2. Queste informazioni vengono restituite al nodo master Spark.  A questo punto sarà possibile analizzare la query per determinare le partizioni all'interno di Cosmos DB a cui è necessario accedere e le relative posizioni.
3. Queste informazioni vengono trasmesse ai nodi del ruolo di lavoro Spark.
4. Ciò consente ai nodi di lavoro Spark di connettersi direttamente alle partizioni Cosmos DB per estrarre i dati necessari e restituire i dati alle partizioni Spark all'interno dei nodi di lavoro Spark.

L'aspetto importante è che la comunicazione tra Spark e Cosmos DB è notevolmente più veloce perché lo spostamento dei dati avviene tra i nodi di lavoro Spark e i nodi dati (partizioni) Cosmos DB.

### <a name="building-the-spark-to-cosmos-db-connector"></a>Creazione del connettore Spark per Cosmos DB
Attualmente il progetto del connettore usa Maven. Per creare il connettore senza le dipendenze, è possibile eseguire:
```
mvn clean package
```
È possibile anche scaricare le versioni più recenti del file con estensione jar all'interno della cartella *releases*.

### <a name="including-the-azure-documentdb-spark-jar"></a>Inclusione del file JAR di Azure DocumentDB Spark
Prima dell'esecuzione del codice, è necessario includere il file JAR di Azure DocumentDB Spark.  Se si usa **spark-shell**, è possibile includere il file JAR usando l'opzione **--jars**.  

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1-jar-with-dependencies.jar
```

In alternativa, se si vuole eseguire il file con estensione jar senza dipendenze:

```
spark-shell --master $master --jars /$location/azure-documentdb-spark-0.0.1.jar,/$location/azure-documentdb-1.9.6.jar
```

Se si usa un servizio notebook, come Azure HDInsight Jupyter, è possibile usare i comandi **spark magic**:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.9.6.jar","wasb:///example/jars/azure-documentdb-spark-0.0.1.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

Il comando **jars** consente di includere i due file con estensione jar necessari per **azure-documentdb-spark** (il file stesso e Azure DocumentDB Java SDK) ed escludere **scala-reflect** in modo da non interferire con le chiamate Livy eseguite (notebook Jupyter > Livy > Spark).

### <a name="connecting-spark-to-cosmos-db-using-the-connector"></a>Connessione di Spark a Cosmos DB con il connettore
Nonostante il trasporto di comunicazione sia relativamente più complesso, l'esecuzione di una query da Spark a Cosmos DB con il connettore è notevolmente più veloce.

Il frammento di codice seguente illustra come usare il connettore all'interno di un contesto Spark.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))
 
// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Come indicato nel frammento di codice:

- **azure-documentdb-spark** contiene tutti i parametri di connessione necessari, tra cui i percorsi preferiti, ad esempio, la scelta della lettura della replica e l'ordine di priorità.
- Importare le librerie necessarie e configurare masterKey e host per creare il client Cosmos DB.

### <a name="executing-spark-queries-via-the-connector"></a>Esecuzione di query Spark tramite il connettore

L'esempio seguente usa l'istanza di Cosmos DB creata nel frammento precedente con le chiavi di sola lettura specificate. Il frammento di codice seguente consente la connessione alla raccolta DepartureDelays.flights_pcoll (nell'account DoctorWho specificato in precedenza) eseguendo una query per estrarre le informazioni sui ritardi relativi ai voli in partenza da Seattle.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-cosmos-db-connector-implementation"></a>Vantaggi dell'implementazione del connettore Spark per Cosmos DB

La connessione di Spark a Cosmos DB con il connettore viene usata in genere negli scenari in cui:

* Si vuole usare Scala e aggiornarlo in modo da includere un wrapper Python come indicato in [Issue 3: Add Python wrapper and examples](https://github.com/Azure/azure-documentdb-spark/issues/3) (Problema 3: aggiungere il wrapper Python ed esempi).
* La quantità di dati da trasferire tra Apache Spark e Cosmos DB è elevata.

Per informazioni sulle differenze a livello di prestazioni delle query, vedere la [wiki sulle esecuzioni di test di query](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Esempio di aggregazione distribuita
Questa sezione contiene alcuni esempi di come è possibile eseguire analisi e aggregazioni distribuite combinando Apache Spark e Azure Cosmos DB.  Si noti che Azure Cosmos DB include già il supporto per le aggregazioni, come illustrato nel post di blog sulle [aggregazioni su scala globale con Azure Cosmos DB](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). In questo articolo viene quindi descritto come passare a un livello superiore con Apache Spark.

Queste aggregazioni fanno riferimento al [notebook del connettore Spark per Cosmos DB](https://github.com/Azure/azure-documentdb-spark/blob/master/samples/notebooks/Spark-to-Cosmos DB_Connector.ipynb).

### <a name="connecting-to-flights-sample-data"></a>Connessione ai dati di esempio sui voli
Per questi esempi di aggregazioni, si accede ad alcuni dati sulle prestazioni dei voli archiviati nel database Cosmos DB **DoctorWho**.  Per connettersi al database, è necessario usare il frammento di codice seguente:

```
// Import Spark to Cosmos DB connector
import com.microsoft.azure.documentdb.spark.schema._
import com.microsoft.azure.documentdb.spark._
import com.microsoft.azure.documentdb.spark.config.Config

// Connect to Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll", 
"SamplingRatio" -> "1.0"))

// Create collection connection 
val coll = spark.sqlContext.read.DocumentDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Con questo frammento si eseguirà anche una query di base che trasferisce il set di dati filtrato da Cosmos DB a Spark, che potrà eseguire aggregazioni distribuite.  In questo caso la richiesta è relativa ai voli in partenza da Seattle (SEA).

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

I risultati seguenti sono stati generati eseguendo le query dal servizio notebook Jupyter.  Si noti che tutti i frammenti di codice sono generici e non specifici di un servizio.

### <a name="running-limit-and-count-queries"></a>Esecuzione di query LIMIT e COUNT
Come avviene in genere in SQL/Spark SQL, è possibile iniziare con una query **LIMIT**:

![Query LIMIT Spark](./media/documentdb-spark-connector/azure-documentdb-spark-sql-query.png)

La query successiva è una semplice e rapida query **COUNT**:

![Query COUNT Spark](./media/documentdb-spark-connector/azure-documentdb-spark-count-query.png)

### <a name="group-by-query"></a>Query GROUP BY
In questo set successivo è possibile eseguire facilmente query **GROUP BY** sul database Cosmos DB:

```
select destination, sum(delay) as TotalDelays 
from originSEA 
group by destination 
order by sum(delay) desc limit 10
```

![Grafico della query GROUP BY Spark](./media/documentdb-spark-connector/azure-documentdb-group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>Query DISTINCT, ORDER BY
Di seguito una query **DISTINCT, ORDER BY**:

![Grafico della query GROUP BY Spark](./media/documentdb-spark-connector/azure-documentdb-order-by-query.png)

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
![Grafico dei ritardi massimi Spark](./media/documentdb-spark-connector/azure-documentdb-top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Calcolare i ritardi medi per città di destinazione dei voli in partenza da Seattle
```
select destination, percentile_approx(delay, 0.5) as median_delay 
from originSEA
where delay < 0 
group by destination 
order by percentile_approx(delay, 0.5)
```

![Grafico dei ritardi medi Spark](./media/documentdb-spark-connector/azure-documentdb-median-delays-graph.png)

## <a name="next-steps"></a>Passaggi successivi

Se non è stata ancora eseguita questa operazione, scaricare il connettore Spark per Cosmos DB dal repository GitHub [azure-documentdb-spark](https://github.com/Azure/azure-documentdb-spark) ed esplorare le risorse aggiuntive nel repository:

* [Esempi di aggregazioni distribuite](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Notebook e script di esempio](https://github.com/Azure/azure-documentdb-spark/tree/master/samples)

È possibile anche esaminare la [guida ad Apache Spark SQL, ai DataFrame e ai set di dati](http://spark.apache.org/docs/latest/sql-programming-guide.html) e l'articolo [Apache Spark in Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md).



