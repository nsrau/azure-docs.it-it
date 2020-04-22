---
title: Eseguire una query Apache Hive tramite il driver JDBC - Azure HDInsight
description: Usare il driver JDBC da un'applicazione Java per inviare query Apache Hive a Hadoop in HDInsight. Connettersi a livello di codice e dal client SQuirrel SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: 803256ab1c5201534cfbd8210f96040ba75081e5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687304"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Eseguire una query Apache Hive tramite il driver JDBC in HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Informazioni su come utilizzare il driver JDBC da un'applicazione Java. To submit Apache Hive queries to Apache Hadoop in Azure HDInsight. Le informazioni contenute in questo documento illustrano come connettersi a livello di codice e dal client SQL SQuirreL.

Per altre informazioni sull'interfaccia Hive JDBC, vedere [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster in HDInsight Hadoop. Per crearne uno, vedere l'[introduzione ad Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Verificare che il servizio HiveServer2 sia in esecuzione.
* [Java Developer Kit (JDK) versione 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) o successiva.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL è un'applicazione client JDBC.

## <a name="jdbc-connection-string"></a>Stringa di connessione JDBC

Le connessioni JDBC a un cluster HDInsight in Azure vengono effettuate tramite la porta 443.JDBC connections to an HDInsight cluster on Azure are made over port 443. Il traffico è protetto utilizzando TLS/SSL. Il gateway pubblico dietro cui si trovano i cluster reindirizza il traffico alla porta su cui HiveServer2 è attualmente in ascolto. La stringa di connessione seguente mostra il formato da usare per HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.

In alternativa, è possibile ottenere la connessione tramite **Ambari UI > Hive > Configs > Advanced**.

![Ottenere la stringa di connessione JDBC tramite AmbariGet JDBC connection string through Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Nome host nella stringa di connessioneHost name in connection string

Il nome host 'CLUSTERNAME.azurehdinsight.net' nella stringa di connessione è lo stesso dell'URL del cluster. È possibile ottenerlo tramite il portale di Azure.You can get it through Azure portal.

### <a name="port-in-connection-string"></a>Porta nella stringa di connessione

È possibile usare la porta 443 solo per connettersi al cluster da alcune posizioni esterne alla rete virtuale di Azure.You can only use **port 443** to connect to the cluster from some places outside of the Azure virtual network. HDInsight è un servizio gestito, ovvero tutte le connessioni al cluster vengono gestite tramite un gateway sicuro. Non è possibile connettersi a HiveServer 2 direttamente sulle porte 10001 o 10000.You can't connect to HiveServer 2 directly on ports 10001 or 10000. Queste porte non sono esposte all'esterno.

## <a name="authentication"></a>Authentication

Quando si stabilisce la connessione, usare il nome e la password dell'amministratore del cluster HDInsight per l'autenticazione. Dai client JDBC, ad esempio SQuirreL SQL, immettere il nome amministratore e la password nelle impostazioni client.

Da un'applicazione Java è necessario usare il nome e la password quando viene stabilita una connessione. Ad esempio, il seguente codice Java apre una nuova connessione:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Connettersi con il client SQuirreL SQL

SQuirreL SQL è un client JDBC che può essere usato per eseguire in modalità remota query Hive con il cluster HDInsight. I passaggi seguenti presuppongono che sia già stato installato SQuirreL SQL.

1. Creare una directory contenente determinati file da copiare dal cluster.

2. Nello script seguente `sshuser` sostituire con il nome dell'account utente SSH per il cluster.  Sostituire `CLUSTERNAME` con il nome del cluster HDInsight.  Da una riga di comando, modificare la directory di lavoro a quella creata nel passaggio precedente e quindi immettere il comando seguente per copiare i file da un cluster HDInsight:From a command line, change your work directory to the one created in the prior step, and then enter the following command to copy files from an HDInsight cluster:

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
    |Percorso di classe aggiuntivoExtra Class Path|Utilizzare il pulsante **Aggiungi** per aggiungere tutti i file jar scaricati in precedenza.|
    |Nome della classe|Org.apache.hive.jdbc.HiveDriver|

   ![Finestra di dialogo Aggiungi driver con parametri](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Selezionare **OK** per salvare queste impostazioni.

6. Nella parte sinistra della finestra di SQL SQuirreL selezionare **Aliases** (Alias). Selezionare **+** quindi l'icona per creare un alias di connessione.

    !['SQuirreL SQL aggiungi nuova finestra di dialogo alias'](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Utilizzare i seguenti valori per la finestra di dialogo **Aggiungi alias:**

    |Proprietà |valore |
    |---|---|
    |Nome|Hive in HDInsight|
    |Driver|Utilizzare l'elenco a discesa per selezionare il driver **Hive.**|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.|
    |Nome utente|nome dell'account di accesso per il cluster HDInsight. L'impostazione predefinita è **admin**.|
    |Password|password per l'account di accesso del cluster.|

    ![Finestra di dialogo Aggiungi alias con parametri](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Usare il pulsante **Test** per verificare il funzionamento della connessione. Quando viene visualizzata la finestra di dialogo **Connect to: Hive on HDInsight** (Connetti a: Hive in HDInsight), selezionare **Connect** (Connetti) per eseguire il test. Se il test ha esito positivo, verrà visualizzata la finestra di dialogo **Connection successful** (Connessione riuscita). Se si verifica un errore, vedere [Risoluzione dei problemi](#troubleshooting).

    Usare il pulsante **OK** nella parte inferiore della finestra di dialogo **Add Alias** (Aggiungi alias) per salvare l'alias di connessione.

8. Nell'elenco a discesa **Connect to** (Connetti a) nella parte superiore di SQL SQuirreL selezionare **Hive on HDInsight** (Hive in HDInsight). Quando richiesto, selezionare **Connect** (Connetti).

    ![finestra di dialogo di connessione con parametri](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. Una volta stabilita la connessione, immettere la query seguente nella finestra di dialogo della query SQL, quindi selezionare l'icona **Esegui** (una persona in esecuzione). Nell'area dei risultati dovrebbero comparire i risultati della query.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![finestra della query sql, con i risultati](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Connettersi da un'applicazione Java di esempio

Un esempio di utilizzo di un client Java per [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc)eseguire una query Hive su HDInsight è disponibile all'indirizzo . Seguire le istruzioni del repository per compilare ed eseguire l'esempio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Errore imprevisto durante il tentativo di aprire una connessione SQL

**Sintomi**: quando ci si connette a un cluster HDInsight versione 3.3 o superiore, un errore potrebbe segnalare che si è verificato un errore imprevisto. L'analisi dello stack dell'errore inizia con le righe seguenti:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: questo errore è causato da una versione precedente del file commons-codec.jar incluso con SQuirreL.

**Soluzione**: per correggere questo errore, attenersi alla seguente procedura:

1. Uscire da SQuirreL, quindi passare alla directory in cui è installato `C:\Program Files\squirrel-sql-4.0.0\lib`SQuirreL nel sistema, ad esempio . Nella directory `lib` della directory di SquirreL sostituire il file commons-codec.jar esistente con quello scaricato dal cluster HDInsight.

1. Riavviare SQuirreL. L'errore non dovrebbe più verificarsi quando ci si connette a Hive in HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Connessione disconnessa da HDInsightConnection disconnected by HDInsight

**Sintomi:** quando si tenta di scaricare un'enorme quantità di dati (ad esempio diversi GB) tramite JDBC/ODBC, la connessione viene disconnessa da HDInsight in modo imprevisto durante il download.

**Causa**: questo errore è causato dalla limitazione sui nodi gateway. Quando si ottengono dati da JDBC/ODBC, tutti i dati devono passare attraverso il nodo Gateway. Tuttavia, un gateway non è progettato per scaricare una quantità enorme di dati, pertanto il gateway potrebbe chiudere la connessione se non è in grado di gestire il traffico.

**Risoluzione**: evitare di utilizzare il driver JDBC/ODBC per scaricare enormi quantità di dati. Copiare invece i dati direttamente dall'archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a usare JDBC per usare Hive, usare i collegamenti seguenti per esplorare altri modi per usare Azure HDInsight.Now that you've learned how to use JDBC to work with Hive, use the following links to explore other ways to work with Azure HDInsight.

* [Visualizzare i dati di Apache Hive con Microsoft Power BI in Azure HDInsight.](apache-hadoop-connect-hive-power-bi.md)
* Visualizzare i dati di [Interactive Query Hive con Power BI in Azure HDInsight.](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel ad Apache Hadoop utilizzando Power Query](apache-hadoop-connect-excel-power-query.md).
* [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](../use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)
