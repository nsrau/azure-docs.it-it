---
title: Eseguire un processo Hadoop usando Azure Cosmos DB e HDInsight | Microsoft Docs
description: Informazioni su come eseguire un semplice processo Hive, Pig e MapReduce con Azure Cosmos DB e Azure HDInsight.
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/08/2017
ms.author: denlee
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: 427864fc4e494c19fcda4cfd454a9923499f6337
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---
# <a name="Azure Cosmos DB-HDInsight"></a>Eseguire un processo Apache Hive, Pig o Hadoop usando Azure Cosmos DB e HDInsight
Questa esercitazione illustra come eseguire processi [Apache Hive][apache-hive], [Apache Pig][apache-pig] e [Apache Hadoop][apache-hadoop] MapReduce in Azure HDInsight con il connettore Hadoop di Cosmos DB. Il connettore Hadoop di Cosmos DB consente a quest'ultimo di fungere da origine e sink per i processi Hive, Pig e MapReduce. Questa esercitazione userà Cosmos DB come origine dati e destinazione per i processi Hadoop.

Dopo aver completato questa esercitazione, si potrà rispondere alle domande seguenti:

* Come si caricano i dati da Cosmos DB usando un processo Hive, Pig o MapReduce?
* Come si archiviano i dati in Cosmos DB usando un processo Hive, Pig o MapReduce?

Per iniziare, è consigliabile guardare il video seguente che illustra un processo Hive usando Cosmos DB e HDInsight.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

Tornare quindi a questo articolo, che illustra in modo dettagliato come eseguire processi di analisi sui dati di Cosmos DB.

> [!TIP]
> Questa esercitazione presume che l'utente abbia una precedente esperienza nell'uso di Apache Hadoop, Hive e/o Pig. Se non si conoscono Apache Hadoop, Hive e Pig, si consiglia di vedere la pagina relativa alla [documentazione di Apache Hadoop][apache-hadoop-doc]. Questa esercitazione presuppone che si abbia esperienza con Cosmos DB e che sia disponibile un account Cosmos DB. Se non si conosce Cosmos DB o non si ha un account Cosmos DB, vedere la pagina [Per iniziare][getting-started].
>
>

Se non si ha tempo di completare l'esercitazione e si vogliono ottenere gli script PowerShell completi di esempio per Hive, Pig e MapReduce, È possibile ottenerli [qui][hdinsight-samples]. Il download contiene anche i file hql, pig e java per questi esempi.

## <a name="NewestVersion"></a>Versione più recente
<table border='1'>
    <tr><th>Versione di Hadoop Connector</th>
        <td>1.2.0</td></tr>
    <tr><th>URI script</th>
        <td>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</td></tr>
    <tr><th>Data ultima modifica</th>
        <td>26/04/2016</td></tr>
    <tr><th>Versioni supportate di HDInsight</th>
        <td>3.1, 3.2.</td></tr>
    <tr><th>Registro modifiche</th>
        <td>Aggiornamento di Azure Cosmos DB Java SDK alla versione 1.6.0</br>
            Aggiunta del supporto per le raccolte partizionate come origine e sink</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Prerequisiti
Prima di seguire le istruzioni di questa esercitazione, verificare che siano disponibili gli elementi seguenti:

* Un account Cosmos DB, un database e una raccolta di documenti all'interno. Per altre informazioni, vedere l'[introduzione a Cosmos DB][getting-started]. Importare dati di esempio nell'account Cosmos DB con lo [strumento di importazione di Cosmos DB][import-data].
* Velocità effettiva. Le letture e le scritture da HDInsight verranno conteggiate rispetto alle unità di richiesta dell'unità di capacità.
* Capacità di una stored procedure aggiuntiva all'interno di ciascun insieme di output. Le stored procedure vengono usate per trasferire i documenti risultanti.
* Capacità per i documenti risultanti da processi Hive, Pig o MapReduce.
* *Facoltativo*Capacità per una raccolta aggiuntiva.

