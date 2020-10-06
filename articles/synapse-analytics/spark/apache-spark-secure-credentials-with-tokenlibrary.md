---
title: Protezione delle credenziali di accesso con servizi collegati in Apache Spark per Azure Synapse Analytics
description: Questo articolo illustra i concetti relativi all'integrazione sicura di Apache Spark per Synapse Analytics con altri servizi usando servizi collegati e la libreria di token
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 90e7297236994650e0820e883c94a98b29c49fb7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249417"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Protezione delle credenziali tramite servizi collegati con TokenLibrary
L'accesso ai dati da origini esterne è uno schema comune. A meno che l'origine dati esterna non consenta l'accesso anonimo, è probabile che sia necessario proteggere la connessione con una credenziale, un segreto o una stringa di connessione.  

Azure Synapse Analytics fornisce servizi collegati per semplificare il processo di integrazione archiviando i dettagli della connessione in un servizio collegato o in Azure Key Vault. Dopo aver creato un servizio collegato, Apache Spark può farvi riferimento per applicare le informazioni di connessione nel codice. 

Per altre informazioni, vedere [Servizi collegati](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
> [!NOTE]
> L'accesso ai file da Azure Data Lake Storage nell'area di lavoro usa il passthrough di AAD per l'autenticazione, per cui non è necessario usare TokenLibrary. 


## <a name="prerequisite"></a>Prerequisito
* Servizio collegato: è necessario creare un servizio collegato all'origine dati esterna e farvi riferimento da TokenLibrary. Per altre informazioni, vedere [Servizi collegati](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Connettersi ad ADLS Gen2 all'esterno dell'area di lavoro di Synapse

Synapse offre un'esperienza integrata di servizi collegati per Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>Usare la libreria di token

Per connettersi ad altri servizi, è possibile effettuare una chiamata diretta a TokenLibrary.

### <a name="getconnectionstring"></a>GetConnectionString
 Per recuperare la stringa di connessione, usare la funzione <b>getConnectionString</b> e passare il <b>nome del servizio collegato</b>.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Per analizzare specifici valori di una coppia <i>chiave/valore</i> nella stringa di connessione, ad esempio 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>,

usare la funzione <b>getConnectionStringAsMap</b> e passare la chiave per restituire il valore.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Passaggi successivi

- [Scrivere in un pool SQL](./synapse-spark-sql-pool-import-export.md)

