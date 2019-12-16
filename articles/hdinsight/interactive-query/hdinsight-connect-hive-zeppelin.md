---
title: 'Guida introduttiva: Apache Hive in Azure HDInsight con Apache Zeppelin'
description: Questo argomento di avvio rapido illustra come usare Apache Zeppelin per eseguire query Apache Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 915aca0e95fce05f74477b526de047c829c7f512
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74890400"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Guida introduttiva: Eseguire query Apache Hive in Azure HDInsight con Apache Zeppelin

Questo argomento di avvio rapido illustra come usare Apache Zeppelin per eseguire query [Apache Hive](https://hive.apache.org/) in Azure HDInsight. I cluster Interactive Query HDInsight includono notebook [Apache Zeppelin](https://zeppelin.apache.org/) che è possibile usare per eseguire query Hive interattive.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Cluster HDInsight Interactive Query. Per creare un cluster HDInsight, vedere [Creare un cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).  Assicurarsi di scegliere il tipo di cluster **Interactive Query**.

## <a name="create-an-apache-zeppelin-note"></a>Creare una nota di Apache Zeppelin

1. Nell'URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin` seguente sostituire `CLUSTERNAME` con il nome del cluster. Immettere quindi l'URL in un Web browser.

2. Immettere il nome utente e la password dell'account di accesso del cluster. Dalla pagina di Zeppelin è possibile creare una nuova nota o aprire le note esistenti. **HiveSample** contiene alcune query Hive di esempio.  

    ![Zeppelin Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selezionare **Create new note** (Crea una nuova nota).

4. Nella finestra di dialogo **Create new note** (Crea una nuova nota) digitare o selezionare i valori seguenti:

    - Nome della nota: immettere un nome per la nota.
    - Interprete predefinito: selezionare **jdbc** dall'elenco a discesa.

5. Selezionare **Create Note** (Crea nota).

6. Immettere la query Hive seguente nella sezione del codice e quindi premere **MAIUSC+INVIO**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Zeppelin Interactive Query HDInsight esegue la query](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    L'istruzione **%jdbc(hive)** nella prima riga indica al notebook di usare l'interprete JDBC Hive.

    La query dovrebbe restituire una tabella Hive denominata **hivesampletable**.

    Di seguito sono illustrate altre due query Hive che è possibile eseguire su **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Rispetto a Hive tradizionale, i risultati della query vengono restituiti più velocemente.

### <a name="additional-examples"></a>Esempi aggiuntivi

1. Creare una tabella. Eseguire il codice seguente nel notebook Zeppelin:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Caricare i dati nella nuova tabella. Eseguire il codice seguente nel notebook Zeppelin:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Inserire un singolo record. Eseguire il codice seguente nel notebook Zeppelin:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Per una sintassi aggiuntiva, vedere il [manuale del linguaggio Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'argomento di avvio rapido, può essere opportuno eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non è in uso. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, eliminare i cluster quando non vengono usati è una scelta economicamente conveniente.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

Questo argomento di avvio rapido ha illustrato come usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight. Per altre informazioni sulle query Hive, l'articolo successivo illustrerà come eseguire query con Visual Studio.

> [!div class="nextstepaction"]
> [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