> [!WARNING]
> Per evitare la creazione di una nuova raccolta durante uno qualsiasi dei processi è possibile stampare i risultati in stdout, salvare l'output nel contenitore WASB oppure specificare una raccolta già esistente. Nel caso in cui venga specificata una raccolta esistente, verranno creati nuovi documenti all'interno della raccolta, mentre i documenti esistenti verranno interessati solo se si verifica un conflitto tra gli *ids*. **Il connettore sovrascriverà automaticamente i documenti esistenti con conflitti tra ID**. È possibile disattivare questa funzionalità impostando l'opzione upsert su false. Se upsert è impostato su false e si verifica un conflitto, il processo Hadoop avrà esito negativo, indicando un errore di conflitto tra ID.
>
>

## <a name="ProvisionHDInsight"></a>Passaggio 1: Creare un nuovo cluster HDInsight
Questa esercitazione usa Azioni script del portale di Azure per personalizzare il cluster HDInsight. In questa esercitazione si userà il portale di Azure per creare un cluster HDInsight. Per istruzioni su come usare i cmdlet di PowerShell oppure HDInsight .NET SDK, vedere l'articolo [Personalizzare cluster HDInsight mediante Azione script][hdinsight-custom-provision].

1. Accedere al [portale di Azure][azure-portal].
2. Fare clic su **+ Nuovo** nella parte superiore del riquadro di spostamento a sinistra e cercare **HDInsight** nella barra di ricerca in alto nel pannello Nuovo.
3. **HDInsight** pubblicato da **Microsoft** verrà visualizzato in cima ai risultati. Fare clic sulla voce e selezionare **Crea**.
4. Nel pannello Nuovo cluster HDInsight immettere un nome in **Nome cluster** e selezionare la **sottoscrizione** in cui effettuare il provisioning della risorsa.

    <table border='1'>
        <tr><td>Nome del cluster</td><td>Assegnare un nome al cluster.<br/>
Il nome DNS deve iniziare e finire con un carattere alfanumerico e può contenere trattini.<br/>
Il campo deve essere una stringa composta da un minimo di 3 a un massimo di 63 caratteri.</td></tr>
        <tr><td>Nome sottoscrizione</td>
            <td>Se sono disponibili più sottoscrizioni di Azure, selezionare quella che ospiterà il cluster HDInsight. </td></tr>
    </table>
5. Fare clic su **Selezionare il tipo di cluster** e impostare le seguenti proprietà sui valori specificati.

    <table border='1'>
        <tr><td>Tipo di cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Livello cluster</td><td><strong>Standard</strong></td></tr>
        <tr><td>Sistema operativo</td><td><strong>Windows</strong></td></tr>
        <tr><td>Versione</td><td>versione più recente</td></tr>
    </table>

    A questo punto fare clic su **SELEZIONA**.

    ![Fornire i dettagli di iniziale del cluster Hadoop HDInsight][image-customprovision-page1]
6. Fare clic su **Credenziali** per impostare le credenziali di accesso e di accesso remoto. Scegliere il **nome utente dell'account di accesso del cluster** e la **password dell'account di accesso del cluster**.

    Se si intende accedere al cluster in modalità remota, selezionare *Sì* nella parte inferiore del pannello e specificare un nome utente e una password.
7. Fare clic su **Origine dati** per impostare la posizione primaria per l'accesso ai dati. Scegliere un'opzione in **Metodo di selezione** e specificare un account di archiviazione già esistente o crearne uno nuovo.
8. Nello stesso pannello specificare un **contenitore predefinito** e una **località**. Fare quindi clic su **SELEZIONA**.

   > [!NOTE]
   > Per ottenere prestazioni migliori, selezionare una località vicina all'area dell'account Cosmos DB.
   >
   >
9. Fare clic su **Prezzi** per selezionare numero e tipo di nodi. È possibile mantenere la configurazione predefinita e ridimensionare il numero di nodi del ruolo di lavoro in un secondo momento.
10. Fare clic su **Configurazione facoltativa** e quindi selezionare **Azioni script** nel pannello Configurazione facoltativa.

     In Azioni script immettere le informazioni seguenti per personalizzare il cluster HDInsight.

     <table border='1'>
         <tr><th>Proprietà</th><th>Valore</th></tr>
         <tr><td>Nome</td>
             <td>Specificare un nome per l'azione script.</td></tr>
         <tr><td>URI script</td>
             <td>Specificare l'URI dello script da richiamare per personalizzare il cluster.</br></br>
