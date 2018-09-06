---
title: Connettere Apache Spark ad Azure Cosmos DB | Microsoft Docs
description: Informazioni sul connettore Spark per Azure Cosmos DB, che permette di connettere Apache Spark ad Azure Cosmos DB. È possibile eseguire aggregazioni distribuite nel sistema di database multi-tenant distribuito a livello globale di Microsoft.
keywords: Apache Spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: e7ed0049e64a7740063f2fab7bdfddff38d45ed9
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287711"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Accelerare l'analisi di Big Data con il connettore Apache Spark per Azure Cosmos DB
 
Il connettore Apache Spark per Azure Cosmos DB consente ad Azure Cosmos DB di fungere da input o da output per i processi Apache Spark. Connettendo [Spark](http://spark.apache.org/) ad [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), è possibile risolvere più velocemente problemi di data science in rapida evoluzione. È possibile usare Azure Cosmos DB per salvare in modo permanente i dati e sottoporli a query in tempi rapidi. Il connettore usa in modo efficiente gli indici gestiti nativi di Azure Cosmos DB. Gli indici consentono colonne aggiornabili in fase di analisi e propagazione del filtro predicato per i dati distribuiti a livello globale in rapida evoluzione. Questo tipo di dati spazia da scenari IoT (Internet delle cose) a scenari di data science e analisi.

## <a name="connector-components"></a>Componenti del connettore

Il connettore Spark per Azure Cosmos DB ha i componenti seguenti:

* [Azure Cosmos DB](http://documentdb.com), che consente di effettuare il provisioning e di ridimensionare in modo elastico la velocità effettiva e le risorse di archiviazione in un numero qualsiasi di aree.  

* [Apache Spark](http://spark.apache.org/), un potente motore di elaborazione open-source incentrato su velocità, semplicità d'uso e analisi avanzata.  

* [Cluster Apache Spark in Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html), che consente di eseguire processi Spark nel cluster Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Connettere Apache Spark a Azure Cosmos DB

Per la connessione di Apache Spark e Azure Cosmos DB sono disponibili due approcci:

- [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), un connettore basato su Python, detto anche *pyDocumentDB*.  

- [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java), un connettore basato su Java.


**Versioni supportate**

| Componente | Version |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Versione del runtime di Azure Databricks | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Connettersi usando Python o pyDocumentDB SDK

Il diagramma seguente illustra l'architettura dell'implementazione di pyDocumentDB SDK:

![Diagramma del flusso di dati da Spark ad Azure Cosmos DB tramite pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Flusso di dati

Il flusso di dati dell'implementazione di pyDocumentDB si presenta come specificato in seguito:

* Il nodo master Spark si connette al nodo del gateway Azure Cosmos DB tramite pyDocumentDB. Vengono specificate solo le connessioni di Spark e Azure Cosmos DB. Le connessioni ai rispettivi nodi master e del gateway sono trasparenti.  

* Il nodo del gateway esegue la query su Azure Cosmos DB, dove la query viene successivamente eseguita sulle partizioni della raccolta nei nodi dati. La risposta a queste query viene inviata di nuovo al nodo del gateway e il set di risultati viene restituito al nodo master Spark.  

* Le query successive, ad esempio su un frame di dati Spark, vengono inviate ai nodi di lavoro Spark per l'elaborazione.  

La comunicazione tra Spark e Azure Cosmos DB è limitata al nodo master Spark e ai nodi del gateway Azure Cosmos DB. Le query vengono eseguite alla velocità consentita dal livello di trasporto tra i due nodi.

Eseguire la procedura seguente per connettere Spark ad Azure Cosmos DB mediante pyDocumentDB SDK:

1. Creare un'[area di lavoro di Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e un [cluster Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Il runtime di Azure Databricks versione 4.0 include Apache Spark 2.3.0 e Scala 2.11 nell'area di lavoro.  

2. Una volta che il cluster è stato creato ed è in esecuzione, passare ad **Area di lavoro** > **Crea** > **Libreria**.  
3. Nella finestra di dialogo Nuova libreria scegliere **Upload Python Egg or PyPi** (Carica Python Egg o PyPi) come origine. Specificare **pydocumentdb** come nome e selezionare **Install Library** (Installa libreria). PyDocumentdb SDK è già pubblicato nei pacchetti pip, quindi è possibile individuarlo e installarlo. 

   ![Screenshot della finestra di dialogo Nuova libreria](./media/spark-connector/create-library.png)

4. Dopo aver installato la libreria, collegarla al cluster creato in precedenza.  

5. Passare ad **Area di lavoro** > **Crea** > **Notebook**.  

6. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome descrittivo e scegliere **Python** come linguaggio. Nell'elenco a discesa selezionare il cluster creato in precedenza e quindi **Crea**.  

7. Eseguire alcune query Spark usando i dati di esempio sui voli ospitati nell'account Azure Cosmos DB "doctorwho". Questo account è accessibile pubblicamente. Il repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) ospita la versione HTML del notebook. Scaricare i file del repository e passare a `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`. È possibile importare il notebook nell'account di Azure Databricks ed eseguirlo. La sezione seguente illustra le funzionalità dei blocchi di codice in dettaglio.

Il frammento di codice seguente illustra come importare pyDocumentDB SDK ed eseguire una query nel contesto di Spark. Come indicato nel frammento di codice, l'SDK di pyDocumentDB contiene i parametri di connessione necessari per connettersi all'account Azure Cosmos DB. Importa le librerie necessarie e configura la chiave master e l'host, per creare il client Azure Cosmos DB (pydocumentdb.document_client).


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

È quindi possibile eseguire le query. Il frammento di codice seguente si connette alla raccolta airports.codes nell'account doctorwho ed esegue una query per estrarre le città con aeroporto nello stato di Washington. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

Dopo l'esecuzione della query, il risultato è un oggetto "query_iterable.QueryIterable" che viene convertito in un elenco Python. Questo elenco, a sua volta, viene convertito in un frame di dati Spark. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Considerazioni per l'uso di pyDocumentDB SDK
La connessione di Spark ad Azure Cosmos DB mediante pyDocumentDB SDK è una procedura consigliata per gli scenari seguenti:

* Si vuole usare Python.  

* Si restituisce un set di risultati relativamente piccolo da Azure Cosmos DB a Spark. Si noti che il set di dati sottostante in Azure Cosmos DB può essere piuttosto grande e vengono applicati filtri o eseguiti filtri predicati nell'origine Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Connettersi usando Java SDK

Il diagramma seguente mostra l'architettura dell'implementazione di Azure Cosmos DB SQL Java SDK e gli spostamenti dei dati tra i nodi di lavoro Spark:

![Diagramma del flusso di dati nel connettore Spark per Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Flusso di dati

Il flusso di dati dell'implementazione di Java SDK si presenta come specificato in seguito:

* Il nodo master Spark si connette al nodo del gateway Azure Cosmos DB per ottenere la mappa delle partizioni. Vengono specificate solo le connessioni di Spark e Azure Cosmos DB. Le connessioni ai rispettivi nodi master e del gateway sono trasparenti.  

* Queste informazioni vengono restituite al nodo master Spark. A questo punto sarà possibile analizzare la query per determinare le partizioni e le relative posizioni in Azure Cosmos DB a cui è necessario accedere.  

* Queste informazioni vengono trasmesse ai nodi di lavoro Spark.  

* I nodi di lavoro Spark si connettono direttamente alle partizioni Azure Cosmos DB per estrarre i dati e restituirli alle partizioni Spark nei nodi di lavoro.  

La comunicazione tra Spark e Azure Cosmos DB è notevolmente più veloce perché lo spostamento dei dati avviene tra i nodi di lavoro Spark e i nodi dati (partizioni) Azure Cosmos DB. In questo esempio si inviano dati di Twitter di esempio all'account Azure Cosmos DB e si eseguono query Spark usando tali dati. Per scrivere i dati di Twitter di esempio in Azure Cosmos DB, procedere come segue:

1. Creare un [account API SQL di Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) e [aggiungere una raccolta](create-sql-api-dotnet.md#add-a-collection) all'account.  

2. Scaricare l'esempio [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) da GitHub. È possibile usare questo feed per scrivere dati di Twitter di esempio in Azure Cosmos DB.  

3. Aprire un prompt dei comandi e installare i moduli Tweepy e pyDocumentdb eseguendo i comandi seguenti:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Estrarre i contenuti del feed di Twitter di esempio e aprire il file config.py. Aggiornare i valori masterKey, pdatabaseId, collectionId e preferredLocations.  

5. Passare a `http://apps.twitter.com/` e registrare lo script del feed Twitter come nuova applicazione. Dopo aver selezionato un nome e un'applicazione per l'app, vengono forniti: **chiave utente, segreto del cliente, token di accesso e segreto del token di accesso**. Copiare questi valori e aggiornarli nel file config.py per fornire all'applicazione l'accesso a livello di codice a Twitter.   

6. Salvare il file config.py. Aprire un prompt dei comandi ed eseguire l'applicazione Python usando il comando seguente:

   ```bash
   Python driver.py
   ```

7. Passare alla raccolta di Azure Cosmos DB nel portale e verificare che i dati di Twitter vengano scritti nella raccolta.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Trovare Java SDK e collegarlo al cluster Spark

1. Creare un'[area di lavoro di Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e un [cluster Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Il runtime di Azure Databricks versione 4.0 include Apache Spark 2.3.0 e Scala 2.11 nell'area di lavoro.  

2. Una volta che il cluster è stato creato ed è in esecuzione, passare ad **Area di lavoro** > **Crea** > **Libreria**.  

3. Nella finestra di dialogo **Nuova libreria** scegliere **Maven Coordinate** (Coordinata Maven) come origine. Specificare il valore della coordinata **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0** e selezionare **Create Library** (Crea libreria). Le dipendenze Maven vengono risolte e il pacchetto viene aggiunto all'area di lavoro. Nel formato della coordinata Maven precedente, 2.3.0 rappresenta la versione di Spark, 2.11 rappresenta la versione di Scala e 1.2.0 rappresenta la versione del connettore Azure Cosmos DB. 

4. Dopo aver installato la libreria, collegarla al cluster creato in precedenza. 

Questo articolo illustra l'uso del connettore Spark per Java SDK negli scenari seguenti:

* Leggere dati di Twitter da Azure Cosmos DB.  

* Leggere dati di Twitter trasmessi ad Azure Cosmos DB.  

* Scrivere dati di Twitter in Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Leggere dati di Twitter da Azure Cosmos DB
 
In questa sezione si eseguono query Spark per leggere un batch di dati di Twitter da Azure Cosmos DB. Il repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) ospita la versione HTML del notebook. Scaricare i file del repository e passare a `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html`. È possibile importare il notebook nell'account di Azure Databricks e aggiornare l'URI dell'account, la chiave master, il database e i nomi delle raccolte. È possibile eseguire il notebook o crearlo come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**. 

2. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome descrittivo e scegliere **Python** come linguaggio. Nell'elenco a discesa selezionare il cluster creato in precedenza e quindi **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi all'account. Leggere i tweet usando il comando spark.read.format().

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Eseguire la query per ottenere il conteggio dei tweet per diversi hashtag dai dati memorizzati nella cache. 

   ```python
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

Java SDK supporta i seguenti valori per il mapping di configurazione: 

|Impostazione  |DESCRIZIONE  |
|---------|---------|
|query_maxdegreeofparallelism  | Imposta il numero di operazioni simultanee eseguite sul lato client durante l'esecuzione di query in parallelo. Se è impostato su un valore maggiore di 0, limita il numero di operazioni simultanee per il valore assegnato. Se è impostato un valore minore di 0, il sistema decide automaticamente il numero di operazioni simultanee da eseguire. Poiché il connettore esegue il mapping di ogni partizione della raccolta con un executor, questo valore non ha alcun effetto sull'operazione di lettura.        |
|query_maxbuffereditemcount     |    Imposta il numero massimo di elementi che possono essere memorizzati nel buffer sul lato client durante l'esecuzione di query parallele. Se è impostato su un valore maggiore di 0, limita il numero di elementi memorizzati nel buffer per il valore assegnato. Se è impostato un valore minore di 0, il sistema decide automaticamente il numero di elementi memorizzati nel buffer.     |
|query_enablescan    |   Imposta l'opzione per abilitare le analisi delle query che non è stato possibile gestire perché l'indicizzazione è stata rifiutata esplicitamente nei percorsi richiesti.       |
|query_disableruperminuteusage  |  Disabilita la capacità di unità richiesta (UR) al minuto per la gestione della query se le normali unità richiesta (UR) al secondo di cui è stato eseguito il provisioning sono state esaurite.       |
|query_emitverbosetraces   |   Imposta l'opzione per consentire alle query di generare tracce dettagliate per l'analisi.      |
|query_pagesize  |   Imposta le dimensioni della pagina dei risultati della query per ogni richiesta di query. Per ottimizzare la velocità effettiva, usare dimensioni di pagina ampie per ridurre il numero di round trip per il recupero dei risultati.      |
|query_custom  |  Imposta la query di Azure Cosmos DB per eseguire l'override della query predefinita durante il recupero di dati da Azure Cosmos DB. Tenere presente che, quando questo valore viene specificato, viene usato anche al posto di una query con predicati propagati.     |

A seconda dello scenario, è opportuno usare valori di configurazione diversi per ottimizzare le prestazioni e la velocità effettiva. Si noti che la chiave di configurazione attualmente non presta attenzione tra maiuscole e minuscole e il valore di configurazione è sempre una stringa.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Leggere dati di Twitter trasmessi ad Azure Cosmos DB

In questa sezione si eseguono query di Spark per la lettura di un feed di modifiche di dati in streaming di Twitter. Durante l'esecuzione delle query in questa sezione, assicurarsi che l'app dei feed di Twitter sia in esecuzione e che stia distribuendo dati ad Azure Cosmos DB. Il repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) ospita la versione HTML del notebook. Scaricare i file del repository e passare a `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`. È possibile importare il notebook nell'account di Azure Databricks e aggiornare l'URI dell'account, la chiave master, il database e i nomi delle raccolte. È possibile eseguire il notebook o crearlo come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**.  

2. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome descrittivo e scegliere **Scala** come linguaggio. Nell'elenco a discesa selezionare il cluster creato in precedenza e quindi **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi all'account.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Iniziare la lettura del feed di modifiche come flusso usando il comando spark.readStream.format():

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Iniziare la trasmissione della query alla console:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK supporta i seguenti valori per il mapping di configurazione:

|Impostazione  |DESCRIZIONE  |
|---------|---------|
|readchangefeed   |  Indica che il contenuto della raccolta viene recuperato dal feed delle modifiche di CosmosDB. Il valore predefinito è False.       |
|changefeedqueryname |   Stringa univoca che identifica la query. Il connettore tiene traccia separatamente dei token di continuazione della raccolta per diverse query dei feed delle modifiche. Se readchangefeed è true, si tratta di una configurazione necessaria che non accetta un valore vuoto.      |
|changefeedcheckpointlocation  |   Un percorso all'archivio file locale per rendere persistenti i token di continuazione in caso di errori dei nodi.      |
|changefeedstartfromthebeginning  |  Imposta se il feed delle modifiche deve iniziare dall'inizio (true) o dal punto corrente (false). Per impostazione predefinita, inizia dalla posizione corrente (false).       |
|rollingchangefeed  |   Un valore booleano che indica se il feed di modifiche debba appartenere all'ultima query. Il valore predefinito è false, che significa che le modifiche vengono conteggiate dalla prima lettura della raccolta.      |
|changefeedusenexttoken  |   Un valore booleano per supportare gli scenari di errore di elaborazione. Indica che il batch di feed di modifiche corrente è stato gestito correttamente. Il set di dati distribuito resiliente deve usare i token di continuazione successivi per ottenere i batch di modifiche successivi.      |
| InferStreamSchema | Valore booleano che indica se lo schema dei dati in streaming deve essere campionato all'inizio della trasmissione. Per impostazione predefinita, questo valore è impostato su true. Se questo parametro è impostato su true e lo schema di dati di streaming cambia dopo che i dati vengono campionati, verranno eliminate le proprietà appena aggiunte nel frame di dati di streaming. <br/><br/> Se si desidera che il frame di dati di streaming sia indipendente dallo schema, impostare questo parametro su false. In questa modalità, viene eseguito il wrapping del corpo dei documenti letti dal feed di modifiche di Azure Cosmos DB in una proprietà body. Questa proprietà si trova nel frame di dati in streaming risultante, separata dai valori delle proprietà di sistema.
 |

### <a name="connection-settings"></a>Impostazioni di connessione

Java SDK supporta le seguenti impostazioni di connessione:

|Impostazione  |DESCRIZIONE  |
|---------|---------|
|connectionmode   |  Imposta la modalità di connessione che il client DocumentClient interno deve usare per comunicare con Azure Cosmos DB. I valori consentiti sono **DirectHttps** (valore predefinito) e **Gateway**. La modalità di connessione DirectHttps instrada le richieste direttamente alle partizioni CosmosDB e fornisce alcuni vantaggi di latenza.       |
|connectionmaxpoolsize   |  Imposta il valore della dimensione del pool di connessioni usato dal DocumentClient interno. Il valore predefinito è 100.       |
|connectionidletimeout  |  Imposta il valore di timeout, in secondi, per le connessioni inattive. Il valore predefinito è 60.       |
|query_maxretryattemptsonthrottledrequests    |  Imposta il numero massimo di tentativi. Questo valore viene usato in caso di errore della richiesta a causa della limitazione di velocità del client. Se non è specificato, il valore predefinito è pari a 1000 tentativi.       |
|query_maxretrywaittimeinseconds   |  Imposta il tempo massimo per i tentativi in secondi. Il valore predefinito è 1000 secondi.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Scrivere dati di Twitter in Azure Cosmos DB 

In questa sezione vengono eseguite query Spark per scrivere un batch di dati di Twitter in una nuova raccolta nello stesso database. Il repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) ospita la versione HTML del notebook. Scaricare i file del repository e passare a `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`. È possibile importare il notebook nell'account di Azure Databricks e aggiornare l'URI dell'account, la chiave master, il database e i nomi delle raccolte. È possibile eseguire il notebook o crearlo come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**.  

2. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome descrittivo e scegliere **Scala** come linguaggio. Nell'elenco a discesa selezionare il cluster creato in precedenza e quindi **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi alla raccolta di database per leggere e scrivere dati di Twitter.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Eseguire la query per ottenere il conteggio dei tweet per diversi hashtag dai dati memorizzati nella cache. 

   ```scala
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

5. Creare un nuovo frame di dati di tag di tweet e salvare i dati nella nuova raccolta. Dopo aver eseguito il codice seguente, è possibile tornare al portale e verificare che i dati siano scritti nella raccolta. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Java SDK supporta i seguenti valori per il mapping di configurazione:

|Impostazione  |DESCRIZIONE  |
|---------|---------|
| BulkImport | Un valore booleano che indica se i dati devono essere importati usando la libreria di BulkExecutor. Per impostazione predefinita, questo valore è impostato su true. |
|WritingBatchSize  |   Indica le dimensioni del batch da usare quando si scrivono i dati nella raccolta di Azure Cosmos DB. <br/><br/> Se il parametro BulkImport è impostato su true, il parametro WritingBatchSize indica la dimensione del batch di documenti forniti come input per l'API importAll della libreria di BulkExecutor. Per impostazione predefinita, questo valore è impostato su 100K. <br/><br/> Se il parametro BulkImport è impostato su false, il parametro WritingBatchSize indica le dimensioni del batch da usare quando si scrive nella raccolta di Azure Cosmos DB. Il connettore invia le richieste createDocument e upsertDocument in modo asincrono in batch. Maggiori sono le dimensioni del batch, maggiore sarà la velocità effettiva che è possibile raggiungere, a condizione che le risorse del cluster siano disponibili. D'altra parte, specificare un numero minore di dimensioni del batch per limitare la frequenza e il consumo di UR. Per impostazione predefinita, le dimensioni del batch di scrittura sono impostate su 500.  |
|Upsert   |  Stringa con un valore booleano che indica se usare upsertDocument invece di CreateDocument quando si scrive nella raccolta di Azure Cosmos DB.   |
| WriteThroughputBudget |  Stringa con un valore intero che rappresenta il numero di UR da allocare al processo Spark di inserimento in blocco rispetto alla velocità effettiva totale allocata alla raccolta. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Scrivere dati di Twitter trasmessi ad Azure Cosmos DB 

In questa sezione vengono eseguite query Spark per scrivere un feed di modifiche di dati in streaming di Twitter in una nuova raccolta nello stesso database. Il repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) ospita la versione HTML del notebook. Scaricare i file del repository e passare a `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`. È possibile importare il notebook nell'account di Azure Databricks e aggiornare l'URI dell'account, la chiave master, il database e i nomi delle raccolte. È possibile eseguire il notebook o crearlo come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**.  

2. Nella finestra di dialogo **Create Notebook** (Crea un notebook) immettere un nome descrittivo e scegliere **Scala** come linguaggio. Nell'elenco a discesa selezionare il cluster creato in precedenza e quindi **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi alla raccolta di database per leggere e scrivere dati di Twitter.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Iniziare la lettura del feed di modifiche come flusso usando il comando spark.readStream.format():
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Definire la configurazione della raccolta di destinazione e avviare il processo di streaming usando il metodo writeStream.format():

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Java SDK supporta i seguenti valori per il mapping di configurazione:

|Impostazione  |DESCRIZIONE  |
|---------|---------|
|Upsert   |  Stringa con un valore booleano che indica se usare upsertDocument invece di CreateDocument quando si scrive nella raccolta di Azure Cosmos DB.   |
|checkpointlocation  |   Un percorso all'archivio file locale per rendere persistenti i token di continuazione in caso di errori dei nodi.   |
|WritingBatchSize  |  Indica le dimensioni del batch da usare quando si scrivono i dati nella raccolta di Azure Cosmos DB. Il connettore invia le richieste createDocument e upsertDocument in modo asincrono in batch. Maggiori sono le dimensioni del batch, maggiore sarà la velocità effettiva che è possibile raggiungere, a condizione che le risorse del cluster siano disponibili. D'altra parte, specificare un numero minore di dimensioni del batch per limitare la frequenza e il consumo di UR. Per impostazione predefinita, le dimensioni del batch di scrittura sono impostate su 500.  |


### <a name="considerations-when-using-java-sdk"></a>Considerazioni per l'uso di Java SDK

La connessione di Spark ad Azure Cosmos DB mediante Java SDK è una procedura consigliata per gli scenari seguenti:

* Si vuole usare Python o Scala.  

* La quantità di dati da trasferire tra Apache Spark e Azure Cosmos DB è elevata. Java SDK offre prestazioni migliori rispetto a pyDocumentDB. Per altre informazioni sulle differenze a livello di prestazioni delle query, vedere l'articolo [wiki sulle esecuzioni dei test di query](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Passaggi successivi

Se ancora non è stato fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Esplorare le risorse aggiuntive seguenti nel repository:

* [Aggregations examples (Esempi di aggregazioni)](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Sample scripts and notebooks (Notebook e script di esempio)](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È anche possibile vedere [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guida ad Apache Spark SQL, frame di dati e set di dati) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
