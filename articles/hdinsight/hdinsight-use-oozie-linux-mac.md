---
title: Usare flussi di lavoro Oozie con Hadoop in HDInsight basato su Linux
description: Usare Oozie di Hadoop in HDInsight basati su Linux. Informazioni su come definire un flusso di lavoro Oozie e inviare un processo Oozie.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 7b0d3ac4775ca057856c28ab42197bb734f149d6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534941"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Usare Apache Oozie con Apache Hadoop per definire ed eseguire un flusso di lavoro in Azure HDInsight basato su Linux

Informazioni su come usare Apache Oozie con Apache Hadoop in Azure HDInsight. Oozie è un sistema di coordinamento dei flussi di lavoro che consente di gestire i processi Hadoop. Oozie è integrato con lo stack Hadoop e supporta i processi seguenti:

* Apache Hadoop MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Oozie può anche essere usato per pianificare processi specifici di un sistema, come programmi Java o script della shell.

> [!NOTE]  
> Per definire i flussi di lavoro con HDInsight, è anche possibile usare Azure Data Factory. Per altre informazioni su Azure Data Factory, vedere [Trasformare i dati in Azure Data Factory](../data-factory/transform-data.md). Per usare Oozie nei cluster con Enterprise Security Package, vedere [Eseguire Apache Oozie nei cluster HDInsight Hadoop con Enterprise Security Package](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md).

## <a name="prerequisites"></a>Prerequisites

