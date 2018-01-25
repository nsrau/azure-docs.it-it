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
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 5f66e60249af489e695029cbb072f3cc881bb039
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2018
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>Usare la vista Hive di Ambari con Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come eseguire query Hive usando la vista Hive di Ambari. Ambari è un'utilità per la gestione e il monitoraggio fornita con i cluster HDInsight basati su Linux. Una delle funzionalità fornite da Ambari è un'interfaccia utente Web che può essere usata per eseguire query Hive.

> [!NOTE]
> Ambari include numerose funzionalità non illustrate in questo documento. Per altre informazioni, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Ambari](../hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>prerequisiti

* Un cluster HDInsight basato su Linux. Per informazioni su come creare un cluster, vedere [Introduzione all'uso di Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight di Azure che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="open-the-hive-view"></a>Aprire la visualizzazione Hive

È possibile aprire una vista di Ambari dal portale di Azure. Selezionare il cluster HDInsight, quindi **Viste di Ambari** nella sezione **Collegamenti rapidi**.

![Sezione Collegamenti rapidi del portale](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

Nell'elenco di viste selezionare __vista Hive__.

![Vista Hive selezionata](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

> [!NOTE]
> Quando si accede ad Ambari, viene richiesto di eseguire l'autenticazione al sito. Immettere il nome dell'account amministratore (il valore predefinito è `admin`) e la password usati durante la creazione del cluster.

Verrà visualizzata una pagina simile all'immagine seguente:

![Immagine del foglio di lavoro della query per la vista Hive](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="run-a-query"></a>Eseguire una query

Per eseguire una query Hive, seguire questa procedura dalla vista di Hive.

1. Dalla scheda __Query__ incollare le istruzioni HiveQL seguenti nel foglio di lavoro:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Le istruzioni eseguono queste azioni:

   * `DROP TABLE`: elimina la tabella e il file di dati, qualora la tabella esista già.

   * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive.
   Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

   * `ROW FORMAT`: indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

   * `STORED AS TEXTFILE LOCATION`: indica dove sono archiviati i dati e che sono archiviati come testo.

   * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna t4 include il valore [ERROR].

     > [!NOTE]
     > Usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce. L'eliminazione di una tabella esterna *non* comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

    > [!IMPORTANT]
    > Mantenere la selezione di __Database__ __predefinita__. Gli esempi di questo documento usano il database predefinito incluso in HDInsight.

2. Per avviare la query, usare il pulsante **Execute** (Esegui) sotto il foglio di lavoro. Il pulsante diventa arancione e il testo cambia in **Interrompi**.

3. Al termine dell'elaborazione della query, nella scheda **Risultati** vengono visualizzati i risultati dell'operazione. Il testo seguente è il risultato della query:

        sev       cnt
        [ERROR]   3

    La scheda **Logs** può essere usata per visualizzare le informazioni sulla registrazione create dal processo.

   > [!TIP]
   > Per scaricare o salvare i risultati, usare la finestra di dialogo **Salva risultati** nella parte superiore sinistra della sezione **Query Process Results** (Risultati del processo di query).

4. Selezionare le prime quattro righe di questa query, quindi selezionare **Esegui**. Si noti che, al termine del processo, non viene visualizzato alcun risultato. Se si usa il pulsante **Execute** (Esegui) quando parte della query è selezionata, verranno eseguite solo le istruzioni selezionate. In questo caso, la selezione non include l'istruzione finale che recupera le righe dalla tabella. Se si seleziona solo tale riga e si usa **Execute** (Esegui), verranno visualizzati i risultati previsti.

5. Per aggiungere un foglio di lavoro, usare il pulsate **New Worksheet** (Nuovo foglio di lavoro) nella parte inferiore di **Query Editor** (Editor query). Nel nuovo foglio di lavoro immettere le istruzioni HiveQL seguenti:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Le istruzioni eseguono queste azioni:

   * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, viene creata una tabella interna. Una tabella interna verrà archiviata nel data warehouse di Hive e gestita completamente da Hive. A differenza delle tabelle esterne, se si elimina una tabella interna vengono eliminati anche i dati sottostanti.

   * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

   * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti `[ERROR]` e quindi inserisce i dati nella tabella **errorLogs**.

Usare il pulsante **Execute** (Esegui) per eseguire la query. La scheda **Results** (Risultati) non contiene informazioni quando la query restituisce zero righe. Lo stato visualizzato deve essere **SUCCEEDED** dopo il completamento della query.

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

## <a name="user-defined-functions"></a>Funzioni definite dall'utente

Hive può anche essere esteso tramite funzioni definite dall'utente (UDF), che consentono di implementare funzionalità o logiche non facilmente modellate in HiveQL.

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
