---
title: Iniziare a usare un esempio di HBase in HDInsight - Azure
description: Seguire questo esempio di Apache HBase per iniziare a usare Hadoop in HDInsight. Creare tabelle dalla shell HBase e sottoporle a query tramite Hive.
keywords: hbasecommand, esempio di hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 9d94a976c08cdb5184ea4c5e2cd70ac039d78378
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66384687"
---
# <a name="get-started-with-an-apache-hbase-example-in-hdinsight"></a>Iniziare a usare un esempio di Apache HBase in HDInsight

Informazioni su come creare un cluster [Apache HBase](https://hbase.apache.org/) in HDInsight, creare tabelle HBase ed eseguire query sulle tabelle con [Apache Hive](https://hive.apache.org/).  Per informazioni generali su HBase, vedere [Panoramica su HBase in HDInsight](./apache-hbase-overview.md).

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Gli esempi in questo articolo usano la shell di Bash in Windows 10 per i comandi di curl. Visualizzare [sottosistema Windows per la Guida all'installazione di Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) per i passaggi di installazione.  Altri [shell Unix](https://www.gnu.org/software/bash/) funzioneranno anche.  Gli esempi di curl, con alcune lievi modifiche, è possono lavorare in un prompt dei comandi di Windows.  In alternativa, è possibile usare il cmdlet di Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).


## <a name="create-apache-hbase-cluster"></a>Creare un cluster Apache HBase

La procedura seguente usa un modello di Azure Resource Manager per creare un cluster HBase e il valore predefinito dipendente dall'account di archiviazione di Azure. Per comprendere i parametri usati nella procedure e altri metodi di creazione del cluster, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso di Data Lake Storage Gen2, vedi [Avvio rapido: Impostazione dei cluster in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)

1. Selezionare l'immagine seguente per aprire il modello nel portale di Azure. Il modello è disponibile nel [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Compilare i campi seguenti del pannello **Distribuzione personalizzata**:

    |Proprietà |Descrizione |
    |---|---|
    |Sottoscrizione|selezionare la sottoscrizione di Azure che viene usata per creare il cluster.|
    |Gruppo di risorse|creare un nuovo gruppo di Azure Resource Manager o usarne uno esistente.|
    |Località|consente di specificare la posizione del gruppo di risorse. |
    |ClusterName|immettere un nome per il cluster HBase.|
    |Nome account di accesso del cluster e la password|Il nome di accesso predefinito è **admin**.|
    |Nome utente SSH e password|Il nome utente predefinito è **sshuser**.|

    Gli altri parametri sono facoltativi.  

    Ogni cluster ha una dipendenza dall'account di Archiviazione di Azure. Dopo aver eliminato un cluster, i dati vengono mantenuti nell'account di archiviazione. Il nome dell'account di archiviazione predefinito del cluster è il nome del cluster a cui viene aggiunto "store". È hardcoded nella sezione delle variabili del modello.

3. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista**. La creazione di un cluster richiede circa 20 minuti.

> [!NOTE]  
> Dopo l'eliminazione di un cluster HBase, è possibile creare un altro cluster HBase usando lo stesso contenitore di BLOB predefinito. Il nuovo cluster seleziona le tabelle HBase create nel cluster originale. Per evitare incoerenze, è consigliabile disabilitare le tabelle HBase prima di eliminare il cluster.

## <a name="create-tables-and-insert-data"></a>Creare tabelle e inserire dati

È possibile usare SSH per connettersi ai cluster HBase e usare la [shell di Apache HBase](https://hbase.apache.org/0.94/book/shell.html) per creare tabelle HBase, inserire dati ed eseguire query sui dati. Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Per la maggior parte delle persone, i dati vengono visualizzati in formato tabulare:

![Tabella con dati HBase di HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

In HBase, che rappresenta un'implementazione di [Cloud BigTable](https://cloud.google.com/bigtable/), gli stessi dati sono simili a:

![Dati BigTable HBase di HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Per usare la shell HBase**

1. Usare il comando `ssh` per connettersi al cluster HBase. Modificare il comando seguente sostituendo `CLUSTERNAME` con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Usare il comando `hbase shell` per avviare la shell interattiva di HBase. Immettere il comando seguente nella connessione SSH:

    ```bash
    hbase shell
    ```

1. Usare il comando `create` per creare una tabella HBase con famiglie a due colonne. I nomi di tabella e colonna sono tra maiuscole e minuscole. Immettere il comando seguente:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Usare il comando `list` per elencare tutte le tabelle in HBase. Immettere il comando seguente:

    ```hbase
    list
    ```

1. Usare il comando `put` per inserire i valori per una determinata colonna in una determinata riga di una determinata tabella. Immettere i comandi seguenti:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Usare il comando `scan` per analizzare e restituire i dati della tabella `Contacts`. Immettere il comando seguente:

    ```hbase
    scan 'Contacts'
    ```

    ![Shell HBase Hadoop di HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Usare il comando `get` per recuperare il contenuto di una riga. Immettere il comando seguente:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Verranno visualizzati risultati simili a quelli che si ottengono usando il comando `scan` perché esiste solo una riga.

    Per altre informazioni sullo schema di tabella HBase, vedere [Introduzione alla progettazione dello schema Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Per altri comandi HBase, vedere [Apache HBase Reference Guide](https://hbase.apache.org/book.html#quickstart) (Guida di riferimento di Apache HBase).

1. Usare il comando `exit` per interrompere la shell interattiva di HBase. Immettere il comando seguente:

    ```hbaseshell
    exit
    ```

**Per il caricamento bulk dei dati nella tabella HBase dei contatti**

HBase include diversi metodi di caricamento dei dati nelle tabelle.  Per altre informazioni, vedere [Caricamento bulk](https://hbase.apache.org/book.html#arch.bulk.load).

Un file di dati di esempio sono reperibili in un contenitore blob pubblico, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Il contenuto del file di dati è il seguente:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

È facoltativamente possibile creare un file di testo e caricare il file nel proprio account di archiviazione. Per istruzioni, vedere [caricare dati per processi Apache Hadoop in HDInsight](../hdinsight-upload-data.md).

> [!NOTE]  
> In questa procedura viene utilizzata la tabella HBase dei contatti creata nella procedura precedente.

1. Dallo stato aperto ssh connessione, eseguire il comando seguente per trasformare i dati file in StoreFiles e archiviare in un percorso relativo specificato da `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Eseguire il comando seguente per caricare i dati da `/example/data/storeDataFileOutput` alla tabella HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. È possibile aprire la shell HBase e usare il `scan` comando per elencare il contenuto della tabella.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Usare Hive per eseguire query su Apache HBase

È possibile eseguire query sui dati nelle tabelle HBase tramite [Apache Hive](https://hive.apache.org/). In questa sezione si crea una tabella Hive che esegue il mapping alla tabella HBase e la usa per la query dei dati nella tabella HBase.

1. Da di aprire la connessione ssh, usare il comando seguente per avviare Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Per altre informazioni su Beeline, vedere [Usare Hive con Hadoop in HDInsight con Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Eseguire il seguente script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) per creare una tabella Hive mappata alla tabella HBase. Prima di eseguire questa istruzione, assicurarsi di aver creato la tabella di esempio usata precedentemente in questa esercitazione come riferimento con la shell HBase.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Eseguire il seguente script HiveQL per eseguire query sui dati nella tabella HBase:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Per uscire da Beeline, usare `!exit`.

1. Per chiudere il ssh connessione, usare `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Usare le API REST HBase mediante Curl

L'API REST viene protetta tramite l' [autenticazione di base](https://en.wikipedia.org/wiki/Basic_access_authentication). Le richieste vengono sempre eseguite usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Avviare la variabile di ambiente per semplicità d'uso. Modificare i seguenti comandi sostituendo `MYPASSWORD` con la password dell'account di accesso del cluster. Sostituire `MYCLUSTERNAME` con il nome del cluster HBase. Immettere quindi i comandi.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Usare il comando seguente per ottenere l'elenco delle tabelle HBase esistenti:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Usare il comando seguente per creare una nuova tabella HBase con famiglie di due colonne:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Lo schema viene fornito nel formato JSON.
1. Usare il comando seguente per inserire alcuni dati:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    È necessario applicare la codifica base64 ai valori specificati nell'interruttore -d. Nell'esempio:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) consente di inserire più valori in batch.

1. Usare il comando seguente per ottenere una riga:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Per altre informazioni sulle API REST HBase, vedere la [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest)(Guida di riferimento di Apache HBase).

> [!NOTE]  
> Thrift non è supportato da HBase in HDInsight.
>
> Quando si usa Curl o qualsiasi altra forma di comunicazione REST con WebHCat, è necessario autenticare le richieste fornendo il nome utente e la password dell'amministratore cluster HDInsight. È anche necessario specificare il nome del cluster come parte dell'URI (Uniform Resource Identifier) usato per inviare le richieste al server:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Verrà visualizzato un messaggio simile alla risposta seguente:
>   
>        {"status":"ok","version":"v1"}


## <a name="check-cluster-status"></a>Controllare lo stato del cluster

HBase in HDInsight viene fornito con un'interfaccia utente Web per il monitoraggio dei cluster. Usando l’interfaccia Web è possibile richiedere statistiche o informazioni sulle aree.

**Per accedere all'interfaccia utente master HBase**

1. Accedere con l'interfaccia utente Web Ambari a `https://Clustername.azurehdinsight.net`.
2. Fare clic su **HBase** nel menu di sinistra.
3. Fare clic su **Quick links** (Collegamenti rapidi) nella parte superiore della pagina, scegliere il collegamento di nodo Zookeeper attivo e quindi fare clic su **HBase Master UI** (Interfaccia utente master HBase).  L'interfaccia utente viene aperta in un'altra scheda del browser:

   ![Interfaccia utente master HBase di HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   L'interfaccia utente master HBase contiene le sezioni seguenti:

   - server di zona
   - master di backup
   - tables
   - attività
   - attributi di software

## <a name="delete-the-cluster"></a>Eliminare il cluster

Per evitare incoerenze, è consigliabile disabilitare le tabelle HBase prima di eliminare il cluster.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Risoluzione dei problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare tabelle e un cluster Apache HBase e come visualizzare i dati delle tabelle dalla shell HBase. Si è inoltre appreso come usare una query Hive sui dati nelle tabelle HBase e come usare le API REST C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella.

Per altre informazioni, vedere:

* [Panoramica su HBase in HDInsight](./apache-hbase-overview.md): Apache HBase è un database NoSQL open source Apache basato su Apache Hadoop che fornisce accesso rapido e coerenza assoluta per quantità elevate di dati non strutturati e semi-strutturati.