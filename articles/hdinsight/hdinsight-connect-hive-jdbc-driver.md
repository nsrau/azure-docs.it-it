<properties
 pageTitle="Usare JDBC per effettuare una query di Hive in Azure HDInsight"
 description="Informazioni su come usare JDBC per connettersi a Hive in Azure HDInsight ed eseguire query in modalità remota sui dati archiviati nel cloud."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="java"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/23/2016"
 ms.author="larryfr"/>

#Connettersi a Hive in Azure HDInsight con il driver Hive JDBC

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

In questo documento si apprenderà come usare JDBC da un'applicazione Java per inviare in modalità remota query Hive a un cluster HDInsight. Verrà spiegato come eseguire la connessione dal client SQuirreL SQL e come connettersi a livello di codice da Java.

Per altre informazioni sull'interfaccia Hive JDBC, vedere [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

##Prerequisiti

Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster Hadoop in HDInsight. Andranno bene i cluster basati su Linux o basati su Windows.

* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL è un'applicazione client JDBC.

Per compilare ed eseguire l'applicazione di esempio Java collegata da questo articolo, è necessario quanto segue.

* [Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o successiva.

* [Apache Maven](https://maven.apache.org). Maven è un sistema di compilazione per progetti Java, usato dal progetto associato a questo articolo.

##Stringa di connessione

Le connessioni JDBC a un cluster HDInsight in Azure vengono stabilite tramite la porta 443 e il traffico viene protetto usando SSL. Il gateway pubblico dietro cui si trovano i cluster reindirizza il traffico alla porta su cui HiveServer2 è attualmente in ascolto. Un tipica stringa di connessione sarà quindi come la seguente:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

##Autenticazione

Quando si stabilisce la connessione, è necessario usare il nome amministratore e la password del cluster HDInsight per l'autenticazione nel gateway cluster. Durante la connessione dai client JDBC come SQuirreL SQL, è necessario immettere il nome amministratore e la password nelle impostazioni del client.

Da un'applicazione Java è necessario usare il nome e la password quando viene stabilita una connessione. Ad esempio, il codice Java seguente apre una nuova connessione usando la stringa di connessione, il nome amministratore e la password:

    DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);

##Connettersi con il client SQuirreL SQL

SQuirreL SQL è un client JDBC che può essere usato per eseguire in modalità remota query Hive con il cluster HDInsight. La procedura seguente presuppone che SQL SQuirreL sia già stato installato e fornirà indicazioni dettagliate per il download e la configurazione dei driver per Hive.

1. Copiare i driver JDBC Hive dal cluster HDInsight.

    * Per __HDInsight basato su Linux__, seguire questa procedura per scaricare i file JAR necessari.

        1. Creare una nuova directory contenente i file, ad esempio `mkdir hivedriver`.

        2. Al prompt dei comandi, Bash, PowerShell o un altro prompt della riga di comando, passare alla nuova directory e usare i comandi seguenti per copiare i file dal cluster HDInsight.

                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-jdbc*standalone.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
                scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .

            Sostituire __USERNAME__ con il nome dell'account utente SSH per il cluster. Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

            > [AZURE.NOTE] Negli ambienti Windows è necessario usare l'utilità PSCP anziché scp. È disponibile per il download all'indirizzo [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * Per __HDInsight basato su Windows__, seguire questa procedura per scaricare i file JAR.

        1. Nel portale di Azure selezionare il cluster HDInsight e quindi selezionare l'icona __Desktop remoto__.

            ![Icona Desktop remoto](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. Nel pannello Desktop remoto fare clic sul pulsante __Connetti__ per connettersi al cluster. Se Desktop remoto non è abilitato, usare il modulo per specificare un nome utente e una password, quindi selezionare __Abilita__ per abilitare Desktop remoto per il cluster.

            ![Pannello Desktop remoto](./media/hdinsight-connect-hive-jdbc-driver/remotedesktopblade.png)

            Dopo aver selezionato __Connetti__ verrà scaricato un file con estensione rdp. Usare questo file per avviare il client Desktop remoto. Quando richiesto, usare il nome utente e la password immessi per Accesso desktop remoto.

        3. Una volta stabilita la connessione, copiare i file seguenti dalla sessione Desktop remoto nel computer locale. Inserirli in una directory locale denominata `hivedriver`.

            * C:\\apps\\dist\\hive-0.14.0.2.2.9.1-7\\lib\\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\\apps\\dist\\hadoop-2.6.0.2.2.9.1-7\\share\\hadoop\\common\\lib\\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [AZURE.NOTE] I numeri di versione inclusi nei percorsi e i nomi file potrebbero essere diversi per il cluster in uso.

        4. Disconnettere la sessione di Desktop remoto al termine della copia dei file.

3. Avviare l'applicazione SQuirreL SQL. Nella parte sinistra della finestra selezionare __Drivers__.

    ![Scheda Drivers sul lato sinistro della finestra](./media/hdinsight-connect-hive-jdbc-driver/squirreldrivers.png)

4. Nella parte superiore della finestra di dialogo __Drivers__ selezionare l'icona __+__ per creare un nuovo driver.

    ![Icone Drivers](./media/hdinsight-connect-hive-jdbc-driver/driversicons.png)

5. Nella finestra di dialogo Add Driver aggiungere le informazioni seguenti.

    * __Name__: Hive
    * __Example URL__: jdbc:hive2://localhost:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2
    * __Extra Class Path__: usare il pulsante Add per aggiungere i file JAR scaricati in precedenza
    * __Class Name__: org.apache.hive.jdbc.HiveDriver

    ![finestra di dialogo add driver](./media/hdinsight-connect-hive-jdbc-driver/adddriver.png)

    Fare clic su __OK__ per salvare le impostazioni.

6. Nella parte sinistra della finestra di SQL SQuirreL selezionare __Aliases__. Fare quindi clic sull'icona __+__ per creare un nuovo alias di connessione.

    ![aggiungere un nuovo alias](./media/hdinsight-connect-hive-jdbc-driver/aliases.png)

7. Usare i valori seguenti per la finestra di dialogo __Add Alias__.

    * __Name__: Hive on HDInsight
    * __Driver__: usare l'elenco a discesa per selezionare il driver __Hive__
    * __URL__: jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=/hive2

        Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

    * __User Name__: nome dell'account di accesso per il cluster HDInsight. Il valore predefinito è `admin`.
    * __Password__: password per l'account di accesso del cluster. Questa password viene fornita durante la creazione del cluster HDInsight.

    ![finestra di dialogo add alias](./media/hdinsight-connect-hive-jdbc-driver/addalias.png)

    Usare il pulsante __Test__ per verificare il funzionamento della connessione. Quando viene visualizzata la finestra di dialogo __Connect to: Hive on HDInsight__, selezionare __Connect__ per eseguire il test. Se il test ha esito positivo, verrà visualizzata la finestra di dialogo __Connection successful__.

    Usare il pulsante __OK__ nella parte inferiore della finestra di dialogo __Add Alias__ per salvare l'alias di connessione.

8. Nell'elenco a discesa __Connect to__ nella parte superiore di SQL SQuirreL selezionare __Hive on HDInsight__. Quando richiesto, selezionare __Connect__.

    ![finestra di dialogo di connessione](./media/hdinsight-connect-hive-jdbc-driver/connect.png)

9. Dopo avere stabilito la connessione, immettere la query seguente nella finestra di dialogo di query SQL e quindi selezionare l'icona __Run__. Nell'area dei risultati dovrebbero comparire i risultati della query.

        select * from hivesampletable limit 10;

    ![finestra della query sql, con i risultati](./media/hdinsight-connect-hive-jdbc-driver/sqlquery.png)

##Connettersi da un'applicazione Java di esempio

Un esempio dell'uso di un client Java per eseguire una query Hive in HDInsight è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Seguire le istruzioni del repository per compilare ed eseguire l'esempio.

##Risoluzione dei problemi

### Errore imprevisto durante il tentativo di aprire una connessione SQL.

__Sintomi__: quando ci si connette a un cluster HDInsight versione 3.3 o 3.4, un errore potrebbe segnalare che si è verificato un errore imprevisto. L'analisi dello stack dell'errore inizia con le righe seguenti:

    java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
    at java.util.concurrent.FutureTas...(FutureTask.java:122)
    at java.util.concurrent.FutureTask.get(FutureTask.java:206)

__Causa__: questo errore è causato da una mancata corrispondenza tra la versione del file commons-codec.jar utilizzato da SQuirreL e la versione necessaria per i componenti Hive JDBC scaricati dal cluster HDInsight.

__Soluzione__: per risolvere l'errore eseguire i passaggi seguenti.

1. Scaricare il file commons-codec.jar dal cluster HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Uscire da SQuirreL e passare alla directory in cui è installato SQuirreL nel sistema. Nella directory `lib` della directory di SquirreL sostituire il file commons-codec.jar esistente con quello scaricato dal cluster HDInsight.

3. Riavviare SQuirreL. L'errore non dovrebbe più verificarsi quando ci si connette a Hive in HDInsight.

##Passaggi successivi

Dopo avere appreso come usare JDBC con Hive, vedere i collegamenti seguenti per scoprire altre modalità di utilizzo di Azure HDInsight.

* [Caricare dati in HDInsight](hdinsight-upload-data.md)
* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare processi MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->