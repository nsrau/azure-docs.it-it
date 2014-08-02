<properties linkid="manage-services-hdinsight-sample-sqoop-import-export" urlDisplayName="HDInsight Samples" pageTitle="Sqoop Import-Export sample | Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="cgronlun" manager="paulettm" title="Sqoop Import-Export sample" />

Esempio di importazione/esportazione con Sqoop
==============================================

In questo argomento di esempio viene illustrato come utilizzare Apache Sqoop per importare dati da un database SQL in Azure a un cluster HDFS Hadoop su Azure.

Benché Hadoop rappresenti una scelta ottimale per l'elaborazione di dati non strutturati e semistrutturati, ad esempio log e file, potrebbe essere tuttavia necessario elaborare anche dati strutturati archiviati in database relazionali.

Sqoop è uno strumento progettato per il trasferimento di dati tra cluster Hadoop e database relazionali. Può essere utilizzato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. In questa esercitazione verrà utilizzato un database SQL per il database relazionale.

Sqoop è un prodotto software open source di Cloudera, Inc. Lo sviluppo software per Sqoop è passato nel 2011 da gitHub al sito [Apache Sqoop](http://sqoop.apache.org/).

In Azure HDInsight, Sqoop viene distribuito dalla shell dei comandi di Hadoop nel nodo head del cluster HDFS. È possibile utilizzare la funzionalità Desktop remoto disponibile in Hadoop nel portale di Azure per accedere al nodo head del cluster per questa distribuzione.

**Si apprenderà come:**

-   Utilizzare Azure PowerShell per eseguire un programma MapReduce su Azure HDInsight che analizza i dati contenuti in un file.

**Prerequisiti**: È necessario disporre di un account Azure e che il servizio HDInsight sia stato abilitato per la sottoscrizione. È necessario che Azure PowerShell e gli strumenti Powershell per Azure HDInsight siano stati installati e configurati per l'utilizzo con l'account. Per istruzioni specifiche, vedere [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)

Quando si configura il firewall su database SQL, sarà inoltre necessario l'indirizzo IP rivolto all'esterno per la posizione corrente. Per ottenerlo, passare al sito [WhatIsMyIP](http://www.whatismyip.com/) e prendere nota dell'indirizzo in uso. Più avanti nella procedura sarà inoltre necessario l'indirizzo IP rivolto all'esterno per il nodo head del cluster Hadoop. È possibile ottenere tale indirizzo IP visitando il sito indicato in precedenza.

**Descrizione generale** 

In questo argomento viene illustrato come eseguire l'esempio, viene presentato il codice Java per il programma MapReduce, vengono riepilogati i concetti appresi e vengono indicati alcuni passaggi successivi. L'argomento include le sezioni seguenti:

1.  [Configurazione di un database SQL](#set-up-sql)
2.  [Utilizzo di Sqoop da HDInsigh per importare dati in un cluster](#java-code)
3.  [Riepilogo](#summary)
4.  [Passaggi successivi](#next-steps)

Configurazione di un database SQL
---------------------------------

Da definire: schermate per questa sezione, questo è ancora l'approccio RDP.

**Per creare un database SQL**

1.  Accedere al portale di gestione di Azure.
2.  Fare clic su NEW nell'angolo inferiore sinistro, quindi su DATA SERVICES e infine su SQL DATABASE. Accedere all'account Azure. Per creare un server database, fare clic sull'icona **Database** nell'angolo inferiore sinistro della pagina.

3.  Nella pagina **Getting Started** fare clic sull'opzione **Create a new SQL Database Server**.

4.  Selezionare il tipo di sottoscrizione, ad esempio **Pay-As-You-Go**, associato all'account nella finestra **Create Server** e quindi fare clic su **Next**.

5.  Selezionare il valore appropriato per **Region** nella finestra **Create Server** e quindi fare clic su **Next**.

6.  Specificare il nome e la password per l'accesso e l'entità di sicurezza a livello di server del server di database SQL e quindi fare clic su **Next**.

7.  Premere **Add** per specificare una regola del firewall che consenta alla posizione corrente di accedere al database SQL per caricare il database AdventureWorks. Il firewall concede l'accesso in base all'indirizzo IP di origine di ogni richiesta. Utilizzare l'indirizzo IP trovato nei passaggi preliminari della configurazione in questa esercitazione relativo ai valori da aggiungere. Specificare un nome di regola, come mostrato, ma ricordare di utilizzare il proprio indirizzo IP, non l'indirizzo IP utilizzato di seguito a scopo illustrativo. È inoltre necessario aggiungere l'indirizzo IP rivolto all'esterno del nodo head nel cluster Hadoop. Se lo si conosce già, aggiungerlo ora. Fare quindi clic sul pulsante **Finish**.

**Per importare AdventureWorks2012**

1.  Scaricare il database AdventureWorks2012 nel computer locale dal collegamento Recommended Downloads nel sito Adventure Works for SQL Database.

2.  Decomprimere il file, aprire un prompt dei comandi di amministratore e passare alla directory AdventureWorks nella cartella AdventureWorks2012ForSQLAzure.

3.  Eseguire CreateAdventureWorksForSQLAzure.cmd digitando quanto segue:

	CreateAdventureWorksForSQLAzure.cmd nomeserver nomeutente password

	Se, ad esempio, il nome del server di database SQL assegnato è b1gl33p, il nome utente dell'amministratore è "Fred" e la password è "Secret", sarà necessario digitare quanto segue:

	CreateAdventureWorksForSQLAzure.cmd b1gl33p.database.windows.net Fred@b1gl33p Secret

	Lo script crea il database, installa lo schema e popola il database con dati di esempio.

1.  Tornare alla pagina del portale **WindowsAzurePlatform**, fare clic sulla sottoscrizione a sinistra (**Pay-As-You-Go** nell'esempio seguente) e quindi selezionare il database (nell'esempio: wq6xlbyoq0). AventureWorks2012 dovrebbe essere incluso nella colonna **Database Name**. Selezionarlo e fare clic sull'icona **Manage** nella parte superiore della pagina.

2.  Quando richiesto, immettere le credenziali per il database SQL e fare clic su **Log on**.

3.  Verrà aperta l'interfaccia Web per il database Adventure Works su database SQL. Fare clic sull'icona **New Query** nella parte superiore della pagina per aprire l'editor di query.

4.  Poiché Sqoop aggiunge attualmente parentesi quadre al nome della tabella, sarà necessario aggiungere un sinonimo per supportare l'utilizzo di nomi composti da due parti per le tabelle SQL Server. A tale scopo, eseguire la query seguente:

	`CREATE SYNONYM [Sales.SalesOrderDetail] FOR Sales.SalesOrderDetail`

1.  Eseguire la query seguente ed esaminarne i risultati.

	`select top 200 * from [Sales.SalesOrderDetail] `

Utilizzo di Sqoop da HDInsigh per importare dati in un cluster
--------------------------------------------------------------

1.  Nella pagina Account scorrere verso il basso fino all'icona Open Ports nella sezione Your cluster e quindi fare clic sull'icona per aprire la porta del server ODBC nel nodo head del cluster.

2.  Tornare alla pagina Account, scorrere verso il basso fino alla sezione Your cluster e quindi fare clic sull'icona **Remote Desktop** per aprire il nodo head nel cluster. Da definire: aggiornamento per l'utilizzo con PS.

3.  Quando richiesto, selezionare **Open** per aprire il file con estensione rdp.

4.  Selezionare Connect nella finestra Remote Desktop Connection.

5.  Immettere le credenziali per il cluster Hadoop (non Hadoop sull'account Azure) nella finestra **Windows Security** e quindi fare clic su **OK**.

6.  Aprire Internet Explorer e passare al sito WhatIsMyIP per ottenere l'indirizzo IP rivolto all'esterno per il nodo head del cluster. Tornare alla pagina di gestione del database SQL e aggiungere una regola del firewall che consente al cluster Hadoop di accedere al database SQL. Il firewall concede l'accesso in base all'indirizzo IP di origine di ogni richiesta.

7.  Fare doppio clic sull'icona della shell dei comandi di Hadoop nell'angolo superiore sinistro del desktop per aprirla. Passare alla directory "c:\\Apps\\dist\\sqoop\\bin" ed eseguire il comando seguente:

    `sqoop import --connect "jdbc:sqlserver://[serverName].database.windows.net;username=[userName]@[serverName];password=[password];database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

    Ad esempio, per i valori seguenti: 
	* nome server: wq6xlbyoq0 
	* nome utente: HadoopOnAzureSqoopAdmin 
	* password: Pa\$\$w0rd

    Il comando sqoop è:

    `sqoop import --connect "jdbc:sqlserver://wq6xlbyoq0.database.windows.net;username=HadoopOnAzureSqoopAdmin@wq6xlbyoq0;password=Pa$$w0rd;;database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

8.  Tornare alla pagina **Accounts** di Hadoop nel portale di Azure e aprire la console interattiva. Eseguire il comando \#lsr dalla console JavaScript per elencare i file e le directory presenti nel cluster HDFS. Da definire: aggiornamento per GA.

9.  Eseguire il comando \#tail per visualizzare risultati selezionati dal file part-m-0000. `tail /user/RAdmin/data/SalesOrderDetail/part-m-00000`

Riepilogo
---------

In questa esercitazione è stato illustrato come trasferire dati tra un cluster Hadoop gestito tramite Azure HDInsight e un database relazionale utilizzando Apache Sqoop.

Passaggi successivi
-------------------

Per esercitazioni in cui vengono eseguiti altri esempi e che includono istruzioni per l'utilizzo di Pig, Hive e processi MapReduce in Azure HDInsight con Azure PowerShell, vedere gli argomenti seguenti:

-   [Esempio: Calcolo del Pi greco](/en-us/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Esempio: Conteggio parole](/en-us/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Esempio: streaming C\#](/en-us/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Esempio: GraySort da 10 GB](/en-us/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Esercitazione: Utilizzo di Pig](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Esercitazione: Utilizzo di Hive](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Esercitazione: Utilizzo di MapReduce](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Documentazione di Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

