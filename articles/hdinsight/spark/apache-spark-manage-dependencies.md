---
title: Gestire le dipendenze delle applicazioni Spark in Azure HDInsight
description: Questo articolo fornisce un'introduzione a come gestire le dipendenze Spark nel cluster HDInsight Spark per le applicazioni PySpark e scala.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: dafb4485ae9b10d89fa36bd790dcf3a799054de3
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90064137"
---
# <a name="manage-spark-application-dependencies"></a>Gestire le dipendenze dell'applicazione Spark

Questo articolo illustra come gestire le dipendenze per le applicazioni Spark in esecuzione in HDInsight. Vengono descritti sia scala che PySpark nell'ambito del cluster e dell'applicazione Spark.

Usare i collegamenti rapidi per passare alla sezione in base al caso dell'utente:
* [Configurare le dipendenze jar del processo Spark usando Jupyter notebook](#use-jupyter-notebook)
* [Configurare le dipendenze jar del processo Spark con use Azure Toolkit for IntelliJ](#use-azure-toolkit-for-intellij)
* [Configurare le dipendenze jar per il cluster Spark](#jar-libs-for-cluster)
* [Gestisci in modo sicuro le dipendenze jar](#safely-manage-jar-dependencies)
* [Configurare i pacchetti Python del processo Spark usando Jupyter notebook](#use-jupyter-notebook-1)
* [Gestire in modo sicuro i pacchetti Python per il cluster Spark](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Librerie jar per un processo Spark
### <a name="use-jupyter-notebook"></a>Usare Jupyter notebook
Quando una sessione Spark viene avviata in Jupyter Notebook nel kernel Spark per scala, è possibile configurare i pacchetti da:

* [Repository maven](https://search.maven.org/)o pacchetti creati dalla community in [pacchetti Spark](https://spark-packages.org/).
* File jar archiviati nell'archiviazione primaria del cluster.

Si userà `%%configure` Magic per configurare il notebook per l'uso di un pacchetto esterno. Nei notebook che usano pacchetti esterni assicurarsi di richiamare `%%configure` magic nella prima cella del codice. Questo accorgimento garantisce che il kernel sia configurato per l'uso del pacchetto prima dell'avvio della sessione.

>
>[!IMPORTANT]  
>Se si dimentica di configurare il kernel nella prima cella, è possibile usare `%%configure` magic con il parametro `-f`. In questo modo, tuttavia, la sessione verrà riavviata e le operazioni eseguite andranno perse.

**Esempio per i pacchetti del repository maven o dei pacchetti Spark**

Dopo aver individuato il pacchetto dal repository maven, raccogliere i valori per **GroupID**, **ArtifactId**e **Version**. Concatenare i tre valori, separati da due punti (**:**).

   ![Concatenare lo schema del pacchetto](./media/apache-spark-manage-dependencies/spark-package-schema.png "Concatenare lo schema del pacchetto")

Assicurarsi che i valori raccolti corrispondano al cluster. In questo caso, viene usato il pacchetto del connettore Spark Cosmos DB per scala 2,11 e Spark 2,3 per il cluster Spark HDInsight 3,6. Se non si è certi, eseguire `scala.util.Properties.versionString` nella cella di codice nel kernel Spark per ottenere la versione di scala del cluster. Eseguire `sc.version` per ottenere la versione di Spark del cluster.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Esempio per i file jar archiviati nell'archiviazione primaria**

Usare lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per i file jar nell'archiviazione primaria del cluster. Corrisponde a `wasb://` per Archiviazione di Azure, a `abfs://` per Azure Data Lake Storage Gen2 e a `adl://` per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per archiviazione di Azure o Data Lake Storage Gen2, l'URI è `wasbs://` o `abfss://` . Vedere [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

Usare un elenco delimitato da virgole di percorsi jar per più file jar. i glob sono consentiti. I file jar sono inclusi nei classpath di driver ed executor.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Dopo aver configurato i pacchetti esterni, è possibile eseguire Import nella cella del codice per verificare se i pacchetti sono stati posizionati correttamente.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>USA Azure Toolkit for IntelliJ
[Azure Toolkit for IntelliJ plug-in](./apache-spark-intellij-tool-plugin.md) fornisce l'esperienza dell'interfaccia utente per inviare un'applicazione Spark scala a un cluster HDInsight. Fornisce `Referenced Jars` `Referenced Files` le proprietà e per configurare i percorsi delle librerie jar quando si invia l'applicazione Spark. Per altre informazioni su [come usare Azure Toolkit for IntelliJ plug-in per HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster), vedere.

![Finestra di dialogo Spark Submission (Invio Spark)](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>Librerie jar per cluster
In alcuni casi, può essere necessario configurare le dipendenze jar a livello di cluster in modo che ogni applicazione possa essere configurata con le stesse dipendenze per impostazione predefinita. L'approccio consiste nell'aggiungere i percorsi jar al driver Spark e al percorso della classe Executor.

1. Eseguire le azioni script di esempio seguenti per copiare i file con estensione jar dall'archiviazione primaria `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` a file system locali del cluster `/usr/libs/sparklibs` . Il passaggio è necessario in quanto Linux usa `:` per separare l'elenco di percorsi della classe, ma HDInsight supporta solo i percorsi di archiviazione con schema come `wasb://` . Il percorso di archiviazione remoto non funzionerà correttamente se lo si aggiunge direttamente al percorso della classe.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Modificare la configurazione del servizio Spark da Ambari per aggiornare il percorso della classe. Passare a **Ambari > Spark > configs > Custom Spark2-defaults**. **Aggiungere la proprietà** come indicato di seguito. Usare `:` per separare i percorsi se si dispone di più di un percorso da aggiungere. I glob sono consentiti.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Modificare la configurazione predefinita di Spark](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Modificare la configurazione predefinita di Spark")

3. Salvare le configurazioni modificate e riavviare i servizi interessati.

   ![Riavviare i servizi interessati](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Riavviare i servizi interessati")

È possibile automatizzare i passaggi usando le [azioni script](../hdinsight-hadoop-customize-cluster-linux.md). L'azione script per l' [aggiunta di librerie personalizzate hive](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) è un riferimento valido. Quando si modificano le configurazioni dei servizi Spark, assicurarsi di usare le API Ambari anziché modificare direttamente i file di configurazione. 

## <a name="safely-manage-jar-dependencies"></a>Gestisci in modo sicuro le dipendenze jar
Il cluster HDInsight include dipendenze jar predefinite e gli aggiornamenti per queste versioni jar si verificano di tanto in tanto. Per evitare conflitti di versione tra i file jar incorporati e i file jar di riferimento, provare a [ombreggiare le dipendenze dell'applicazione](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Pacchetti Python per un processo Spark
### <a name="use-jupyter-notebook"></a>Usare Jupyter notebook
HDInsight Jupyter notebook PySpark kernel non supporta l'installazione di pacchetti Python direttamente da PyPi o dal repository di pacchetti Anaconda. Se si dispone `.zip` di `.egg` `.py` dipendenze, o e si desidera farvi riferimento per una sessione di Spark, attenersi alla procedura seguente:

1. Eseguire le azioni script di esempio seguenti per copiare `.zip` `.egg` o `.py` file dall'archiviazione primaria `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` a file system locali del cluster `/usr/libs/pylibs` . Il passaggio è necessario quando Linux usa `:` per separare l'elenco dei percorsi di ricerca, ma HDInsight supporta solo i percorsi di archiviazione con schema come `wasb://` . Il percorso di archiviazione remoto non funzionerà correttamente quando si usa `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Nel notebook eseguire il codice seguente in una cella di codice con il kernel PySpark:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Eseguire `import` per verificare se i pacchetti sono stati inclusi correttamente.  

## <a name="python-packages-for-cluster"></a>Pacchetti Python per cluster
È possibile installare pacchetti Python da Anaconda al cluster usando il comando conda tramite azioni script. I pacchetti installati si trovano a livello di cluster e si applicano a tutte le applicazioni. 

Il cluster HDInsight Spark include due installazioni di Python predefinite, Anaconda Python 2,7 e Anaconda Python 3,5. Per ulteriori informazioni sulle impostazioni predefinite di Python per i servizi e su come installare in modo sicuro i pacchetti Python esterni senza suddividere il cluster, vedere la pagina relativa alla [gestione sicura delle dipendenze Python per il cluster](./apache-spark-python-package-installation.md).
