<properties linkid="manage-services-hdinsight-howto-sdk" urlDisplayName="HDInsight SDK" pageTitle="How to use the HDInsight .NET libraries | Azure" metaKeywords="" description="Learn how to get the HDInsight NuGet packages and use them from your .NET application." metaCanonical="" services="hdinsight" documentationCenter="" title="Use the Hadoop .NET SDK with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Utilizzo di Hadoop .NET SDK con HDInsight
=========================================

Hadoop .NET SDK fornisce librerie client .NET che semplificano l'utilizzo di Hadoop da .NET. In questa esercitazione si apprenderà come ottenere Hadoop .NET SDK e come utilizzarlo per creare una semplice applicazione basata su .NET per l'esecuzione di query Hive tramite il servizio Azure HDInsight. Dato un file actors.txt esistente, verrà scritta un'applicazione per l'individuazione dell'attore o dell'attrice che ottiene il maggior numero di riconoscimenti.

Per abilitare HDInsight, fare clic [qui](https://account.windowsazure.com/PreviewFeatures).

Contenuto dell'articolo
-----------------------

-   [Download e installazione di Hadoop .NET SDK](#install)
-   [Preparazione dell'esercitazione](#prepare)
-   [Creazione dell'applicazione](#create)
-   [Esecuzione dell'applicazione](#run)
-   [Passaggi successivi](#nextsteps)

Download e installazione di Hadoop .NET SDK
-------------------------------------------

È possibile installare l'ultima build pubblicata dell'SDK da [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Nell'SDK sono inclusi i componenti seguenti:

-   **Libreria MapReduce**: semplifica la scrittura di processi MapReduce nei linguaggi .NET tramite l'interfaccia di flusso Hadoop.

-   **Libreria LINQ per client Hive**: converte query C\# o F\# LINQ in query HiveQL e le esegue nel cluster Hadoop. Questa libreria consente inoltre di eseguire query HiveQL arbitrarie da un'applicazione .NET.

-   **Libreria WebClient**: include le librerie client per *WebHDFS* e *WebHCat*.

    -   **Libreria client WebHDFS**: utilizza file in HDFS e nell'archivio BLOB di Azure.

    -   **Libreria client WebHCat**: gestisce la pianificazione e l'esecuzione di processi nel cluster HDInsight.

Di seguito è riportata la sintassi NuGet per l'installazione delle librerie:

    install-package Microsoft.Hadoop.MapReduce
    install-package Microsoft.Hadoop.Hive 
    install-package Microsoft.Hadoop.WebClient 

Tali comandi aggiungono le librerie e i riferimenti al progetto corrente di Visual Studio.

Preparazione dell'esercitazione
-------------------------------

Prima di continuare, è necessario disporre di una [sottoscrizione di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/) e di un [account di archiviazione di Azure](http://www.windowsazure.com/it-it/manage/services/storage/how-to-create-a-storage-account/). È inoltre necessario conoscere il nome e la chiave dell'account di archiviazione di Azure. Per istruzioni su come ottenere queste informazioni, vedere la sezione *Procedura: Visualizzare, copiare e rigenerare le chiavi di accesso alle risorse di archiviazione* di [Come gestire gli account di archiviazione](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

È inoltre necessario scaricare il file Actors.txt utilizzato in questa esercitazione. Per scaricare tale file nell'ambiente di sviluppo, eseguire la procedura seguente:

1.  Creare una cartella C:\\Tutorials nel computer locale.

2.  Scaricare [Actors.txt](http://www.microsoft.com/en-us/download/details.aspx?id=37003) e salvare il file nella cartella C:\\Tutorials.

Creazione dell'applicazione
---------------------------

In questa sezione verrà illustrato come caricare file nel cluster Hadoop a livello di codice e come eseguire processi Hive utilizzando LINQ per Hive.

1.  Aprire Visual Studio 2012.

2.  Scegliere **Nuovo** dal menu File, quindi fare clic su **Progetto**.

3.  In Nuovo progetto digitare o selezionare i valori seguenti:

 <table data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
 <tr data-morhtml="true">
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriet&agrave;</th>
 <th data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valore</th></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modello</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Applicazione console</td></tr>
 <tr data-morhtml="true">
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
 <td data-morhtml="true" style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SimpleHiveJob</td></tr>
 </table>

4.  Fare clic su **OK** per creare il progetto.

5.  Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Package Manager Console**.

6.  Eseguire i seguenti comandi nella console per installare i pacchetti.

         install-package Microsoft.Hadoop.Hive 
         install-package Microsoft.Hadoop.WebClient 

    Questi comandi aggiungono librerie .NET e riferimenti ad esse nel progetto corrente di Visual Studio.

7.  In Esplora soluzioni fare doppio clic su **Program.cs** per aprirlo.

8.  Aggiungere le istruzioni using seguenti all'inizio del file:

         using Microsoft.Hadoop.WebHDFS.Adapters;
         using Microsoft.Hadoop.WebHDFS;
         using Microsoft.Hadoop.Hive;

9.  Nella funzione Main() copiare e incollare il codice seguente:

         // Upload actors.txt to Blob Storage
         var asvAccount = [Storage-account-name.blob.core.windows.net];
         var asvKey = [Storage account key];
         var asvContainer = [Container name];
         var localFile = "C:/Tutorials/Actors.txt";
         var hadoopUser = [Hadoop user name]; // The HDInsight cluster user
         var hadoopUserPassword = [Hadoop user password]; // The HDInsight cluster user password
         var clusterURI = [HDInsight cluster URL]; //"https://HDInsightCluster Name.azurehdinsight.net:563";
            
         var storageAdapter = new BlobStorageAdapter(asvAccount, asvKey, asvContainer, true);
         var HDFSClient = new WebHDFSClient(hadoopUser, storageAdapter);
            
         Console.WriteLine("Creating MovieData directory and uploading actors.txt...");
         HDFSClient.CreateDirectory("/user/hadoop/MovieData").Wait();
         HDFSClient.CreateFile(localFile, "/user/hadoop/MovieData/Actors.txt").Wait();
            
         // Create Hive connection
         var hiveConnection = new HiveConnection(
           new System.Uri(clusterURI),
           hadoopUser, 
           hadoopUserPassword,
           asvAccount, 
           asvKey);
            
         // Drop any existing tables called Actors
         // Only needed if you wish to rerun this application
         // and drop a previous Actors table.
         //hiveConnection.GetTable<HiveRow>("Actors").Drop();

         Console.WriteLine("Creating a Hive table named 'Actors'...");
         string command =
           @"CREATE TABLE Actors(
                     MovieId string, 
                     ActorId string,
                     Name string, 
                     AwardsCount int) 
                     row format delimited fields 
                 terminated by ',';";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Load data from Actors.txt into the 'Actors' table in Hive...");
         command =
           @"LOAD DATA INPATH 
                 '/user/hadoop/MovieData/Actors.txt'
             OVERWRITE INTO TABLE Actors;";
         hiveConnection.ExecuteHiveQuery(command).Wait();
            
         Console.WriteLine("Performing Hive query...");
         var result = hiveConnection.ExecuteQuery(@"select name, awardscount
                   from actors sort by awardscount desc
                   limit 1");
         result.Wait();

         Console.WriteLine("The results are: {0}", result.Result.ReadToEnd());
         Console.WriteLine("\nPress any key to continue.");
         Console.ReadKey();

10. Aggiornare le costanti nell'applicazione. Il servizio Azure HDInsight utilizza l'archivio BLOB di Azure come file system predefinito. Durante il processo di provisioning di HDInsight, un BLOB viene designato come file system predefinito. È possibile utilizzare il contenitore di file system predefinito o un contenitore in un archivio BLOB diverso. Per ulteriori informazioni, vedere [Utilizzo dell'archivio BLOB di Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

    Se si sceglie di utilizzare il contenitore di file system predefinito, sarà possibile trovare il nome dell'account di archiviazione, la chiave di archiviazione e il nome del contenitore nel file di configurazione *c:\\apps\\dist\\hadoop-1.1.0-SNAPSHOT\\conf\>core-site.xml* tramite la comunicazione remota con il cluster. È possibile trovare il contenitore utilizzato come file system predefinito tramite una ricerca di *fs.default.name*. Per trovare il nome dell'account di archiviazione e la chiave dell'account, cercare *fs.azure.account.key*.

Esecuzione dell'applicazione
----------------------------

Mentre l'applicazione è aperta in Visual Studio, premere **F5** per eseguirla. Verrà aperta una finestra della console e verranno visualizzati i passaggi eseguiti dall'applicazione durante il caricamento dei dati, l'archiviazione in una tabella Hive e infine l'esecuzione di query. Dopo il completamento dell'applicazione e la restituzione dei risultati delle query, premere un tasto qualsiasi per terminare l'applicazione.

![HDI.HadoopSDKOutput](./media/hdinsight-use-hadoop-dotnet-sdk/HDI.HadoopSDKOutput.PNG "Applicazione console")

**Nota**

È possibile che per il completamento di ogni passaggio eseguito dall'applicazione siano necessari alcuni secondi o alcuni minuti. Il tempo necessario per il caricamento del file Actors.txt dipende dalla connessione Internet al data center di Azure, mentre altri passaggi dipendono dalla dimensione del cluster.

**Nota**

L'operazione LOAD DATA INPATH è un'operazione di spostamento che consente di spostare i dati di Actors.txt nello spazio dei nomi del file system controllato da Hive. Il file Actors.txt verrà pertanto rimosso dal percorso di caricamento. Sarà quindi necessario caricare il file Actors.txt ogni volta che si esegue questa applicazione.

Per ulteriori informazioni sul caricamento di dati in Hive, vedere la pagina relativa alle [operazioni introduttive per Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-DMLOperations).

Passaggi successivi
-------------------

È stato illustrato come creare un'applicazione .NET utilizzando Hadoop .NET SDK. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Utilizzo di Pig con HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilizzo di MapReduce con HDInsight](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Utilizzo di Hive con HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)

