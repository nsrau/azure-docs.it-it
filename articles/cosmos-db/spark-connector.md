---
title: Connessione di Apache Spark ad Azure Cosmos DB | Microsoft Docs
description: Usare questa esercitazione per ottenere informazioni sul connettore Spark per Azure Cosmos DB, che consente di connettere Apache Spark ad Azure Cosmos DB per eseguire attività di data science e aggregazioni distribuite nel sistema di database multi-tenant con distribuzione globale di Microsoft progettato per il cloud.
keywords: Apache Spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113948"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Velocizzare l'analisi di Big Data in tempo reale con il connettore Spark per Azure Cosmos DB
 
Il connettore Spark per Azure Cosmos DB consente ad Azure Cosmos DB di fungere da input o da output per i processi Apache Spark. Connettendo [Spark](http://spark.apache.org/) ad [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), è possibile risolvere più velocemente problemi di data science in rapida evoluzione usando Azure Cosmos DB per salvare in modo permanente i dati ed eseguire query su di essi in modo rapido. Il connettore Spark per Azure Cosmos DB usa in modo efficiente gli indici gestiti nativi di Azure Cosmos DB. Gli indici consentono colonne aggiornabili in fase di analisi e propagazione del filtraggio in base al predicato per i dati distribuiti a livello globale in rapida evoluzione, che spaziano da scenari IoT (Internet delle cose) a scenari di data science e analisi.

Altre informazioni sul connettore Spark per Azure Cosmos DB sono reperibili in questo video:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Componenti del connettore

Il connettore Spark per Azure Cosmos DB usa i componenti seguenti:

* [Azure Cosmos DB](http://documentdb.com) consente ai clienti di effettuare il provisioning e ridimensionare in modo elastico la velocità effettiva e le risorse di archiviazione in un numero qualsiasi di aree geografiche.  

* [Apache Spark](http://spark.apache.org/) è un potente motore di elaborazione open source incentrato su velocità, semplicità d'uso e analisi avanzata.  

* [Cluster Apache Spark in Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) per eseguire processi Spark nel cluster Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Connettere Apache Spark a Azure Cosmos DB

Per la connessione di Apache Spark e Azure Cosmos DB sono disponibili due approcci:

1. Uso di [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), un connettore Spark per Cosmos DB basato su Python, detto anche "pyDocumentDB".  

2. Uso di [Azure Cosmos DB SQL Java SD](https://github.com/Azure/azure-documentdb-java), un connettore Spark per Cosmos DB basato su Java.


**Versioni supportate**

| Componente | Version |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Versione del runtime di Databricks | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Connettersi usando Python o pyDocumentDB SDK

L'immagine seguente illustra l'architettura dell'implementazione di pyDocumentDB SDK:

![Flusso di dati da Spark ad Azure Cosmos DB tramite pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Flusso di dati

Il flusso di dati dell'implementazione di pyDocumentDB si presenta come specificato in seguito:

* Il nodo master Spark si connette al nodo del gateway Azure Cosmos DB tramite pyDocumentDB. Un utente specifica solo le connessioni di Spark e di Azure Cosmos DB. Le connessioni ai rispettivi nodi master e del gateway sono trasparenti per l'utente.  

* Il nodo del gateway esegue la query su Azure Cosmos DB, dove la query viene successivamente eseguita sulle partizioni della raccolta nei nodi dati. La risposta alle query viene inviata di nuovo al nodo del gateway e il set di risultati viene restituito al nodo master Spark.  

* Le query successive, ad esempio su un frame di dati Spark, vengono inviate ai nodi di lavoro Spark per essere elaborate.  

La comunicazione tra Spark e Azure Cosmos DB è limitata al nodo master Spark e ai nodi del gateway Azure Cosmos DB. Le query vengono eseguite alla velocità consentita dal livello di trasporto tra i due nodi.

Eseguire la procedura seguente per connettere Spark ad Azure Cosmos DB mediante pyDocumentDB SDK:

1. Creare un'[area di lavoro Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e un [cluster Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (versione del runtime Databricks 4.0 (include Apache Spark 2.3.0, Scala 2.11) all'interno di tale area di lavoro.  

2. Una volta che il cluster viene creato ed è in esecuzione, passare a **Area di lavoro** > **Crea** > **Libreria**.  
3. Dalla finestra di dialogo Nuova libreria, scegliere **Carica Python Egg o PyPi** come origine, specificare **pydocumentdb** come nome e selezionare **Installare libreria**. PyDocumentdb SDK è già pubblicato per i pacchetti pip in modo da essere rilevato e installato. 

   ![Creare e collegare librerie](./media/spark-connector/create-library.png)

4. Dopo aver installato la libreria, collegarla al cluster creato in precedenza.  

5. Passare quindi a **Area di lavoro** > **Crea** > **Notebook**.  

6. Nella finestra di dialogo **Crea notebook** , immettere un nome intuitivo e scegliere **Python** come lingua. Nell'elenco a discesa selezionare il cluster creato in precedenza e selezionare **Crea**.  

7. La semplicità del trasporto di comunicazione rende relativamente semplice l'esecuzione di una query da Spark ad Azure Cosmos DB con pyDocumentDB. Quindi verranno eseguite solo alcune query Spark tramite i dati di esempio sui voli ospitati nell'account Cosmos DB "doctorwho" che è accessibile pubblicamente. La versione HTML del notebook è ospitata nel repository GitHub [azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). È necessario scaricare i file del repository e passare a `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`; è possibile importare il notebook nell'account di Azure Databricks ed eseguirlo. La sezione seguente illustra le funzionalità dei blocchi di codice in dettaglio.

Il frammento di codice seguente illustra come importare pyDocumentDB SDK ed eseguire una query nel contesto di Spark. Come indicato nel frammento di codice, l'SDK di pyDocumentDB contiene i parametri di connessione necessari per connettersi all'account Azure Cosmos DB. Importa le librerie necessarie e configura la masterKey e l'host per creare il client Azure Cosmos DB (pydocumentdb.document_client).


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

Quindi è possibile eseguire le query; il frammento di codice seguente si connette alla raccolta airports.codes nell'account DoctorWho ed esegue una query per estrarre le città con aeroporto nello stato di Washington. 

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

Dopo l'esecuzione della query, il risultato è un oggetto "query_iterable.QueryIterable" che viene convertito in un elenco Python, che viene poi convertito in un frame di dati Spark. 

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

## <a name="considerations-when-using-pydocumentdb-sdk"></a>Considerazioni per l'uso di pyDocumentDB SDK
La connessione di Spark ad Azure Cosmos DB mediante pyDocumentDB SDK è una procedura consigliata per gli scenari seguenti:

* Si vuole usare Python.  

* Viene restituito un set di risultati relativamente piccolo da Azure Cosmos DB a Spark. Si noti che il set di dati sottostante in Azure Cosmos DB può essere piuttosto grande ed è in corso l'applicazione di filtri o l'esecuzione dei filtri dei predicati rispetto all'origine Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Connettersi usando Java SDK

L'immagine seguente mostra l'architettura dell'implementazione di Azure Cosmos DB SQL Java SDK e i dati vengono trasportati tra i nodi di lavoro di Spark:

![Flusso di dati nel connettore Spark per Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Flusso di dati

Il flusso di dati dell'implementazione di Java SDK si presenta come specificato in seguito:

* Il nodo master Spark si connette al nodo del gateway Azure Cosmos DB per ottenere la mappa delle partizioni. Un utente specifica solo le connessioni di Spark e Azure Cosmos DB. Le connessioni ai rispettivi nodi master e del gateway sono trasparenti per l'utente.  

* Queste informazioni vengono restituite al nodo master Spark. A questo punto sarà possibile analizzare la query per determinare le partizioni e le relative posizioni in Azure Cosmos DB a cui è necessario accedere.  

* Queste informazioni vengono trasmesse ai nodi di lavoro Spark.  

* I nodi di lavoro Spark si connettono direttamente alle partizioni Azure Cosmos DB per estrarre i dati e restituiscono i dati alle partizioni Spark nei nodi di lavoro Spark.  

La comunicazione tra Spark e Azure Cosmos DB è notevolmente più veloce perché lo spostamento dei dati avviene tra i nodi di lavoro Spark e i nodi dati (partizioni) Azure Cosmos DB. In questo documento, verranno inviati alcuni dati di Twitter di esempio all'account Azure Cosmos DB e verranno eseguite le query Spark usando tali dati. Per scrivere i dati di Twitter di esempio in Azure Cosmos DB, procedere come segue:

1. Creare un [account API SQL di Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) e [aggiungere una raccolta](create-sql-api-dotnet.md#add-a-collection) all'account.  

2. Scaricare l'esempio [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) da GitHub, che consente di scrivere i dati di Twitter di esempio in Azure Cosmos DB.  

3. Aprire un prompt dei comandi e installare i moduli Tweepy e pyDocumentdb eseguendo i comandi seguenti:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Estrarre il contenuto dell'esempio del feed di Twitter e aprire il file config.py. Aggiornare i valori masterKey, pdatabaseId, collectionId e preferredLocations.  

5. Passare a `http://apps.twitter.com/` e registrare il feed di script di Twitter come una nuova applicazione. Dopo aver selezionato un nome e un'applicazione per l'app, vengono forniti: **chiave utente, segreto del cliente, token di accesso e segreto del token di accesso**. Copiare questi valori e aggiornarli nel file config.py per fornire all'applicazione l'accesso a livello di codice a Twitter.   

6. Salvare il file config.py. Aprire il prompt dei comandi ed eseguire l'applicazione python usando il comando seguente:

   ```bash
   Python driver.py
   ```

7. Passare alla raccolta di Azure Cosmos DB nel portale e verificare che i dati di Twitter vengano scritti nella raccolta.

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Trovare e collegare Java SDK nel cluster Spark

1. Creare un'[area di lavoro Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) e un [cluster Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (versione del runtime Databricks 4.0 (include Apache Spark 2.3.0, Scala 2.11) all'interno di tale area di lavoro.  

2. Una volta che il cluster viene creato ed è in esecuzione, passare a **Area di lavoro** > **Crea** > **Libreria**.  

3. Dalla finestra di dialogo Nuova libreria, scegliere **Coordinate di Maven** come origine, specificare il valore della coordinata **com.microsoft.azure:azure-cosmosdb-Spark_2.3.0_2.11:1.2.0** e selezionare **Crea libreria**. Le dipendenze Maven vengono risolte e il pacchetto viene aggiunto all'area di lavoro. Nel precedente formato di coordinate Maven, 2.3.0 rappresenta la versione di Spark, 2.11 rappresenta la versione di scala e 1.2.0 rappresenta la versione del connettore Azure Cosmos DB. 

4. Dopo aver installato la libreria, collegarla al cluster creato in precedenza. 

Questo articolo illustra l'uso del connettore Spark per Java SDK nei seguenti scenari:

* Lettura dei dati di Twitter da Azure Cosmos DB  

* Lettura dei dati di Twitter che vengono trasmessi in streaming in Azure Cosmos DB  

* Scrittura dei dati di Twitter in Azure Cosmos DB 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Lettura dei dati di Twitter da Azure Cosmos DB
 
In questa sezione si eseguono query di Spark per leggere un batch di dati di Twitter da Azure Cosmos DB. La versione HTML del notebook è ospitata nel repository GitHub [azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). È necessario scaricare i file del repository e passare a `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` è possibile importare il notebook nell'account di Azure Databricks, aggiornare URI dell'account, chiave master, database, nomi delle raccolte ed eseguirlo oppure è possibile creare il notebook come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**. 

2. Nela finestra di dialogo **Crea notebook**, immettere un nome intuitivo, scegliere **Python** come lingua, nell'elenco a discesa selezionare il cluster creato in precedenza e selezionare **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi all'account e leggere i tweet con comando Spark.read.format().

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
|query_maxdegreeofparallelism  | Imposta il numero di operazioni simultanee eseguite sul lato client durante l'esecuzione di query in parallelo. Se è impostato su un valore maggiore di 0, limita il numero di operazioni simultanee per il valore assegnato. Se è impostato un valore minore di 0, il sistema decide automaticamente il numero di operazioni simultanee da eseguire. Mentre il connettore esegue il mapping di ogni partizione della raccolta con un executor, questo valore non avrà alcun effetto sull'operazione di lettura.        |
|query_maxbuffereditemcount     |    Imposta il numero massimo di elementi che possono essere memorizzati nel buffer sul lato client durante l'esecuzione di query parallele. Se è impostato su un valore maggiore di 0, limita il numero di elementi memorizzati nel buffer per il valore assegnato. Se è impostato un valore minore di 0, il sistema decide automaticamente il numero di elementi memorizzati nel buffer.     |
|query_enablescan    |   Imposta l'opzione per abilitare le analisi delle query che non possono essere gestite perché l'indicizzazione è stata rifiutata esplicitamente nei percorsi di richiesta.       |
|query_disableruperminuteusage  |  Disabilita la capacità di unità richiesta (UR) al minuto di gestire la query qualora siano state esaurite UR al secondo normali su cui è stato eseguito il provisioning.       |
|query_emitverbosetraces   |   Imposta l'opzione per consentire alle query di generare tracce dettagliate per l'analisi.      |
|query_pagesize  |   Imposta le dimensioni della pagina dei risultati della query per ogni richiesta di query. Per ottimizzare la velocità effettiva, usare una dimensione di pagina ampia per ridurre il numero di round trip al fine di recuperare i risultati di query.      |
|query_custom  |  Imposta la query di Azure Cosmos DB per eseguire l'override della query predefinita durante il recupero di dati da Azure Cosmos DB. Tenere presente che quando questo valore viene specificato, verrà usato al posto di una query con i predicati distribuiti.     |

A seconda dello scenario, è opportuno usare diversi valori di configurazione per ottimizzare le prestazioni e la velocità effettiva. Si noti che la chiave di configurazione attualmente non presta attenzione tra maiuscole e minuscole e il valore di configurazione è sempre una stringa.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Lettura dei dati di Twitter che vengono trasmessi in streaming in Azure Cosmos DB

In questa sezione si eseguono query di Spark per la lettura di un feed di modifiche dei dati di Twitter attualmente trasmessi in streaming. Durante l'esecuzione delle query in questa sezione, assicurarsi che l'app dei feed di Twitter sia in esecuzione e che stia distribuendo dati ad Azure Cosmos DB. La versione HTML del notebook è ospitata nel repository GitHub [azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). È necessario scaricare i file del repository e passare a `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`; è possibile importare il notebook nell'account di Azure Databricks, aggiornare URI dell'account, chiave master, database, nomi delle raccolte ed eseguirlo oppure è possibile creare il notebook come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**.  

2. Nela finestra di dialogo **Crea notebook**, immettere un nome intuitivo, scegliere **Scala** come lingua, nell'elenco a discesa selezionare il cluster creato in precedenza, quindi **Crea**.  

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
4. Iniziare la lettura dei feed di modifiche come flusso usando il comando Spark.readStream.format():

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Iniziare la trasmissione in streaming delle query alla console:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Java SDK supporta i seguenti valori per il mapping di configurazione:

|Impostazione  |DESCRIZIONE  |
|---------|---------|
|readchangefeed   |  Indica che il contenuto della raccolta viene recuperato dal feed delle modifiche di CosmosDB. Il valore predefinito è False.       |
|changefeedqueryname |   Stringa univoca che identifica la query. Il connettore tiene traccia separatamente dei token di continuazione della raccolta per diverse query dei feed delle modifiche. Se readchangefeed è true, si tratta di una configurazione necessaria che non può accettare un valore vuoto.      |
|changefeedcheckpointlocation  |   Un percorso all'archivio file locale per rendere persistenti i token di continuazione in caso di errori dei nodi.      |
|changefeedstartfromthebeginning  |  Imposta se il feed delle modifiche deve iniziare dall'inizio (true) o dal punto corrente (false). Per impostazione predefinita, inizia dalla posizione corrente (false).       |
|rollingchangefeed  |   Un valore booleano che indica se il feed di modifiche debba appartenere all'ultima query. Il valore predefinito è false, il che significa che le modifiche verranno conteggiate dalla prima lettura della raccolta.      |
|changefeedusenexttoken  |   Un valore booleano per supportare gli scenari di errore di elaborazione. Viene usato per indicare che il batch corrente di feed di modifiche è stato gestito correttamente e RDD deve usare i token di continuazione successivi per ottenere i successivi batch di modifiche.      |
| InferStreamSchema | Un valore booleano che indica se lo schema dei dati di streaming deve essere campionato all'inizio dello streaming. Per impostazione predefinita, questo valore è impostato su true. Se questo parametro è impostato su true e lo schema di dati di streaming cambia dopo che i dati vengono campionati, verranno eliminate le proprietà appena aggiunte nel frame di dati di streaming. <br/><br/> Se si desidera che il frame di dati di streaming sia indipendente dallo schema, impostare questo parametro su false. In questa modalità, viene eseguoti il wrapping del corpo dei documenti letti dal feed di modifiche di Azure Cosmos DB, ciò avviene in una proprietà "body" del frame di dati in streaming risultante a parte rispetto ai valori delle proprietà di sistema.
 |

### <a name="connection-settings"></a>Impostazioni di connessione

Java SDK supporta le seguenti impostazioni di connessione:

|Impostazione  |DESCRIZIONE  |
|---------|---------|
|connectionmode   |  Imposta la modalità di connessione che il client DocumentClient interno deve usare per comunicare con Azure Cosmos DB. I valori consentiti sono **DirectHttps** (valore predefinito) e **Gateway**. La modalità di connessione DirectHttps instrada le richieste direttamente alle partizioni CosmosDB e fornisce alcuni vantaggi di latenza.       |
|connectionmaxpoolsize   |  Imposta il valore della dimensione del pool di connessioni usato dal DocumentClient interno. Il valore predefinito è 100.       |
|connectionidletimeout  |  Imposta il valore di timeout per le connessioni inattive in secondi. Il valore predefinito è 60.       |
|query_maxretryattemptsonthrottledrequests    |  Imposta il numero massimo di tentativi. Questo valore viene usato in caso di errore di richiesta a causa della limitazione di velocità del client. Se non è specificato, il valore predefinito è pari a 1000 tentativi.       |
|query_maxretrywaittimeinseconds   |  Imposta il tempo massimo per i tentativi in secondi. Il valore predefinito è 1000 secondi.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Scrittura dei dati di Twitter in Azure Cosmos DB 

In questa sezione verranno eseguite le query di Spark per scrivere un batch di dati di Twitter in una nuova raccolta nello stesso database. La versione HTML del notebook è ospitata nel repository GitHub [azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). È necessario scaricare i file del repository e passare a `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`; è possibile importare il notebook nell'account di Azure Databricks, aggiornare URI dell'account, chiave master, database, nomi delle raccolte ed eseguirlo oppure è possibile creare il notebook come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**.  

2. Nela finestra di dialogo **Crea notebook**, immettere un nome intuitivo, scegliere **Scala** come lingua, nell'elenco a discesa selezionare il cluster creato in precedenza, quindi **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi alla raccolta del database per leggere e scrivere i dati di Twitter.

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

5. Creare nuovo frame di dati di tag di tweet e salvare i dati nella nuova raccolta. Dopo aver eseguito il codice seguente, è possibile tornare al portale e verificare che i dati siano scritti nella raccolta. 

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
|WritingBatchSize  |   Indica le dimensioni del batch da usare durante la scrittura dei dati nella raccolta Azure Cosmos DB. <br/><br/> Se il parametro BulkImport è impostato su true, il parametro WritingBatchSize indica la dimensione del batch di documenti forniti come input per l'API importAll della libreria di BulkExecutor. Per impostazione predefinita, questo valore è impostato su 100K. <br/><br/> Se il parametro BulkImport è impostato su false, il parametro WritingBatchSize indica le dimensioni del batch da usare durante la scrittura in una raccolta Azure Cosmos DB. Il connettore invia in modo asincrono le richieste createDocument/upsertDocument nel batch. Maggiori sono le dimensioni del batch e maggiore sarà la velocità effettiva che è possibile ottenere fino a quando le risorse del cluster rimangono disponibili. D'altra parte, specificare un numero minore di dimensioni del batch per limitare la frequenza e il consumo di UR. Per impostazione predefinita, le dimensioni del batch di scrittura sono impostate su 500.  |
|Upsert   |  Un valore booleano che indica se è necessario usare upsertDocument invece di CreateDocument durante la scrittura alla raccolta di CosmosDB.   |
| WriteThroughputBudget |  Una stringa di tipo integer che rappresenta il numero di UR che si desidera allocare per il processo Spark di inserimento in blocco rispetto alla velocità effettiva totale allocata alla raccolta. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Scrittura dei dati di Twitter che vengono trasmessi in streaming in Azure Cosmos DB 

In questa sezione verranno eseguite le query di Spark per scrivere un feed di modifiche di streaming in una nuova raccolta dello stesso database. La versione HTML del notebook è ospitata nel repository GitHub [azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). È necessario scaricare i file del repository e passare a `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`; è possibile importare il notebook nell'account di Azure Databricks, aggiornare URI dell'account, chiave master, database, nomi delle raccolte ed eseguirlo oppure è possibile creare il notebook come indicato di seguito:

1. Passare all'account di Azure Databricks e selezionare **Area di lavoro** > **Crea** > **Notebook**.  

2. Nela finestra di dialogo **Crea notebook**, immettere un nome intuitivo, scegliere **Scala** come lingua, nell'elenco a discesa selezionare il cluster creato in precedenza, quindi **Crea**.  

3. Aggiornare endpoint, chiave master, database e valori della raccolta per connettersi alla raccolta del database per leggere e scrivere i dati di Twitter.

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
4. Iniziare la lettura dei feed di modifiche come flusso usando il comando Spark.readStream.format():
 
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
|Upsert   |  Un valore booleano che indica se è necessario usare upsertDocument invece di CreateDocument durante la scrittura alla raccolta di CosmosDB.   |
|checkpointlocation  |   Un percorso all'archivio file locale per rendere persistenti i token di continuazione in caso di errori dei nodi.   |
|WritingBatchSize  |  Indica le dimensioni del batch da usare durante la scrittura dei dati nella raccolta Azure Cosmos DB. Il connettore invia in modo asincrono le richieste createDocument/upsertDocument nel batch. Maggiori sono le dimensioni del batch e maggiore sarà la velocità effettiva che è possibile ottenere fino a quando le risorse del cluster rimangono disponibili. D'altra parte, specificare un numero minore di dimensioni del batch per limitare la frequenza e il consumo di UR. Per impostazione predefinita, le dimensioni del batch di scrittura sono impostate su 500.  |


## <a name="considerations-when-using-java-sdk"></a>Considerazioni per l'uso di Java SDK

Connettere Spark ad Azure Cosmos DB mediante Java SDK è una procedura consigliata per gli scenari seguenti:

* Si vuole usare Python e/o Scala.  

* Si dispone di una grande quantità di dati da trasferire tra Apache Spark e Azure Cosmos DB, Java SDK offre prestazioni più elevate rispetto a pyDocumentDB. Per informazioni sulle differenze a livello di prestazioni delle query, vedere la [wiki sulle esecuzioni di test di query](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Passaggi successivi

Se ancora non lo si è fatto, scaricare il connettore Spark per Azure Cosmos DB dal repository GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) ed esplorare le risorse aggiuntive nel repository:

* [Esempi di aggregazioni distribuite](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Notebook e script di esempio](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

È possibile anche esaminare la [guida ad Apache Spark SQL, DataFrame e set di dati](http://spark.apache.org/docs/latest/sql-programming-guide.html) e l'articolo su [Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
