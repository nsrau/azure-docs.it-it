---
title: 'Esercitazione: Usare Apache HBase in Azure HDInsight'
description: Seguire questa esercitazione su Apache HBase per iniziare a usare Hadoop in HDInsight. Creare tabelle dalla shell HBase e sottoporle a query tramite Hive.
keywords: hbasecommand, esempio di hbase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: df216c4d634ac20365cc5a1cc6e26fbd78be7ab9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70917396"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Esercitazione: Usare Apache HBase in Azure HDInsight

Questa esercitazione illustra come creare un cluster Apache HBase in Azure HDInsight, come creare tabelle HBase e come eseguire query sulle tabelle con Apache Hive.  Per informazioni generali su HBase, vedere [Panoramica di HDInsight HBase](./apache-hbase-overview.md).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un cluster Apache HBase
> * Creare tabelle HBase e inserire dati
> * Usare Hive per eseguire query su Apache HBase
> * Usare le API REST HBase mediante Curl
> * Controllare lo stato del cluster

## <a name="prerequisites"></a>Prerequisiti

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Per i comandi curl, gli esempi in questo articolo usano la shell Bash in Windows 10. Per la procedura di installazione, vedere [Guida all'installazione del sottosistema Windows per Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).  Funzionano anche altre [shell Unix](https://www.gnu.org/software/bash/).  Gli esempi di curl, con alcune lievi modifiche, funzionano in un prompt dei comandi di Windows.  In alternativa, è possibile usare il cmdlet di Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Creare un cluster Apache HBase

La procedura seguente usa un modello di Azure Resource Manager per creare un cluster HBase e l'account di Archiviazione di Azure predefinito dipendente. Per comprendere i parametri usati nella procedure e altri metodi di creazione del cluster, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Selezionare l'immagine seguente per aprire il modello nel portale di Azure. Il modello è disponibile in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure"></a>

2. Compilare i campi seguenti del pannello **Distribuzione personalizzata**:

    |Proprietà |DESCRIZIONE |
    |---|---|
    |Subscription|selezionare la sottoscrizione di Azure che viene usata per creare il cluster.|
    |Resource group|creare un nuovo gruppo di Azure Resource Manager o usarne uno esistente.|
    |Location|consente di specificare la posizione del gruppo di risorse. |
    |ClusterName|immettere un nome per il cluster HBase.|
    |ID di accesso e password del cluster|Il nome di accesso predefinito è **admin**.|
    |Nome utente e password SSH|Il nome utente predefinito è **sshuser**.|

    Gli altri parametri sono facoltativi.  

    Ogni cluster ha una dipendenza dall'account di Archiviazione di Azure. Dopo aver eliminato un cluster, i dati vengono mantenuti nell'account di archiviazione. Il nome dell'account di archiviazione predefinito del cluster è il nome del cluster a cui viene aggiunto "store". È hardcoded nella sezione delle variabili del modello.

3. Selezionare **Accetto le condizioni riportate sopra** e quindi **Acquista**. La creazione di un cluster richiede circa 20 minuti.

Dopo l'eliminazione di un cluster HBase, è possibile creare un altro cluster HBase usando lo stesso contenitore di BLOB predefinito. Il nuovo cluster seleziona le tabelle HBase create nel cluster originale. Per evitare incoerenze, è consigliabile disabilitare le tabelle HBase prima di eliminare il cluster.

## <a name="create-tables-and-insert-data"></a>Creare tabelle e inserire dati

È possibile usare SSH per connettersi ai cluster HBase e usare la [shell di Apache HBase](https://hbase.apache.org/0.94/book/shell.html) per creare tabelle HBase, inserire dati ed eseguire query sui dati.

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

1. Usare il comando `create` per creare una tabella HBase con famiglie a due colonne. I nomi di tabella e colonna fanno distinzione tra maiuscole e minuscole. Immettere il comando seguente:

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

Un file di dati di esempio è disponibile in un contenitore BLOB pubblico, `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Il contenuto del file di dati è il seguente:

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

È facoltativamente possibile creare un file di testo e caricare il file nel proprio account di archiviazione. Per le istruzioni, vedere [Caricare dati per processi Apache Hadoop in HDInsight](../hdinsight-upload-data.md).

Questa procedura usa la tabella HBase `Contacts` creata nella procedura precedente.

1. Dalla connessione SSH aperta eseguire il comando seguente per trasformare il file di dati in StoreFiles e archiviarlo in un percorso relativo specificato da `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Eseguire il comando seguente per caricare i dati da `/example/data/storeDataFileOutput` nella tabella HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. È possibile aprire la shell HBase e usare il comando `scan` per visualizzare i contenuti della tabella.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Usare Hive per eseguire query su Apache HBase

È possibile eseguire query sui dati nelle tabelle HBase tramite [Apache Hive](https://hive.apache.org/). In questa sezione si crea una tabella Hive che esegue il mapping alla tabella HBase e la usa per la query dei dati nella tabella HBase.

1. Dalla connessione SSH aperta usare il comando seguente per avviare Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Per altre informazioni su Beeline, vedere [Usare Hive con Hadoop in HDInsight con Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Eseguire il seguente script [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) per creare una tabella Hive mappata alla tabella HBase. Prima di eseguire questa istruzione, assicurarsi di aver creato la tabella di esempio usata in precedenza come riferimento in questo articolo tramite la shell HBase.

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

1. Per chiudere la connessione SSH, usare `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>Usare le API REST HBase mediante Curl

L'API REST viene protetta tramite l' [autenticazione di base](https://en.wikipedia.org/wiki/Basic_access_authentication). Le richieste vengono sempre eseguite usando il protocollo HTTPS (Secure HTTP) per essere certi che le credenziali vengano inviate in modo sicuro al server.

1. Inizializzare una variabile di ambiente per semplicità d'uso. Modificare i comandi seguenti sostituendo `MYPASSWORD` con la password dell'account di accesso del cluster. Sostituire `MYCLUSTERNAME` con il nome del cluster HBase e quindi immettere i comandi.

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

1. Accedere all'interfaccia utente Web Ambari all'indirizzo `https://CLUSTERNAME.azurehdinsight.net`, dove `CLUSTERNAME` è il nome del cluster HBase.

1. Selezionare **HBase** dal menu a sinistra.

1. Selezionare **Collegamenti rapidi** nella parte superiore della pagina, scegliere il collegamento del nodo Zookeeper attivo e quindi selezionare **HBase Master UI** (Interfaccia utente HBase Master).  L'interfaccia utente viene aperta in un'altra scheda del browser:

   ![Interfaccia utente master HBase di HDInsight](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   L'interfaccia utente master HBase contiene le sezioni seguenti:

   - server di zona
   - master di backup
   - tables
   - attività
   - attributi di software

## <a name="clean-up-resources"></a>Pulire le risorse

Per evitare incoerenze, è consigliabile disabilitare le tabelle HBase prima di eliminare il cluster. È possibile usare il comando HBase `disable 'Contacts'`. Se non si intende continuare a usare questa applicazione, eliminare il cluster HBase creato con i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Nella casella **Ricerca** in alto digitare **HDInsight**.
1. Selezionare **Cluster HDInsight** in **Servizi**.
1. Nell'elenco di cluster HDInsight visualizzato, fare clic su **...** accanto al cluster creato per questa esercitazione.
1. Fare clic su **Elimina**. Fare clic su **Sì**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un cluster Apache HBase, come creare tabelle e come visualizzare i dati di tali tabelle dalla shell HBase. Si è inoltre appreso come usare una query Hive sui dati nelle tabelle HBase e come usare le API REST C# di HBase per creare una tabella HBase e recuperare i dati dalla tabella. Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Panoramica di HBase di HDInsight](./apache-hbase-overview.md)