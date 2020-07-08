---
title: Eseguire una query Apache Hive tramite il driver JDBC - Azure HDInsight
description: Usare il driver JDBC da un'applicazione Java per inviare query Apache Hive a Hadoop in HDInsight. Connettersi a livello di codice e dal client SQuirrel SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 258dfec20644ee29368de075673dfc7798bee28a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86083543"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Eseguire una query Apache Hive tramite il driver JDBC in HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Informazioni su come usare il driver JDBC da un'applicazione Java. Per inviare Apache Hive query Apache Hadoop in Azure HDInsight. Le informazioni in questo documento illustrano come connettersi a livello di codice e dal client SQuirreL SQL.

Per altre informazioni sull'interfaccia Hive JDBC, vedere [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster in HDInsight Hadoop. Per crearne uno, vedere l'[introduzione ad Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Verificare che il servizio HiveServer2 sia in esecuzione.
* [Java Developer Kit (JDK) versione 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) o successiva.
* [Scoiattolo SQL](http://squirrel-sql.sourceforge.net/). SQuirreL è un'applicazione client JDBC.

## <a name="jdbc-connection-string"></a>Stringa di connessione JDBC

Le connessioni JDBC a un cluster HDInsight in Azure vengono effettuate sulla porta 443. Il traffico viene protetto tramite TLS/SSL. Il gateway pubblico dietro cui si trovano i cluster reindirizza il traffico alla porta su cui HiveServer2 è attualmente in ascolto. La stringa di connessione seguente mostra il formato da usare per HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.

In alternativa, è possibile ottenere la connessione tramite l' **interfaccia utente di Ambari > configurazioni > Hive > avanzate**.

![Ottenere la stringa di connessione JDBC tramite Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Nome host nella stringa di connessione

Il nome host ' CLUSTERNAME.azurehdinsight.net ' nella stringa di connessione è uguale all'URL del cluster. È possibile ottenerlo tramite portale di Azure.

### <a name="port-in-connection-string"></a>Porta nella stringa di connessione

È possibile usare solo la **porta 443** per connettersi al cluster da alcune posizioni esterne alla rete virtuale di Azure. HDInsight è un servizio gestito che indica che tutte le connessioni al cluster vengono gestite tramite un gateway sicuro. Non è possibile connettersi direttamente a del 2 sulle porte 10001 o 10000. Queste porte non sono esposte all'esterno.

## <a name="authentication"></a>Authentication

Quando si stabilisce la connessione, usare il nome e la password dell'amministratore del cluster HDInsight per l'autenticazione. Da client JDBC, ad esempio SQuirreL SQL, immettere il nome dell'amministratore e la password nelle impostazioni client.

Da un'applicazione Java è necessario usare il nome e la password quando viene stabilita una connessione. Ad esempio, il codice Java seguente apre una nuova connessione:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Connettersi con il client SQuirreL SQL

SQuirreL SQL è un client JDBC che può essere usato per eseguire in modalità remota query Hive con il cluster HDInsight. I passaggi seguenti presuppongono che sia già stato installato SQuirreL SQL.

1. Creare una directory che contenga determinati file da copiare dal cluster.

2. Nello script seguente sostituire `sshuser` con il nome dell'account utente SSH per il cluster.  Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.  Dalla riga di comando modificare la directory di lavoro in quella creata nel passaggio precedente e quindi immettere il comando seguente per copiare i file da un cluster HDInsight:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Avviare l'applicazione SQuirreL SQL. Nella parte sinistra della finestra selezionare **Drivers** (Driver).

    ![Scheda Drivers sul lato sinistro della finestra](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Nella parte superiore della finestra di dialogo **Drivers** (Driver) selezionare l'icona **+** per creare un driver.

    ![Icona dei driver dell'applicazione SQL SQuirreL](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. Nella finestra di dialogo di aggiunta del driver aggiungere le informazioni seguenti:

    |Proprietà | valore |
    |---|---|
    |Nome|Hive|
    |URL di esempio|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Percorso di classe aggiuntivo|Usare il pulsante **Aggiungi** per aggiungere tutti i file jar scaricati in precedenza.|
    |Nome della classe|org. Apache. hive. JDBC. HiveDriver|

   ![finestra di dialogo Aggiungi driver con parametri](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selezionare **OK** per salvare le impostazioni.

6. Nella parte sinistra della finestra di SQL SQuirreL selezionare **Aliases** (Alias). Quindi selezionare l' **+** icona per creare un alias di connessione.

    ![Finestra di dialogo ' scoiattolo SQL Aggiungi nuovo alias '](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Usare i valori seguenti per la finestra di dialogo **Aggiungi alias** :

    |Proprietà |valore |
    |---|---|
    |Nome|Hive in HDInsight|
    |Driver|Usare l'elenco a discesa per selezionare il driver **hive** .|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.|
    |Nome utente|nome dell'account di accesso per il cluster HDInsight. Il valore predefinito è **admin**.|
    |Password|password per l'account di accesso del cluster.|

    ![finestra di dialogo Aggiungi alias con parametri](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Usare il pulsante **Test** per verificare il funzionamento della connessione. Quando viene visualizzata la finestra di dialogo **Connect to: Hive on HDInsight** (Connetti a: Hive in HDInsight), selezionare **Connect** (Connetti) per eseguire il test. Se il test ha esito positivo, verrà visualizzata la finestra di dialogo **Connection successful** (Connessione riuscita). Se si verifica un errore, vedere [Risoluzione dei problemi](#troubleshooting).

    Usare il pulsante **OK** nella parte inferiore della finestra di dialogo **Add Alias** (Aggiungi alias) per salvare l'alias di connessione.

8. Nell'elenco a discesa **Connect to** (Connetti a) nella parte superiore di SQL SQuirreL selezionare **Hive on HDInsight** (Hive in HDInsight). Quando richiesto, selezionare **Connect** (Connetti).

    ![finestra di dialogo di connessione con parametri](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Una volta stabilita la connessione, immettere la query seguente nella finestra di dialogo query SQL e quindi selezionare l'icona **Esegui** (persona in esecuzione). Nell'area dei risultati dovrebbero comparire i risultati della query.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![finestra della query sql, con i risultati](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Connettersi da un'applicazione Java di esempio

Un esempio di uso di un client Java per eseguire query su hive in HDInsight è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Seguire le istruzioni del repository per compilare ed eseguire l'esempio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Errore imprevisto durante il tentativo di aprire una connessione SQL

**Sintomi**: quando ci si connette a un cluster HDInsight versione 3.3 o superiore, un errore potrebbe segnalare che si è verificato un errore imprevisto. L'analisi dello stack dell'errore inizia con le righe seguenti:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: questo errore è causato da una versione precedente del file commons-codec.jar incluso con SQuirreL.

**Soluzione**: per correggere l'errore, attenersi alla procedura seguente:

1. Uscire da SQuirreL, quindi passare alla directory in cui è installato SQuirreL nel sistema, ad esempio `C:\Program Files\squirrel-sql-4.0.0\lib` . Nella directory `lib` della directory di SquirreL sostituire il file commons-codec.jar esistente con quello scaricato dal cluster HDInsight.

1. Riavviare SQuirreL. L'errore non dovrebbe più verificarsi quando ci si connette a Hive in HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Connessione disconnessa da HDInsight

**Sintomi**: quando si tenta di scaricare una grande quantità di dati (ad eccezione di diversi GB) tramite JDBC/ODBC, la connessione viene disconnessa da HDInsight in modo imprevisto durante il download.

**Causa**: questo errore è causato dalla limitazione dei nodi del gateway. Quando si recuperano dati da JDBC/ODBC, tutti i dati devono passare attraverso il nodo del gateway. Tuttavia, un gateway non è progettato per scaricare una grande quantità di dati, quindi il gateway potrebbe chiudere la connessione se non è in grado di gestire il traffico.

**Soluzione**: evitare di utilizzare il driver JDBC/ODBC per scaricare grandi quantità di dati. Copiare i dati direttamente dall'archivio BLOB.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare JDBC per lavorare con hive, usare i collegamenti seguenti per esplorare altri modi per lavorare con Azure HDInsight.

* [Visualizzare i dati di Apache hive con Microsoft Power bi in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizza i dati di hive Interactive query con Power bi in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel a Apache Hadoop tramite Power query](apache-hadoop-connect-excel-power-query.md).
* [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](../use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