Immettere: </br> <strong>https://portalcontent.blob.core.windows.net/scriptaction/documentdb-hadoop-installer-v04.ps1</strong>.</td></tr>
         <tr><td>Head</td>
             <td>Selezionare la casella di controllo per eseguire lo script di PowerShell nel nodo head.</br></br>
             <strong>Selezionare questa casella di controllo</strong>.</td></tr>
         <tr><td>Worker</td>
             <td>Selezionare la casella di controllo per eseguire lo script di PowerShell nel nodo del ruolo di lavoro.</br></br>
             <strong>Selezionare questa casella di controllo</strong>.</td></tr>
         <tr><td>Zookeeper</td>
             <td>Selezionare la casella di controllo per eseguire lo script di PowerShell nel nodo Zookeeper.</br></br>
             <strong>Non necessario</strong>.
             </td></tr>
         <tr><td>Parametri</td>
             <td>Specificare i parametri, se richiesti dallo script.</br></br>
             <strong>Nessun parametro necessario</strong>.</td></tr>
     </table>
11. Creare un nuovo **gruppo di risorse** o usarne uno esistente nella sottoscrizione di Azure.
12. Selezionare ora **Aggiungi al dashboard** per tenere traccia della distribuzione e fare clic su **Crea**.

## <a name="InstallCmdlets"></a>Passaggio 2: Installare e configurare Azure PowerShell
1. Installare Azure PowerShell. Le istruzioni sono consultabili [qui][powershell-install-configure].

   > [!NOTE]
   > In alternativa, solo per le query Hive, è possibile usare l'Editor Hive online di HDInsight. A tale scopo, accedere al [portale di Azure][azure-portal], fare clic su **HDInsight** nel riquadro sinistro per visualizzare un elenco dei cluster HDInsight. Fare clic sul cluster che sul quale si vogliono eseguire le query Hive e quindi fare clic su **Console Query**.
   >
   >
2. Aprire Integrated Scripting Environment di Azure PowerShell:

   * In un computer che esegue Windows 8 o Windows Server 2012 o versione successiva, è possibile usare la funzionalità di ricerca incorporata. Dalla schermata Start digitare **powershell ise** e fare clic su **Invio**.
   * In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile usare il menu Start. Dal menu Start, digitare **Prompt dei comandi** nella casella di ricerca, quindi nell'elenco dei risultati, fare clic su **Prompt dei comandi**. Al prompt dei comandi digitare **powershell_ise** e fare clic su **Invio**.
3. Aggiungere il proprio Account Azure.

   1. Nel riquadro console digitare **Add-AzureAccount** e fare clic su **Invio**.
   2. Digitare l'indirizzo di posta elettronica associato alla sottoscrizione di Azure e fare clic su **Continua**.
   3. Digitare la password per la sottoscrizione di Azure.
   4. Fare clic su **Accedi**.
