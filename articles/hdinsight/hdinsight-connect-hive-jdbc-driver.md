---
title: Usare JDBC per effettuare una query di Hive in Azure HDInsight
description: "Informazioni su come usare JDBC per connettersi a Hive in Azure HDInsight ed eseguire query in modalità remota sui dati archiviati nel cloud."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 50a9c3929a4d3194c3786a3d4f6cdd1b73fb5867
ms.openlocfilehash: 41c3e20dc059ecc344cba6ca09ec04892e202d63


---
# <a name="connect-to-hive-on-azure-hdinsight-using-the-hive-jdbc-driver"></a>Connettersi a Hive in Azure HDInsight con il driver Hive JDBC

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

In questo documento si apprenderà come usare JDBC da un'applicazione Java per inviare in modalità remota query Hive a un cluster HDInsight. Verrà spiegato come eseguire la connessione dal client SQuirreL SQL e come connettersi a livello di codice da Java.

Per altre informazioni sull'interfaccia Hive JDBC, vedere [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop in HDInsight. Andranno bene i cluster basati su Linux o basati su Windows.

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL è un'applicazione client JDBC.

Per compilare ed eseguire l'applicazione di esempio Java collegata da questo articolo, è necessario quanto segue.

* [Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o successiva.
* [Apache Maven](https://maven.apache.org). Maven è un sistema di compilazione per progetti Java, usato dal progetto associato a questo articolo.

## <a name="connection-string"></a>Stringa di connessione

Le connessioni JDBC a un cluster HDInsight in Azure vengono stabilite tramite la porta 443 e il traffico viene protetto usando SSL. Il gateway pubblico dietro cui si trovano i cluster reindirizza il traffico alla porta su cui HiveServer2 è attualmente in ascolto. Un tipica stringa di connessione sarà quindi come la seguente:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

## <a name="authentication"></a>Autenticazione

Quando si stabilisce la connessione, è necessario usare il nome amministratore e la password del cluster HDInsight per l'autenticazione nel gateway cluster. Durante la connessione dai client JDBC come SQuirreL SQL, è necessario immettere il nome amministratore e la password nelle impostazioni del client.

Da un'applicazione Java è necessario usare il nome e la password quando viene stabilita una connessione. Ad esempio, il codice Java seguente apre una nuova connessione usando la stringa di connessione, il nome amministratore e la password:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Connettersi con il client SQuirreL SQL

SQuirreL SQL è un client JDBC che può essere usato per eseguire in modalità remota query Hive con il cluster HDInsight. La procedura seguente presuppone che SQL SQuirreL sia già stato installato e fornirà indicazioni dettagliate per il download e la configurazione dei driver per Hive.

1. Copiare i driver JDBC Hive dal cluster HDInsight.
   
    * Per **HDInsight basato su Linux**, seguire questa procedura per scaricare i file JAR necessari.
     
        1. Creare una nuova directory contenente i file, ad esempio `mkdir hivedriver`.
        2. Al prompt dei comandi, Bash, PowerShell o un altro prompt della riga di comando, passare alla nuova directory e usare i comandi seguenti per copiare i file dal cluster HDInsight.
        
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
        
            Sostituire **USERNAME** con il nome dell'account utente SSH per il cluster. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.
        
        > [!NOTE]
        > Negli ambienti Windows il comando `scp` non è sempre disponibile. In questo caso usare l'utilità PSCP. È disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * Per **HDInsight basato su Windows**, seguire questa procedura per scaricare i file JAR.
     
        1. Nel portale di Azure selezionare il cluster HDInsight e quindi selezionare l'icona **Desktop remoto**.
        
            ![Icona Desktop remoto](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. Nel pannello Desktop remoto fare clic sul pulsante **Connetti** per connettersi al cluster. Se Desktop remoto non è abilitato, usare il modulo per specificare un nome utente e una password, quindi selezionare **Abilita** per abilitare Desktop remoto per il cluster.
        
            ![Pannello Desktop remoto](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)
        
            Dopo aver selezionato **Connetti**verrà scaricato un file con estensione rdp. Usare questo file per avviare il client Desktop remoto. Quando richiesto, usare il nome utente e la password immessi per Accesso desktop remoto.

        3. Una volta stabilita la connessione, copiare i file seguenti dalla sessione Desktop remoto nel computer locale. Inserirli in una directory locale denominata `hivedriver`.
        
            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar
          
            > [!NOTE]
            > I numeri di versione inclusi nei percorsi e i nomi file potrebbero essere diversi per il cluster in uso.

        4. Disconnettere la sessione di Desktop remoto al termine della copia dei file.

2. Avviare l'applicazione SQuirreL SQL. Nella parte sinistra della finestra selezionare **Drivers** (Driver).
   
    ![Scheda Drivers sul lato sinistro della finestra](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

3. Nella parte superiore della finestra di dialogo **Drivers** (Driver) selezionare l'icona **+** per creare un nuovo driver.
   
    ![Icone Drivers](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

4. Nella finestra di dialogo Add Driver aggiungere le informazioni seguenti.
   
    * **Name** (Nome): Hive
    * **Example URL** (URL di esempio): `jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2`
    * **Extra Class Path** (Percorso classe extra): usare il pulsante Add (Aggiungi) per aggiungere i file JAR scaricati in precedenza
    * **Class Name** (Nome classe): org.apache.hive.jdbc.HiveDriver
    
   ![finestra di dialogo add driver](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)
    
   Fare clic su **OK** per salvare le impostazioni.

5. Nella parte sinistra della finestra di SQL SQuirreL selezionare **Aliases** (Alias). Fare quindi clic sull'icona **+** per creare un nuovo alias di connessione.
   
    ![aggiungere un nuovo alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

6. Usare i valori seguenti per la finestra di dialogo **Add Alias** (Aggiungi alias).
   
    * **Name** (Nome): Hive on HDInsight

    * **Driver**: usare l'elenco a discesa per selezionare il driver **Hive**

    * **URL**: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
     
        Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

    * **User Name** (Nome utente): nome dell'account di accesso per il cluster HDInsight. Il valore predefinito è `admin`.

    * **Password**: password per l'account di accesso del cluster. Questa password viene fornita durante la creazione del cluster HDInsight.
     
    ![finestra di dialogo add alias](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)
     
    Usare il pulsante **Test** per verificare il funzionamento della connessione. Quando viene visualizzata la finestra di dialogo **Connect to: Hive on HDInsight** (Connetti a: Hive in HDInsight), selezionare **Connect** (Connetti) per eseguire il test. Se il test ha esito positivo, verrà visualizzata la finestra di dialogo **Connection successful** (Connessione riuscita).
     
    Usare il pulsante **OK** nella parte inferiore della finestra di dialogo **Add Alias** (Aggiungi alias) per salvare l'alias di connessione.

7. Nell'elenco a discesa **Connect to** (Connetti a) nella parte superiore di SQL SQuirreL selezionare **Hive on HDInsight** (Hive in HDInsight). Quando richiesto, selezionare **Connect** (Connetti).
   
    ![finestra di dialogo di connessione](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

8. Dopo avere stabilito la connessione, immettere la query seguente nella finestra di dialogo di query SQL e quindi selezionare l'icona **Run** (Esegui). Nell'area dei risultati dovrebbero comparire i risultati della query.
   
        select * from hivesampletable limit 10;
   
    ![finestra della query sql, con i risultati](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Connettersi da un'applicazione Java di esempio

Un esempio dell'uso di un client Java per eseguire una query Hive in HDInsight è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Seguire le istruzioni del repository per compilare ed eseguire l'esempio.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Errore imprevisto durante il tentativo di aprire una connessione SQL

**Sintomi**: quando ci si connette a un cluster HDInsight versione 3.3 o 3.4, un errore potrebbe segnalare che si è verificato un errore imprevisto. L'analisi dello stack dell'errore inizia con le righe seguenti:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: questo errore è causato da una mancata corrispondenza tra la versione del file commons-codec.jar usato da SQuirreL e la versione necessaria per i componenti Hive JDBC scaricati dal cluster HDInsight.

**Soluzione**: per risolvere l'errore eseguire questa procedura.

1. Scaricare il file commons-codec.jar dal cluster HDInsight.
   
        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Uscire da SQuirreL e passare alla directory in cui è installato SQuirreL nel sistema. Nella directory `lib` della directory di SquirreL sostituire il file commons-codec.jar esistente con quello scaricato dal cluster HDInsight.

3. Riavviare SQuirreL. L'errore non dovrebbe più verificarsi quando ci si connette a Hive in HDInsight.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come usare JDBC con Hive, vedere i collegamenti seguenti per scoprire altre modalità di utilizzo di Azure HDInsight.

* [Caricare dati in HDInsight](hdinsight-upload-data.md)
* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Feb17_HO2-->


