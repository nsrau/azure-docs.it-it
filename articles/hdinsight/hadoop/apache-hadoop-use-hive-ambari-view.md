---
title: Usare le visualizzazioni di Ambari per l'uso di Hive in HDInsight (Hadoop) - Azure | Documentazione Microsoft
description: Informazioni su come usare la visualizzazione Hive dal Web browser per inviare query Hive. La visualizzazione Hive fa parte delle visualizzazioni di Ambari fornite con il cluster HDInsight basato su Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2018
ms.author: larryfr
ms.openlocfilehash: af5fe44b611e8ff9d93aba8a30c71213c452aff9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Usare la vista Hive di Ambari con Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come eseguire query Hive usando la vista Hive di Ambari. Le viste di Hive consentono di creare, ottimizzare ed eseguire query Hive dal Web browser.

## <a name="prerequisites"></a>prerequisiti

* Un cluster Hadoop basato su Linux in HDInsight versione 3.4 o successiva.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un Web browser

## <a name="run-a-hive-query"></a>Eseguire una query Hive

1. Aprire il [portale di Azure](https://portal.azure.com).

2. Selezionare il cluster HDInsight, quindi **Viste di Ambari** nella sezione **Collegamenti rapidi**.

    ![Sezione Collegamenti rapidi del portale](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    Quando viene richiesta l'autenticazione, usare il nome e la password dell'account di accesso al cluster (per impostazione predefinita, `admin`) specificati durante la creazione del cluster.

3. Nell'elenco di viste selezionare __vista Hive__.

    ![Vista Hive selezionata](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    La pagina Vista Hive è simile all'immagine seguente:

    ![Immagine del foglio di lavoro della query per la vista Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Dalla scheda __Query__ incollare le istruzioni HiveQL seguenti nel foglio di lavoro:

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

    Le istruzioni eseguono queste azioni:

   * `DROP TABLE`: elimina la tabella e il file di dati, qualora la tabella esista già.

   * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive.
   Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

   * `ROW FORMAT`: indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

   * `STORED AS TEXTFILE LOCATION`: indica dove sono archiviati i dati e che sono archiviati come testo.

   * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna t4 include il valore [ERROR].

    > [!IMPORTANT]
    > Mantenere la selezione di __Database__ __predefinita__. Gli esempi di questo documento usano il database predefinito incluso in HDInsight.

5. Per avviare la query, usare il pulsante **Execute** (Esegui) sotto il foglio di lavoro. Il pulsante diventa arancione e il testo cambia in **Interrompi**.

6. Al termine dell'elaborazione della query, nella scheda **Risultati** vengono visualizzati i risultati dell'operazione. Il testo seguente è il risultato della query:

        loglevel       count
        [ERROR]        3

    La scheda **Logs** può essere usata per visualizzare le informazioni sulla registrazione create dal processo.

   > [!TIP]
   > Per scaricare o salvare i risultati, usare la finestra di dialogo **Salva risultati** nella parte superiore sinistra della sezione **Query Process Results** (Risultati del processo di query).

### <a name="visual-explain"></a>Visual Explain

Per aprire una visualizzazione del piano di query, selezionare la scheda **Visual Explain** (Spiegazione visiva) sotto il foglio di lavoro.

La vista **Visual Explain** (Spiegazione visiva) della query può essere utile per conoscere il flusso delle query complesse. Per un equivalente testuale di questa visualizzazione, usare il pulsante **Spiega** in Query Editor.

### <a name="tez-ui"></a>Interfaccia utente di Tez

Per visualizzare l'interfaccia utente di Tez per la query, selezionare la scheda **Tez** sotto il foglio di lavoro.

> [!IMPORTANT]
> Tez non viene usato per risolvere tutte le query. Molte query possono essere risolte senza usare Tez. 

Se per risolvere la query è stato usato Tez, viene visualizzato il grafo aciclico diretto. Se si vuole visualizzare il DAG per le query eseguite in passato o eseguire il debug del processo Tez, usare invece [Tez View](../hdinsight-debug-ambari-tez-view.md).

## <a name="view-job-history"></a>Visualizzare la cronologia processo

La scheda __Jobs__ (Processi) visualizza una cronologia delle query Hive.

![Immagine della cronologia processo](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Tabelle di database

È possibile usare la scheda __Tables__ (Tabelle) per utilizzare le tabelle in un database Hive.

![Immagine della scheda delle tabelle](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Query salvate

Dalla scheda **Query** è facoltativamente possibile salvare le query. Dopo aver salvato una query, è possibile riusarla dalla scheda __Query salvate__.

![Immagine della scheda delle query salvate](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]
> Le query salvate vengono archiviate nell'archiviazione cluster predefinita. Le query salvate sono disponibili nel percorso `/user/<username>/hive/scripts`. Vengono archiviate come file `.hql` in testo normale.
>
> Se si elimina il cluster, ma si conserva l'archiviazione, è possibile usare un'utilità come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) o Data Lake Storage Explorer dal [portale di Azure](https://portal.azure.com) per recuperare le query.

## <a name="user-defined-functions"></a>Funzioni definite dall'utente

Hive può essere esteso tramite funzioni definite dall'utente (UDF), che consentono di implementare funzionalità o logiche non facilmente modellate in HiveQL.

La scheda della **funzione definita dall'utente** nella parte superiore della vista Hive consente di dichiarare e salvare un set di funzioni definite dall'utente, che è possibile usare con **Query Editor**.

![Immagine della scheda delle funzioni definite dall'utente](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Dopo avere aggiunto una funzione definita dall'utente alla visualizzazione Hive, verrà visualizzato un pulsante **Insert udfs** (Inserisci funzioni definite dall'utente) nella parte inferiore di **Query Editor**. Se si seleziona questa voce, verrà visualizzato un elenco di riepilogo a discesa di funzioni definite dall'utente nella vista Hive. La selezione di una funzione definita dall'utente aggiungerà istruzioni HiveQL alla query per abilitare la funzione definita dall'utente.

Ad esempio, se è stata specificata una funzione definita dall'utente con le proprietà seguenti:

* Nome della risorsa: myudfs

* Percorso della risorsa: /myudfs.jar

* Nome della funzione definita dall'utente: myawesomeudf

* Nome della classe per la funzione definita dall'utente: com.myudfs.Awesome

Se si usa il pulsante **Insert udfs** (Inserisci funzioni definite dall'utente), verrà visualizzata una voce denominata **myudfs**, con un altro elenco a discesa per ogni funzione definita dall'utente specificata per tale risorsa. In questo caso è **myawesomeudf**. Se si seleziona questa voce, verrà aggiunto quanto segue all'inizio della query:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Si potrà quindi usare la funzione definita dall'utente nella query, Ad esempio, `SELECT myawesomeudf(name) FROM people;`.

Per altre informazioni sull'uso di funzioni definite dall'utente con Hive in HDInsight, vedere gli articoli seguenti:

* [Usare Python con Hive e Pig in HDInsight](python-udf-hdinsight.md)
* [Come aggiungere una funzione definita dall'utente Hive personalizzata in HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Settings di Hive

È possibile modificare diverse impostazioni di Hive, ad esempio il motore di esecuzione per Hive da Tez (impostazione predefinita), in MapReduce.

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)
