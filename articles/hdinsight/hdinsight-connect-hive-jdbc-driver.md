<properties
 pageTitle="Usare JDBC per effettuare una query di Hive in Azure HDInsight"
 description="Informazioni su come usare JDBC per connettersi a Hive in Azure HDInsight ed eseguire query in modalità remota sui dati archiviati nel cloud."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="02/01/2016"
 ms.author="larryfr"/>

#Connettersi a Hive in Azure HDInsight con il driver Hive JDBC

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

In questo documento si apprenderà come usare JDBC da un'applicazione Java per inviare in modalità remota query Hive a un cluster HDInsight. Per altre informazioni sull'interfaccia Hive JDBC, vedere [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop in HDInsight. Andranno bene i cluster basati su Linux o basati su Windows.

* [Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o successiva.

* [Apache Maven](https://maven.apache.org). Maven è un sistema di compilazione per progetti Java, usato dal progetto associato a questo articolo.

##Stringa di connessione

Le connessioni JDBC a un cluster HDInsight in Azure vengono stabilite tramite la porta 443 e il traffico viene protetto usando SSL. Il gateway pubblico dietro cui si trovano i cluster reindirizza il traffico alla porta su cui HiveServer2 è attualmente in ascolto. Un tipica stringa di connessione sarà quindi come la seguente:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

##Autenticazione

Quando si stabilisce la connessione, è necessario specificare il nome amministratore e la password del cluster HDInsight, che autenticano le richieste al gateway. Ad esempio, il codice Java seguente apre una nuova connessione usando la stringa di connessione, il nome amministratore e la password:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Query

Una volta stabilita la connessione, è possibile eseguire query su Hive. Ad esempio, il codice Java seguente esegue un'operazione __SELECT__ da una tabella, limitando i risultati solo a tre righe, quindi visualizza i risultati:

    sql = "SELECT querytime, market, deviceplatform, devicemodel, state, country from " + tableName + " LIMIT 3";
    stmt2 = conn.createStatement();
    System.out.println("\nRetrieving inserted data:");

    res2 = stmt2.executeQuery(sql);

    while (res2.next()) {
      System.out.println( res2.getString(1) + "\t" + res2.getString(2) + "\t" + res2.getString(3) + "\t" + res2.getString(4) + "\t" + res2.getString(5) + "\t" + res2.getString(6));
    }

##Progetto Java di esempio

Un esempio dell'uso di un client Java per eseguire una query Hive in HDInsight è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Seguire le istruzioni del repository per compilare ed eseguire l'esempio.

##Passaggi successivi

Dopo avere appreso come usare JDBC con Hive, vedere i collegamenti seguenti per scoprire altre modalità di utilizzo di Azure HDInsight.

* [Caricare dati in HDInsight](hdinsight-upload-data.md)
* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0204_2016-->