4. Il diagramma seguente identifica le parti importanti dell'ambiente di Scripting PowerShell di Azure.

    ![Diagramma per Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Passaggio 3: Eseguire un processo Hive usando Cosmos DB e HDInsight
> [!IMPORTANT]
> Tutte le variabili indicate da < > devono essere compilate usando le proprie impostazioni di configurazione.
>
>

1. Impostare le variabili seguenti nel riquadro di script di PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>Si inizia a costruire la stringa di query. Verrà scritta una query Hive che accetta i timestamp generati dal sistema di tutti i documenti (_ts) e ID univoci (_rid) da una raccolta di Azure Cosmos DB, calcola tutti i documenti in base al minuto e quindi archivia i risultati in una nuova raccolta di Azure Cosmos DB.</p>

    <p>Creare prima di tutto una tabella Hive dalla raccolta Azure Cosmos DB. Aggiungere il seguente frammento di codice nel riquadro di script di PowerShell <strong>dopo</strong> il frammento di codice da #1. Assicurarsi di includere il parametro DocumentDB.query facoltativo per ridurre i documenti semplicemente a _ts e _rid.</p>

   > [!NOTE]
   > **La denominazione DocumentDB.inputCollections non è stata un errore.** È effettivamente possibile aggiungere più raccolte come input: </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. Successivamente, si passa alla creazione di una tabella Hive per la raccolta di output. Le proprietà del documento di output saranno mese, giorno, ora, minuti e numero totale di occorrenze.

   > [!NOTE]
   > **Ancora una volta, la denominazione di DocumentDB.outputCollections non è un errore.** È effettivamente possibile aggiungere più raccolte come output: </br>
   > '*DocumentDB.outputCollections*' = '*\<DocumentDB Output Collection Name 1\>*,*\<DocumentDB Output Collection Name 2\>*' </br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola. </br></br>
   > Verrà eseguita la distribuzione round robin dei documenti tra più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verrà archiviato nella raccolta successiva e così via.
   >
   >

       # Create a Hive table for the output data to DocumentDB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. Infine, si calcoleranno i documenti per mese, giorno, ora e minuto e si inseriranno i risultati nuovamente nell'output della tabella Hive.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. Aggiungere il frammento di script seguente per creare una definizione processo Hive dalla query precedente.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    È anche possibile usare l'opzione -File per specificare un file di script HiveQL in HDFS.
4. Aggiungere il frammento seguente per salvare l'ora di inizio e inviare il processo Hive.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. Aggiungere quanto segue per attendere il completamento del processo Hive.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. Aggiungere il comando seguente per stampare l'output standard e l'ora di inizio e fine.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **Eseguire** il nuovo script. **Fare clic** sul pulsante di esecuzione verde.
8. Verificare i risultati. Accedere al [Portale di Azure][azure-portal].

   1. Fare clic su <strong>Sfoglia</strong> nel riquadro a sinistra. </br>
   2. Fare clic su <strong>tutto</strong> in alto a destra del riquadro di esplorazione. </br>
   3. Trovare e fare clic su <strong>Account Azure Cosmos DB</strong>. </br>
   4. Trovare quindi l'<strong>account Azure Cosmos DB</strong>, il <strong>database Azure Cosmos DB</strong> e la <strong>raccolta Azure Cosmos DB</strong> associati alla raccolta di output specificata nella query Hive.</br>
   5. Infine, fare clic su <strong>Esplora documenti</strong> sotto <strong>Strumenti di sviluppo</strong>.</br></p>

   Verranno visualizzati i risultati della query Hive.

   ![Risultati della query relativa alla tabella][image-hive-query-results]

## <a name="RunPig"></a>Passaggio 4: Eseguire un processo Pig usando Cosmos DB e HDInsight
> [!IMPORTANT]
> Tutte le variabili indicate da < > devono essere compilate usando le proprie impostazioni di configurazione.
>
>

1. Impostare le variabili seguenti nel riquadro di script di PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>Si inizia a costruire la stringa di query. Verrà scritta una query Pig che accetta i timestamp generati dal sistema di tutti i documenti (_ts) e ID univoci (_rid) da una raccolta di Azure Cosmos DB, calcola tutti i documenti in base al minuto e quindi archivia i risultati in una nuova raccolta di Azure Cosmos DB.</p>
    <p>In primo luogo, caricare i documenti da Cosmos DB in HDInsight. Aggiungere il seguente frammento di codice nel riquadro di script di PowerShell <strong>dopo</strong> il frammento di codice da #1. Assicurarsi di aggiungere una query di DocumentDB al parametro di query DocumentDB facoltativo per ridurre i documenti semplicemente a _ts e _rid.</p>

   > [!NOTE]
   > È effettivamente possibile aggiungere più raccolte come input: </br>
   > '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*'</br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola. </b>
   >
   >

    Verrà eseguita la distribuzione round robin dei documenti tra più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verrà archiviato nella raccolta successiva e così via.

        # Load data from Cosmos DB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. Successivamente, verranno calcolati i documenti in base a mese, giorno, ora, minuti e numero totale di occorrenze.

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. Infine, si archivieranno i risultati nella nuova raccolta di output.

   > [!NOTE]
   > È effettivamente possibile aggiungere più raccolte come output: </br>
   > '\<DocumentDB Output Collection Name 1\>,\<DocumentDB Output Collection Name 2\>'</br> I nomi di raccolta sono separati senza spazi, mediante una singola virgola.</br>
   > Verrà eseguita la distribuzione round robin dei documenti tra più raccolte. Un batch di documenti verrà archiviato in una raccolta, quindi un secondo batch dei documenti verrà archiviato nella raccolta successiva e così via.
   >
   >

        # Store output data to Cosmos DB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. Aggiungere il frammento di script seguente per creare una definizione processo Pig dalla query precedente.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    È anche possibile usare l'opzione -File per specificare un file di script Pig in HDFS.
6. Aggiungere il frammento seguente per salvare l'ora di inizio e inviare il processo Pig.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. Aggiungere quanto segue per attendere il completamento del processo Pig.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. Aggiungere il comando seguente per stampare l'output standard e l'ora di inizio e fine.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **Eseguire** il nuovo script. **Fare clic** sul pulsante di esecuzione verde.
10. Verificare i risultati. Accedere al [Portale di Azure][azure-portal].

    1. Fare clic su <strong>Sfoglia</strong> nel riquadro a sinistra. </br>
    2. Fare clic su <strong>tutto</strong> in alto a destra del riquadro di esplorazione. </br>
    3. Trovare e fare clic su <strong>Account Azure Cosmos DB</strong>. </br>
    4. Trovare quindi l'<strong>account Azure Cosmos DB</strong>, il <strong>database Azure Cosmos DB</strong> e la <strong>raccolta Azure Cosmos DB</strong> associati alla raccolta di output specificata nella query Pig.</br>
    5. Infine, fare clic su <strong>Esplora documenti</strong> sotto <strong>Strumenti di sviluppo</strong>.</br></p>

    Verranno visualizzati i risultati della query Pig.

    ![Risultati della query SQL][image-pig-query-results]

## <a name="RunMapReduce"></a>Passaggio 5: Eseguire un processo MapReduce usando Azure Cosmos DB e HDInsight
1. Impostare le variabili seguenti nel riquadro di script di PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. È possibile eseguire un processo MapReduce che calcola il numero di occorrenze per ogni proprietà di documento dalla raccolta Azure Cosmos DB. Aggiungere questo frammento di script **dopo** il frammento di codice riportato sopra.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > Il file TallyProperties-v01.jar viene fornito con l'installazione personalizzata del connettore Hadoop di Cosmos DB.
   >
   >
3. Per inviare il processo MapReduce, aggiungere il comando seguente.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Oltre alla definizione del processo MapReduce, è necessario specificare anche il nome del cluster HDInsight in cui si desidera eseguire il processo MapReduce e le credenziali. Start-AzureHDInsightJob è una chiamata non sincronizzata. Per verificare il completamento del processo, usare il cmdlet *Wait-AzureHDInsightJob* .
4. Per controllare se si sono verificati errori nel processo MapReduce, aggiungere il comando seguente:

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **Eseguire** il nuovo script. **Fare clic** sul pulsante di esecuzione verde.
6. Verificare i risultati. Accedere al [Portale di Azure][azure-portal].

   1. Fare clic su <strong>Sfoglia</strong> nel riquadro a sinistra.
   2. Fare clic su <strong>tutto</strong> in alto a destra del riquadro di esplorazione.
   3. Trovare e fare clic su <strong>Account Azure Cosmos DB</strong>.
   4. Trovare quindi l'<strong>account Azure Cosmos DB</strong>, il <strong>database Azure Cosmos DB</strong> e la <strong>raccolta Azure Cosmos DB</strong> associati alla raccolta di output specificata nel processo MapReduce.
   5. Infine, fare clic su <strong>Esplora documenti</strong> sotto <strong>Strumenti di sviluppo</strong>.

      Verranno visualizzati i risultati del processo MapReduce.

      ![Risultati della query MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Passaggi successivi
Congratulazioni. Sono stati eseguiti i primi processi Hive, Pig e MapReduce usando Azure Cosmos DB e HDInsight.

Abbiamo reso Open Source il connettore Hadoop. Se si è interessati, è possibile contribuire in [GitHub][github].

Per altre informazioni, vedere gli articoli seguenti:

* [Sviluppare un'applicazione Java con Documentdb][documentdb-java-application]
* [Sviluppare programmi MapReduce Java per Hadoop in HDInsight][hdinsight-develop-deploy-java-mapreduce]
* [Introduzione all'uso di Hadoop con Hive in HDInsight per analizzare l'uso di telefoni cellulari][hdinsight-get-started]
* [Usare MapReduce con HDInsight][hdinsight-use-mapreduce]
* [Usare Hive con HDInsight][hdinsight-use-hive]
* [Usare Pig con HDInsight][hdinsight-use-pig]
* [Personalizzare cluster HDInsight mediante l'azione script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[import-data]: import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]: ../hdinsight/hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight-use-mapreduce]: ../hdinsight/hdinsight-use-mapreduce.md
[hdinsight-use-pig]: ../hdinsight/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0

