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
ms.date: 05/05/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: 9adfd92b72eb0ecd5b4811c525d4b6ff6609f60f
ms.contentlocale: it-it
ms.lasthandoff: 07/08/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Usare la visualizzazione Hive con Hadoop in HDInsight

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Informazioni su come eseguire query Hive usando la vista Hive di Ambari. Ambari è un'utilità per la gestione e il monitoraggio fornita con i cluster HDInsight basati su Linux. Una delle funzionalità fornite da Ambari è un'interfaccia utente Web che può essere usata per eseguire query Hive.

> [!NOTE]
> Ambari include numerose funzionalità non illustrate in questo documento. Per altre informazioni, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster HDInsight basato su Linux. Per informazioni sulla creazione di un cluster, vedere [Introduzione all'uso di Hadoop con Hive in HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="open-the-hive-view"></a>Aprire la visualizzazione Hive

Per accedere alle visualizzazioni di Ambari dal portale di Azure, selezionare il cluster HDInsight e quindi **Visualizzazioni di Ambari** nella sezione **Collegamenti rapidi**.

![Sezione Collegamenti rapidi](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

È anche possibile passare direttamente ad Ambari accedendo a https://CLUSTERNAME.azurehdinsight.net in un Web browser. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight. Per elencare le viste disponibili, selezionare il set di quadratini nel menu. Per aprire la vista, selezionare la voce **vista Hive**.

![Selezione delle visualizzazioni di Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/select-hive-view.png).

> [!NOTE]
> Quando si accede ad Ambari, viene richiesto di eseguire l'autenticazione al sito. Immettere il nome dell'account amministratore (il valore predefinito è `admin`) e la password usati durante la creazione del cluster.

Verrà visualizzata una pagina simile all'immagine seguente:

![Immagine della pagina della visualizzazione Hive, contenente una sezione Query Editor](./media/hdinsight-hadoop-use-hive-ambari-view/ambari-hive-view.png)

## <a name="view-tables"></a>Visualizzare tabelle

Nella sezione **Database Explorer** (Esplora database) della pagina selezionare la voce **default** nella scheda **Databases**. Viene visualizzato un elenco di tabelle nel database predefinito. HDInsight include una tabella denominata **hivesampletable**.

![Database Explorer con il database predefinito espanso](./media/hdinsight-hadoop-use-hive-ambari-view/database-explorer.png)

Poiché vengono aggiunte tabelle tramite la procedura descritta in questo documento, è possibile usare l'icona di aggiornamento nell'angolo superiore destro di Database Explorer per aggiornare l'elenco.

## <a name="hivequery"></a>Query editor

Per eseguire una query Hive, seguire questa procedura dalla vista di Hive.

1. Nella sezione **Query Editor** (Editor di query) della pagina incollare l'istruzione HiveQL seguente nel foglio di lavoro:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Di seguito sono elencate le istruzioni che eseguono queste azioni:

   * `DROP TABLE`: elimina la tabella e il file di dati, qualora la tabella esista già.

   * `CREATE EXTERNAL TABLE`: crea una nuova tabella "esterna" in Hive.
   Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati rimangono nel percorso originale.

   * `ROW FORMAT`: indica il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.

   * `STORED AS TEXTFILE LOCATION`: indica dove sono archiviati i dati e che sono archiviati come testo.

   * `SELECT`: seleziona un conteggio di tutte le righe in cui la colonna t4 include il valore [ERROR].

     > [!NOTE]
     > Usa le tabelle esterne se si prevede che i dati sottostanti verranno aggiornati da un'origine esterna. Ad esempio, un processo di caricamento dati automatizzato o un'altra operazione MapReduce. L'eliminazione di una tabella esterna *non* comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.

2. Usare il pulsante **Execute** (Esegui) nella parte inferiore di Query Editor per avviare la query. Il pulsante diventa arancione e il testo cambia in **Stop execution** (Arresta esecuzione). Sotto Query Editor (Editor di query) verrà visualizzata la sezione **Query Process Results** (Risultati elaborazione query) con le informazioni sul processo.

   > [!IMPORTANT]
   > È possibile che alcuni browser non aggiornino correttamente il log o le informazioni sui risultati. Se si esegue un processo che prosegue all'infinito senza aggiornare il log o restituire risultati, provare a usare Mozilla FireFox o Google Chrome.

3. Al termine dell'elaborazione della query, nella sezione **Query Process Results** (Risultati elaborazione query) vengono visualizzati i risultati dell'operazione. Il pulsante **Stop execution** (Arresta esecuzione) verrà sostituito da un pulsante **Execute** (Esegui) verde al completamento della query. La scheda **Results** conterrà le informazioni seguenti:

        sev       cnt
        [ERROR]   3

    La scheda **Logs** può essere usata per visualizzare le informazioni sulla registrazione create dal processo,

   > [!TIP]
   > La finestra di dialogo **Save results** (Salva risultati) nella parte superiore sinistra della sezione **Query Process Results** (Risultati del processo query) consente di scaricare o salvare i risultati.

4. Selezionare le prime quattro righe di questa query, quindi selezionare **Execute** (Esegui). Si noti che, al termine del processo, non viene visualizzato alcun risultato. Se si usa il pulsante **Execute** (Esegui) quando parte della query è selezionata, verranno eseguite solo le istruzioni selezionate. In questo caso, la selezione non include l'istruzione finale che recupera le righe dalla tabella. Se si seleziona solo tale riga e si usa **Execute** (Esegui), verranno visualizzati i risultati previsti.

5. Per aggiungere un foglio di lavoro usare il pulsate **New Worksheet** (Nuovo foglio di lavoro) nella parte inferiore di **Query Editor**. Nel nuovo foglio di lavoro immettere le istruzioni HiveQL seguenti:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Di seguito sono elencate le istruzioni che eseguono queste azioni:

   * **CREATE TABLE IF NOT EXISTS** : crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, viene creata una tabella interna. Una tabella interna verrà archiviata nel data warehouse di Hive e gestita completamente da Hive. A differenza delle tabelle esterne, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.

   * **STORED AS ORC** : archivia i dati nel formato ORC (Optimized Row Columnar). ORC è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.

   * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti `[ERROR]` e quindi inserisce i dati nella tabella **errorLogs**.

     Usare il pulsante **Execute** (Esegui) per eseguire la query. La scheda **Results** (Risultati) non contiene informazioni quando la query restituisce zero righe. Lo stato visualizzato deve essere **SUCCEEDED** dopo il completamento della query.

### <a name="hive-settings"></a>Settings di Hive

Selezionare l'icona **Settings** (Impostazioni) a destra dell'editor.

![Icona di impostazioni per la vista di hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

Le impostazioni possono essere usate per modificare varie impostazioni di Hive. Consentono ad esempio di cambiare il motore di esecuzione per Hive da Tez (opzione predefinita) a MapReduce.

### <a name="visualization"></a>Visualizzazione

Selezionare l'icona __Visualization__ (Visualizzazione) a destra dell'editor.

![Icona di visualizzazione per la vista di Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

L'interfaccia di visualizzazione consente di creare visualizzazioni dei dati restituiti dalla query. Nell'immagine seguente è illustrata una visualizzazione di esempio basata sui dati di `hivesampletable` inclusi in HDInsight:

![Visualizzazione di esempio](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>Visual Explain

Selezionare l'icona **Visual Explain** a destra dell'editor.

![Icona di Visual Explain per la vista di Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

La vista **Visual Explain** (Spiegazione visiva) della query può essere utile per conoscere il flusso delle query complesse. Per un equivalente testuale di questa visualizzazione, usare il pulsante **Explain** (Spiega) in Query Editor.

![Immagine di Visual Explain](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

Selezionare l'icona **Tez** a destra dell'editor.

![Icona di Tez per la vista di hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

Se per risolvere la query è stato usato Tez, viene visualizzato il grafo aciclico diretto. Se si vuole visualizzare il DAG per le query eseguite in passato o eseguire il debug del processo Tez, usare invece [Tez View](hdinsight-debug-ambari-tez-view.md) .

### <a name="notifications"></a>Notifiche

Selezionare l'icona **Notifications** (Notifiche) a destra dell'editor.

![Icona delle notifiche per la vista di hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

Le notifiche sono messaggi generati quando si eseguono query. Ad esempio, si riceverà una notifica quando viene inviata una query o quando si verifica un errore.

## <a name="saved-queries"></a>Query salvate

1. In Query Editor creare un foglio di lavoro e immettere la query seguente:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Eseguire la query per verificare che funzioni. I risultati sono simili all'esempio seguente:

        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. Usare il pulsante **Save as** (Salva con nome) nella parte inferiore dell'editor. Assegnare a questa query il nome **Errorlogs** e selezionare **OK**. Il nome del foglio di lavoro diventa **Errorlogs**.

3. Selezionare la scheda **Saved Queries** (Query salvate) nella parte superiore della pagina della visualizzazione Hive. Ora **Errorlogs** viene elencato come una query salvata e rimarrà in questo elenco fino a quando non viene rimosso. Selezionando il nome, la query verrà aperta in Query Editor.

## <a name="query-history"></a>Cronologia delle query

Il pulsante **History** (Cronologia) nella parte superiore della vista Hive consente di visualizzare le query eseguite in precedenza. Usarlo per selezionare una delle query eseguite in precedenza. Quando si seleziona una query, viene aperta in Query Editor.

## <a name="user-defined-functions-udf"></a>Funzioni definite dall'utente (UDF)

Hive può anche essere esteso tramite **funzioni definite dall'utente (UDF)**, che consentono di implementare funzionalità o logica non facilmente modellate in HiveQL.

La scheda della funzione definita dall'utente nella parte superiore della vista Hive consente di dichiarare e salvare un set di funzioni definite dall'utente, che è possibile usare con **Query Editor**.

Dopo avere aggiunto una funzione definita dall'utente alla visualizzazione Hive, verrà visualizzato un pulsante **Insert udfs** (Inserisci funzioni definite dall'utente) nella parte inferiore di **Query Editor**. Se si seleziona questa voce, verrà visualizzato un elenco di riepilogo a discesa di funzioni definite dall'utente nella vista Hive. La selezione di una funzione definita dall'utente aggiungerà istruzioni HiveQL alla query per abilitare la funzione definita dall'utente.

Ad esempio, se è stata specificata una funzione definita dall'utente con le proprietà seguenti:

* Nome della risorsa: myudfs

* Percorso della risorsa: /myudfs.jar

* Nome della funzione definita dall'utente: myawesomeudf

* Nome della classe per la funzione definita dall'utente: com.myudfs.Awesome

Se si usa il pulsante **Insert udfs** (Inserisci funzioni definite dall'utente), verrà visualizzata una voce denominata **myudfs**, con un altro elenco a discesa per ogni funzione definita dall'utente specificata per tale risorsa. In questo caso, **myawesomeudf**. Se si seleziona questa voce, verrà aggiunto quanto segue all'inizio della query:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Si potrà quindi usare la funzione definita dall'utente nella query, Ad esempio, `SELECT myawesomeudf(name) FROM people;`.

Per altre informazioni sull'uso di funzioni definite dall'utente con Hive in HDInsight, vedere i documenti seguenti:

* [Usare Python con Hive e Pig in HDInsight](hdinsight-python.md)
* [Come aggiungere una funzione definita dall'utente Hive personalizzata in HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

