---
title: Usare la visualizzazione Hive Apache Ambari con Apache Hadoop in Azure HDInsightUse Apache Ambari Hive View with Apache Hadoop in Azure HDInsight
description: Informazioni su come usare la visualizzazione Hive dal Web browser per inviare query Hive. La visualizzazione Hive fa parte delle visualizzazioni di Ambari fornite con il cluster HDInsight basato su Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/06/2020
ms.openlocfilehash: 787d88d336abcf3b0ba9b14c3d3798850b665eca
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745102"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Usare la vista Hive di Apache Ambari con Apache Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come eseguire query Hive usando la vista Hive di Apache Ambari. Le viste di Hive consentono di creare, ottimizzare ed eseguire query Hive dal Web browser.

## <a name="prerequisites"></a>Prerequisiti

Un cluster Hadoop in HDInsight.A Hadoop cluster on HDInsight. Vedere [Introduzione a HDInsight su Linux](./apache-hadoop-linux-tutorial-get-started.md).

## <a name="run-a-hive-query"></a>Eseguire una query Hive

1. Dal [portale di Azure](https://portal.azure.com/)selezionare il cluster.  Per istruzioni, vedere [Elencare e mostrare i cluster.](../hdinsight-administer-use-portal-linux.md#showClusters) Il cluster viene aperto in una nuova vista del portale.

1. In **Dashboard cluster**selezionare **Visualizzazioni Ambari**. Quando viene richiesta l'autenticazione, usare il nome e la password dell'account di accesso al cluster (per impostazione predefinita, `admin`) specificati durante la creazione del cluster. È anche possibile `https://CLUSTERNAME.azurehdinsight.net/#/main/views` passare a `CLUSTERNAME` nel browser, dove è il nome del cluster.

1. Nell'elenco di viste selezionare __vista Hive__.

    ![Apache Ambari selezionare vista Apache Hive](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    La pagina Vista Hive è simile all'immagine seguente:

    ![Immagine del foglio di lavoro della query per la vista Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

1. Dalla scheda __Query__ incollare le istruzioni HiveQL seguenti nel foglio di lavoro:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]'
        GROUP BY t4;
    ```

    Queste istruzioni eseguire le azioni seguenti:These statements do the following actions:

    |. | Descrizione |
    |---|---|
    |DROP TABLE|elimina la tabella e il file di dati, qualora la tabella esista già.|
    |CREA TABELLA ESTERNA|crea una nuova tabella "esterna" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.|
    |FORMATO RIGA|indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.|
    |MEMORIZZATO COME PERCORSO FILE DI TESTO|indica dove sono archiviati i dati e che sono archiviati come testo.|
    |SELECT|seleziona un conteggio di tutte le righe in cui la colonna t4 contiene il valore [ERROR].|

   > [!IMPORTANT]  
   > Mantenere la selezione di __Database____predefinita__. Gli esempi di questo documento usano il database predefinito incluso in HDInsight.

1. Per avviare la query, selezionare **Esegui** sotto il foglio di lavoro. Il pulsante diventa arancione e il testo cambia in **Interrompi**.

1. Al termine dell'elaborazione della query, nella scheda **Risultati** vengono visualizzati i risultati dell'operazione. Il testo seguente è il risultato della query:

        loglevel       count
        [ERROR]        3

    È possibile utilizzare la scheda **LOG** per visualizzare le informazioni di registrazione create dal processo.

   > [!TIP]  
   > Scaricare o salvare i risultati dalla finestra di dialogo a discesa **Azioni** nella scheda **Risultati.**

### <a name="visual-explain"></a>Visual Explain

Per aprire una visualizzazione del piano di query, selezionare la scheda **Visual Explain** (Spiegazione visiva) sotto il foglio di lavoro.

La vista **Visual Explain** (Spiegazione visiva) della query può essere utile per conoscere il flusso delle query complesse.

### <a name="tez-ui"></a>Interfaccia utente di Tez

Per visualizzare l'interfaccia utente di Tez per la query, selezionare la scheda **Tez UI** sotto il foglio di lavoro.

> [!IMPORTANT]  
> Tez non viene usato per risolvere tutte le query. Molte query possono essere risolte senza usare Tez.

## <a name="view-job-history"></a>Visualizzazione cronologia processo

La scheda __Jobs__ (Processi) visualizza una cronologia delle query Hive.

![Apache Hive visualizza la cronologia delle schede dei processi](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Tabelle di database

È possibile usare la scheda __Tables__ (Tabelle) per utilizzare le tabelle in un database Hive.

![Immagine della scheda Tabelle Apache Hive](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Query salvate

Dalla scheda **Query** è facoltativamente possibile salvare le query. Dopo aver salvato una query, è possibile riusarla dalla scheda __Query salvate__.

![Scheda Query salvate delle viste Apache Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Le query salvate vengono archiviate nell'archiviazione cluster predefinita. Le query salvate sono disponibili nel percorso `/user/<username>/hive/scripts`. Vengono archiviate come file `.hql` in testo normale.
>
> Se si elimina il cluster, ma si conserva l'archiviazione, è possibile usare un'utilità come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) o Data Lake Storage Explorer dal [portale di Azure](https://portal.azure.com) per recuperare le query.

## <a name="user-defined-functions"></a>Funzioni definite dall'utente

Hive può essere esteso tramite funzioni definite dall'utente (UDF), che consentono di implementare funzionalità o logiche non facilmente modellate in HiveQL.

La scheda della **funzione definita dall'utente** nella parte superiore della vista Hive consente di dichiarare e salvare un set di funzioni definite dall'utente, che è possibile usare con **Query Editor**.

![Apache Hive visualizza la visualizzazione della scheda UDFs](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Nella parte inferiore **dell'Editor di query**viene visualizzato il pulsante Inserisci funzioni definite **dall'utente** . In questa voce viene visualizzato un elenco a discesa delle funzioni definite dall'utente definite nella visualizzazione Hive. La selezione di una funzione definita dall'utente aggiungerà istruzioni HiveQL alla query per abilitare la funzione definita dall'utente.

Ad esempio, se è stata definita una fDU con le seguenti proprietà:

* Nome della risorsa: myudfs

* Percorso della risorsa: /myudfs.jar

* Nome della funzione definita dall'utente: myawesomeudf

* Nome della classe per la funzione definita dall'utente: com.myudfs.Awesome

Se si usa il pulsante **Insert udfs** (Inserisci funzioni definite dall'utente), verrà visualizzata una voce denominata **myudfs**, con un altro elenco a discesa per ogni funzione definita dall'utente specificata per tale risorsa. In questo caso, **myawesomeudf**. Se si seleziona questa voce, verrà aggiunto quanto segue all'inizio della query:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Si potrà quindi usare la funzione definita dall'utente nella query, Ad esempio: `SELECT myawesomeudf(name) FROM people;`.

Per altre informazioni sull'uso di funzioni definite dall'utente con Hive in HDInsight, vedere gli articoli seguenti:

* [Usare le funzioni definite dall'utente di Python con Apache Hive e Apache Pig in HDInsight](python-udf-hdinsight.md)
* [Usare una funzione definita dall'utente Java con Apache Hive in HDInsight](./apache-hadoop-hive-java-udf.md)

## <a name="hive-settings"></a>Settings di Hive

È possibile modificare diverse impostazioni di Hive, ad esempio il motore di esecuzione per Hive da Tez (impostazione predefinita), in MapReduce.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Apache Pig con Apache Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