* **Un cluster Hadoop in HDInsight** . Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* **Un client SSH** . Vedere [connettersi a HDInsight (Apache Hadoop) tramite SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Un database SQL di Azure** .  Vedere [creare un database nel database SQL di Azure nel portale di Azure](../azure-sql/database/single-database-create-quickstart.md).  Questo articolo usa un database denominato **oozietest** .

* Lo schema URI per l'archiviazione primaria dei cluster. `wasb://` per l'archiviazione di Azure, `abfs://` per Azure Data Lake storage Gen2 o `adl://` per Azure Data Lake storage Gen1. Se il trasferimento sicuro è abilitato per Archiviazione di Azure, l'URI sarà `wasbs://`. Vedere anche l'articolo sul [trasferimento sicuro](../storage/common/storage-require-secure-transfer.md).

## <a name="example-workflow"></a>Esempio di flusso di lavoro

Il flusso di lavoro usato in questo documento prevede due azioni. Le azioni sono definizioni di attività, ad esempio l'esecuzione di processi Hive, Sqoop, MapReduce o altri:

![Diagramma del flusso di lavoro oozie HDInsight](./media/hdinsight-use-oozie-linux-mac/oozie-workflow-diagram.png)

1. Un'azione hive esegue uno script HiveQL per estrarre i record da `hivesampletable` incluso in HDInsight. Ogni riga di dati descrive una visita da un dispositivo mobile specifico. Il formato del record risulterà simile al testo seguente:

    ```output
    8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
    23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
    23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1
    ```

    Lo script Hive usato in questo documento conta le visite totali per ogni piattaforma (ad esempio Android o iPhone) e archivia i conteggi in una nuova tabella Hive.

    Per altre informazioni su hive, vedere [usare Apache Hive con HDInsight] [HDInsight-use-hive].

2. Un'azione di Sqoop esporta il contenuto della nuova tabella Hive in una tabella creata nel database SQL di Azure. Per altre informazioni su Sqoop, vedere [Usare Apache Sqoop con HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md).

> [!NOTE]  
> Per informazioni sulle versioni di oozie supportate nei cluster HDInsight, vedere Novità [delle versioni cluster di Hadoop fornite da HDInsight](hdinsight-component-versioning.md).

## <a name="create-the-working-directory"></a>Creare la directory di lavoro

Oozie presuppone che tutte le risorse necessarie per un processo siano archiviate nella stessa directory. In questo esempio viene utilizzato `wasbs:///tutorials/useoozie`. Per creare questa directory, completare la procedura seguente:

1. Modificare il codice seguente per sostituire `sshuser` con il nome utente SSH per il cluster e sostituire `CLUSTERNAME` con il nome del cluster.  Immettere quindi il codice per connettersi al cluster HDInsight [usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).  

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Per creare la directory, usare il comando seguente:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > Il parametro `-p` crea tutte le directory nel percorso. La `data` Directory viene utilizzata per conservare i dati utilizzati dallo `useooziewf.hql` script.

3. Modificare il codice seguente per sostituire `sshuser` con il nome utente SSH.  Per assicurarsi che Oozie possa rappresentare l'account utente, usare il comando seguente:

    ```bash
    sudo adduser sshuser users
    ```

    > [!NOTE]  
    > È possibile ignorare gli errori che indicano che l'utente è già un membro del gruppo `users`.

## <a name="add-a-database-driver"></a>Aggiungere un driver di database

Questo flusso di lavoro USA Sqoop per esportare i dati nel database SQL. Pertanto, è necessario fornire una copia del driver JDBC usato per interagire con il database SQL. Per copiare il driver JDBC nella directory di lavoro, usare il comando seguente della sessione SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> Verificare il driver JDBC effettivo presente in `/usr/share/java/` .

Se il flusso di lavoro ha usato altre risorse, ad esempio un file con estensione jar contenente un'applicazione MapReduce, è necessario aggiungere anche queste risorse.

## <a name="define-the-hive-query"></a>Definire la query Hive

Usare la procedura seguente per creare uno script HiveQL (linguaggio di query Hive) che definisce una query La query verrà usata in un flusso di lavoro oozie più avanti in questo documento.

1. Dalla connessione SSH, usare il comando seguente per creare un file denominato `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

1. All'apertura dell'editor nano GNU, usare la query seguente come contenuto del file:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Nello script vengono usate due variabili:

   * `${hiveTableName}`: contiene il nome della tabella da creare.

   * `${hiveDataFolder}`: contiene il percorso in cui archiviare i file di dati per la tabella.

     Il file di definizione del flusso di lavoro, workflow.xml in questo articolo, passa questi valori allo script HiveQL in fase di esecuzione.

1. Per salvare il file, premere **CTRL + X** , immettere **Y** , quindi premere **invio** .  

1. Usare il comando seguente per copiare `useooziewf.hql` `wasbs:///tutorials/useoozie/useooziewf.hql` :

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Questo comando archivia il file `useooziewf.hql` nella risorsa di archiviazione compatibile con HDFS per il cluster.

## <a name="define-the-workflow"></a>Definire il flusso di lavoro

Le definizioni del flusso di lavoro di Oozie sono scritte in un linguaggio di definizione dei processi XML denominato hPDL (Hadoop Process Definition Language). Usare i passaggi seguenti per definire il flusso di lavoro:

1. Usare l'istruzione seguente per creare e modificare un nuovo file:

    ```bash
    nano workflow.xml
    ```

2. All'apertura dell'editor nano, immettere il codice XML seguente come contenuto del file:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Nel flusso di lavoro vengono definite due azioni:

   * `RunHiveScript`: azione di avvio che esegue lo script `useooziewf.hql` di Hive.

   * `RunSqoopExport`: azione che esporta i dati creati dallo script Hive nel database SQL tramite Sqoop. Questa azione viene eseguita solo se l'azione `RunHiveScript` ha esito positivo.

     Il flusso di lavoro include molte voci, ad esempio `${jobTracker}`. Queste voci verranno sostituite con i valori usati nella definizione del processo. La definizione del processo verrà creata più avanti in questo documento.

     Notare anche la voce `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` nella sezione Sqoop. Questa voce indica a Oozie di rendere disponibile questo archivio per Sqoop quando l'azione viene eseguita.

3. Per salvare il file, premere **CTRL + X** , immettere **Y** , quindi premere **invio** .  

4. Usare il comando seguente per copiare il file `workflow.xml` in `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>Creare una tabella

> [!NOTE]  
> Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](https://www.freetds.org/) dal cluster HDInsight.

1. Usare il comando seguente per installare FreeTDS nel cluster HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Modificare il codice riportato di seguito per sostituire `<serverName>` con il nome del [server SQL logico](../azure-sql/database/logical-servers.md) e `<sqlLogin>` con l'account di accesso del server.  Immettere il comando per connettersi al database SQL prerequisito.  Immettere la password al prompt.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    L'output ricevuto ha un aspetto simile al testo seguente:

    ```output
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to oozietest
    1>
    ```

3. Al prompt `1>`, immettere il codice seguente:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Dopo aver immesso l'istruzione `GO`, vengono valutate le istruzioni precedenti. Queste istruzioni creano una tabella, denominata `mobiledata` , che viene usata dal flusso di lavoro.

    Per verificare che la tabella sia stata creata, usare i comandi seguenti:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    Viene visualizzato un output simile al testo seguente:

    ```output
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo             mobiledata      BASE TABLE
    ```

4. Uscire dall'utilità TSQL immettendo `exit` al `1>` prompt.

## <a name="create-the-job-definition"></a>Creare la definizione del processo

La definizione del processo descrive dove trovare il file workflow.xml. Descrive inoltre dove trovare altri file usati dal flusso di lavoro, ad esempio `useooziewf.hql`. Definisce inoltre i valori per le proprietà utilizzate all'interno del flusso di lavoro e i file associati.

1. Per ottenere l'indirizzo completo della risorsa di archiviazione predefinita, usare il comando seguente. L'indirizzo viene usato nel file di configurazione che verrà creato nel passaggio successivo.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Questo comando restituisce informazioni simili al codice XML seguente:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > Se il cluster HDInsight usa l'archiviazione di Azure come percorso di archiviazione predefinito, il contenuto dell'elemento `<value>` inizia con `wasbs://`. Se invece si usa Azure Data Lake Storage Gen1, l'elemento inizia con `adl://`. Se si usa Azure Data Lake Storage Gen2, l'elemento inizia con `abfs://`.

    Salvare il contenuto dell'elemento `<value>`, necessario nei passaggi successivi.

2. Modificare il codice XML seguente come segue:

    |Valore segnaposto| Valore sostituito|
    |---|---|
    |\@mystorageaccount.blob.core.windows.net wasbs://mycontainer| Valore ricevuto al passaggio 1.|
    |admin| Nome dell'account di accesso per il cluster HDInsight, se non è amministratore.|
    |serverName| Nome del server di database SQL di Azure.|
    |sqlLogin| Accesso al server di database SQL di Azure.|
    |sqlPassword| Password di accesso al server del database SQL di Azure.|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    La maggior parte delle informazioni in questo file viene usata per popolare i valori usati nel file workflow.xml o ooziewf.hql (ad esempio `${nameNode}`).  Se il percorso è un percorso `wasbs`, è necessario usare il percorso completo, Non abbreviarlo semplicemente `wasbs:///` . La voce `oozie.wf.application.path` definisce il percorso in cui trovare il file workflow.xml, che contiene il flusso di lavoro eseguito da questo processo.

3. Per creare la configurazione della definizione del processo Oozie, usare il comando seguente:

    ```bash
    nano job.xml
    ```

4. Dopo l'apertura dell'editor nano, incollare il codice XML modificato come contenuto del file.

5. Per salvare il file, premere **CTRL + X** , immettere **Y** , quindi premere **invio** .

## <a name="submit-and-manage-the-job"></a>Inviare e gestire il processo

La procedura seguente usa il comando Oozie per inviare e gestire i flussi di lavoro di Oozie nel cluster. Il comando Oozie è un'interfaccia utente semplice per l' [API REST di Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]  
> Quando si usa il comando Oozie, è necessario usare l'FQDN per il nodo head di HDInsight. Questo FQDN è accessibile solo dal cluster o, se il cluster si trova in una rete virtuale di Azure, da altri computer nella stessa rete.

1. Per ottenere l'URL del servizio di Oozie, usare questo comando:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Vengono restituite informazioni simili al codice XML seguente:

    ```xml
    <name>oozie.base.url</name>
    <value>http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    La parte `http://ACTIVE-HEADNODE-NAME.UNIQUEID.cx.internal.cloudapp.net:11000/oozie` è l'URL da usare con il comando Oozie.

2. Modificare il codice per sostituire l'URL con quello ricevuto in precedenza. Per creare una variabile di ambiente per l'URL, usare il codice seguente, in modo che non sia necessario eseguirne la digitazione per ogni comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. Per inviare il processo, usare il codice seguente:

    ```bash
    oozie job -config job.xml -submit
    ```

    Questo comando carica le informazioni sul processo da `job.xml` e le invia a oozie, ma non lo esegue.

    Dopo il completamento, il comando dovrebbe restituire l'ID del processo, ad esempio `0000005-150622124850154-oozie-oozi-W`. L'ID viene usato per gestire il processo.

4. Modificare il codice riportato di seguito per sostituire `<JOBID>` con l'ID restituito nel passaggio precedente.  Per visualizzare lo stato del processo, usare il comando seguente:

    ```bash
    oozie job -info <JOBID>
    ```

    Verranno restituite informazioni simili al testo seguente:

    ```output
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasb:///tutorials/useoozie
    Status        : PREP
    Run           : 0
    User          : USERNAME
    Group         : -
    Created       : 2015-06-22 15:06 GMT
    Started       : -
    Last Modified : 2015-06-22 15:06 GMT
    Ended         : -
    CoordAction ID: -
    ------------------------------------------------------------------------------------------------------------------------------------
    ```

    Lo stato del processo è `PREP`. Questo stato indica che il processo è stato creato, ma non avviato.

5. Modificare il codice riportato di seguito per sostituire `<JOBID>` con l'ID restituito in precedenza.  Per avviare il processo, usare il comando seguente:

    ```bash
    oozie job -start <JOBID>
    ```

    Dopo questo comando, lo stato risulterà in esecuzione e verranno restituite informazioni relative alle azioni all'interno del processo.  Il completamento del processo può richiedere alcuni minuti.

6. Modificare il codice riportato di seguito per sostituire `<serverName>` con il nome del server e `<sqlLogin>` con l'account di accesso del server.  *Una volta completata l'attività* , è possibile verificare che i dati siano stati generati ed esportati nella tabella del database SQL usando il comando seguente.  Immettere la password al prompt.

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    Al prompt di `1>` immettere la query seguente:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Le informazioni restituite sono simili al testo seguente:

    ```output
    deviceplatform  count
    Android 31591
    iPhone OS       22731
    proprietary development 3
    RIM OS  3464
    Unknown 213
    Windows Phone   1791
    (6 rows affected)
    ```

Per altre informazioni sul comando Oozie, vedere [Oozie command-line tool](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html) (Strumento da riga di comando di Apache Oozie).

## <a name="oozie-rest-api"></a>API REST di Oozie

Con l'API REST di Oozie, è possibile compilare strumenti personalizzati che funzionano con Oozie. Di seguito sono riportate informazioni specifiche di HDInsight sull'uso dell'API REST di oozie:

* **URI** : è possibile accedere all'API REST all'esterno del cluster in `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticazione** : per eseguire l'autenticazione, usare l'API, l'account (admin) e la password HTTP del cluster, Ad esempio:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Per altre informazioni sull'uso dell'API REST di Oozie, vedere la pagina relativa all'[API dei servizi Web di Apache Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

## <a name="oozie-web-ui"></a>Interfaccia utente Web di Oozie

L'interfaccia utente Web di Oozie fornisce una visualizzazione basata sul Web dello stato dei processi Oozie nel cluster. L'interfaccia utente Web consente di visualizzare le informazioni seguenti:

   * Stato processo
   * Definizione del processo
   * Configurazione
   * Grafico delle azioni nel processo
   * Log per il processo

È anche possibile visualizzare i dettagli delle azioni all'interno di un processo.

Per accedere all'interfaccia utente Web di Oozie, completare la procedura seguente:

1. Creare un tunnel SSH per il cluster HDInsight. Per altre informazioni, vedere [Usare il tunneling SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Dopo aver creato un tunnel, aprire l'interfaccia utente Web di Ambariri nel Web browser usando URI `http://headnodehost:8080` .

3. Sul lato sinistro della pagina selezionare **oozie**  >  **Quick Links**  >  **oozie Web UI** .

    ![Passaggi dell'interfaccia utente Web di Apache Ambari OOZIE](./media/hdinsight-use-oozie-linux-mac/hdi-oozie-web-ui-steps.png)

4. Per impostazione predefinita, nell'interfaccia utente Web di Oozie sono visualizzati i processi del flusso di lavoro in esecuzione. Per visualizzare tutti i processi del flusso di lavoro, selezionare **All Jobs** (Tutti i processi).

    ![Processi del flusso di lavoro della console Web OOZIE](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-jobs.png)

5. Per visualizzare altre informazioni specifiche di un processo, selezionarlo.

    ![Informazioni sul processo di HDInsight Apache OOZIE](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-info.png)

6. Nella scheda **Job Info** (Informazioni processo) è possibile visualizzare informazioni di base sul processo e le singole azioni all'interno del processo. È possibile usare le schede nella parte superiore per visualizzare **Job Definition** (Definizione processo) e **Job Configuration** (Configurazione processo), accedere a **Job Log** (Log processo) o visualizzare un grafo aciclico diretto (DAG) del processo in **Job DAG** (DAG processo).

   * **Job Log** (Log processo): selezionare il pulsante **Get Logs** (Ottieni log) per recuperare tutti i log relativi al processo o usare il campo **Enter Search Filter** (Immetti filtro di ricerca) per filtrare i log.

       ![Log del processo Apache oozie di HDInsight](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-log.png)

   * **Job DAG** (DAG processo): il grafo aciclico diretto (DAG) è una rappresentazione grafica dei percorsi dati rilevati nel flusso di lavoro.

       ![' HDInsight Apache oozie Job DAG '](./media/hdinsight-use-oozie-linux-mac/hdinsight-oozie-job-dag.png)

7. Se si seleziona una delle azioni dalla scheda **Job Info** (Informazioni processo), vengono visualizzate informazioni sull'azione. Selezionare ad esempio l'azione **RunSqoopExport** .

    ![Informazioni sull'azione del processo oozie di HDInsight](./media/hdinsight-use-oozie-linux-mac/oozie-job-action-info.png)

8. È possibile visualizzare i dettagli per l'azione, ad esempio un collegamento a **Console URL** (URL della console). Usare questo collegamento per visualizzare le informazioni di JobTracker per il processo.

## <a name="schedule-jobs"></a>Pianificare i processi

È possibile usare il coordinatore per specificare inizio, fine e frequenza dei processi. Per definire una pianificazione per il flusso di lavoro, completare la procedura seguente:

1. Usare il comando seguente per creare un file denominato **coordinator.xml** :

    ```bash
    nano coordinator.xml
    ```

    Usare il codice XML seguente come contenuto del file:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > Le variabili `${...}` vengono sostituite dai valori nella definizione del processo in fase di esecuzione. Le variabili sono:
    >
    > * `${coordFrequency}`: tempo trascorso tra l'esecuzione delle istanze del processo.
    > * `${coordStart}`: ora di inizio del processo.
    > * `${coordEnd}`: ora di fine del processo.
    > * `${coordTimezone}`: i processi del coordinatore si trovano in un fuso orario fisso che non tiene conto dell'ora legale (in genere rappresentato dall'acronimo UTC). Questo fuso orario viene definito *fuso orario di elaborazione oozie.*
    > * `${wfPath}`: percorso del file workflow.xml.

2. Per salvare il file, premere **CTRL + X** , immettere **Y** , quindi premere **invio** .

3. Per copiare il file nella directory di lavoro del processo, usare il comando seguente:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Per modificare il `job.xml` file creato in precedenza, usare il comando seguente:

    ```bash
    nano job.xml
    ```

    Apportare le modifiche seguenti:

   * Per indicare a Oozie di eseguire il file del coordinatore invece del flusso di lavoro, sostituire `<name>oozie.wf.application.path</name>` con `<name>oozie.coord.application.path</name>`.

   * Per impostare la variabile `workflowPath` usata dal coordinatore, aggiornare il codice XML seguente:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Sostituire il testo `wasbs://mycontainer@mystorageaccount.blob.core.windows` con il valore usato nelle altre voci del file job.xlm.

   * Per definire l'inizio, la fine e la frequenza per il coordinatore, aggiungere il codice XML seguente:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Questi valori impostano l'ora di inizio su 12:00 PM il 10 maggio 2018 e l'ora di fine fino al 12 maggio 2018. L'intervallo per l'esecuzione del processo viene impostato come giornaliero. La frequenza è espressa in minuti, quindi 24 ore x 60 minuti = 1440 minuti. Il fuso orario è infine impostato su UTC.

5. Per salvare il file, premere **CTRL + X** , immettere **Y** , quindi premere **invio** .

6. Per inviare e avviare il processo, usare il comando seguente:

    ```bash
    oozie job -config job.xml -run
    ```

7. Se si accede all'interfaccia utente Web di Oozie e si seleziona la scheda **Coordinator Jobs** (Processi coordinatore), si ottengono informazioni simili a quelle nell'immagine seguente:

    ![Scheda processi coordinatore della console Web OOZIE](./media/hdinsight-use-oozie-linux-mac/coordinator-jobs-tab.png)

    La voce **Next Materialization** (Materializzazione successiva) contiene l'orario per l'esecuzione successiva del processo.

8. Come per il processo del flusso di lavoro precedente, se si seleziona la voce del processo nell'interfaccia utente Web vengono visualizzate informazioni sul processo:

    ![Informazioni sul processo di Apache oozie Coordinator](./media/hdinsight-use-oozie-linux-mac/coordinator-job-info.png)

    > [!NOTE]  
    > Questa immagine visualizza solo le esecuzioni riuscite del processo, non le singole azioni nel flusso di lavoro pianificato. Per visualizzare le singole azioni, selezionare una delle voci relative alle **azioni** .

    ![Scheda informazioni processo della console Web OOzie](./media/hdinsight-use-oozie-linux-mac/coordinator-action-job.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come definire un flusso di lavoro di oozie e come eseguire un processo oozie. Per altre informazioni sull'uso di HDInsight, vedere gli articoli seguenti:

* [Caricare dati per processi Apache Hadoop in HDInsight](hdinsight-upload-data.md)
* [Usare Apache Sqoop con Apache Hadoop in HDInsight](hadoop/apache-hadoop-use-sqoop-mac-linux.md)
* [Usare Apache Hive con Apache Hadoop su HDInsight](hadoop/hdinsight-use-hive.md)
* [Risolvere i problemi di Apache Oozie](./troubleshoot-oozie.md)