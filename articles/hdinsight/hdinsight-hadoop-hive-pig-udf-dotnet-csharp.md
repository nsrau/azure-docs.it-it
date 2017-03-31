---
title: Usare C# con Hive e Pig in Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come usare le funzioni definite dall&quot;utente C# con lo streaming Hive e Pig in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 257138fddc75b39985ba974b1314e978a554b1e2
ms.lasthandoff: 03/07/2017


---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Usare le funzioni definite dall'utente C# con lo streaming Hive e Pig in Hadoop in HDInsight

Hive e Pig sono soluzioni ottimali per usare i dati in Azure HDInsight, ma in alcuni casi è necessario un linguaggio più generico. Sia Hive che Pig consentono di chiamare codice esterno tramite funzioni definite dall'utente (UDF, User-Defined Function) o streaming.

Questo documento illustra come usare C# con Hive e Pig.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight che usa il sistema operativo Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti

* Windows 7 o versioni successive.

* Visual Studio con le seguenti versioni:

  * Visual Studio 2012 Professional/Premium/Ultimate con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 Community/Professional/Premium/Ultimate con [Update 4](https://www.microsoft.com/download/details.aspx?id=44921)

  * Visual Studio 2015

  * Visual Studio 2017

* Cluster Hadoop in HDInsight. Per la procedura di creazione di un cluster, vedere [Provisioning di un cluster HDInsight](hdinsight-provision-clusters.md)

* Strumenti Hadoop per Visual Studio o strumenti Data Lake per Visual Studio. Per la procedura per l'installazione e la configurazione degli strumenti, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Gli strumenti Data Lake vengono installati in Visual Studio 2017 selezionando il carico di lavoro __Sviluppo di Azure__ durante l'installazione.

## <a name="net-on-hdinsight"></a>.NET su HDInsight

Il Common Language Runtime (CLR) e i framework .NET vengono installati per impostazione predefinita nei cluster HDInsight basati su Windows. Il CLR .NET consente di usare applicazioni C# con streaming Hive e Pig. I dati vengono passati tra Hive/Pig e l'applicazione C# tramite stdout/stdin.

> [!NOTE]
> Non è attualmente disponibile supporto per l'esecuzione di UDF .NET Framework su cluster HDInsight basati su Linux.


## <a name="net-and-streaming"></a>.NET e streaming

Lo streaming comporta il passaggio di dati da parte di Hive e Pig a un'applicazione esterna su stdout e la ricezione dei risultati su stdin. Per applicazioni C#, usare `Console.ReadLine()` e `Console.WriteLine()`.

Poiché Hive e Pig devono richiamare l'applicazione in fase di esecuzione, per i progetti C# è consigliabile usare il modello **App console (.NET Framework)**.

## <a name="hive-and-c35"></a>Hive e C&#35;

### <a name="create-the-c-project"></a>Creare il progetto C#

1. Aprire Visual Studio e creare una soluzione. Come tipo di progetto selezionare **App console (.NET Framework)** e assegnare al nuovo progetto il nome **HiveCSharp**.

2. Sostituire il contenuto del file **Program.cs** con il codice seguente:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Compilare il progetto.

### <a name="upload-to-storage"></a>Caricare nella risorsa di archiviazione

1. In Visual Studio aprire **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Se richiesto, immettere le credenziali della sottoscrizione di Azure, quindi fare clic su **Accedi**.

4. Espandere il cluster HDInsight in cui si vuole distribuire l'applicazione, quindi espandere **Account di archiviazione predefinito**.

    ![Esplora server con account di archiviazione per il cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

5. Fare doppio clic su **Contenitore predefinito** per consentire al cluster di visualizzare il contenuto del contenitore predefinito.
6. Fare clic sull'icona per il caricamento, quindi passare alla cartella **bin\debug** per il progetto **HiveCSharp**. Selezionare infine il file **HiveCSharp.exe** e fare clic su **Ok**.

    ![icona relativa al caricamento](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

7. Al termine del caricamento è possibile usare l'applicazione da una query Hive.

### <a name="hive-query"></a>Query Hive

1. In Visual Studio aprire **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Fare clic con il pulsante destro del mouse sul cluster in cui è stata distribuita l'applicazione **HiveCSharp**, quindi selezionare **Scrivi una query Hive**.

4. Per la query Hive usare il testo seguente:

    ```hiveql
    add file wasbs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    Questa query seleziona i campi `clientid`, `devicemake` e `devicemodel` da `hivesampletable` e li passa all'applicazione HiveCSharp.exe. La query si aspetta che l'applicazione restituisca tre campi, che vengono archiviati come `clientid`, `phoneLabel` e `phoneHash`. La query si aspetta anche di trovare HiveCSharp.exe nella radice del contenitore di archiviazione predefinito (`add file wasbs:///HiveCSharp.exe`).

5. Fare clic su **Invia** per inviare il processo al cluster HDInsight. Viene visualizzata la finestra **Hive Job Summary** (Riepilogo processo Hive).

6. Fare clic su **Aggiorna** per aggiornare il riepilogo fino all'impostazione del valore **Stato processo** su **Completato**. Per visualizzare l'output del processo, fare clic su **Output processo**.

## <a name="pig-and-c35"></a>Pig e C&#35;

### <a name="create-the-c-project"></a>Creare il progetto C#

1. Aprire Visual Studio e creare una soluzione. Come tipo di progetto selezionare **Applicazione console** e assegnare al nuovo progetto il nome **PigUDF**.

2. Sostituire il contenuto del file **Program.cs** con il codice seguente:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Questa applicazione analizza le righe inviate da Pig e riformatta quelle che iniziano con `java.lang.Exception`.

3. Salvare **Program.cs**e quindi compilare il progetto.

### <a name="upload-the-application"></a>Caricare l'applicazione

1. Lo streaming Pig si aspetta che l'applicazione sia locale nel file system del cluster. Abilitare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni fornite in [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. Dopo la connessione, copiare **PigUDF.exe** dalla directory **bin/debug** per il progetto PigUDF nel computer locale, quindi incollarlo nella directory **%PIG_HOME%** nel cluster.

### <a name="use-the-application-from-pig-latin"></a>Usare l'applicazione da Pig Latin

1. Dalla sessione Desktop remoto avviare la riga di comando di Hadoop usando l'icona relativa alla **riga di comando di Hadoop** sul desktop.

2. Usare il codice seguente per avviare la riga di comando di Pig:

        cd %PIG_HOME%
        bin\pig

    Viene visualizzato un prompt `grunt>`.
    
3. Immettere il codice seguente per eseguire un processo Pig usando l'applicazione .NET Framework:

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    L'istruzione `DEFINE` crea un alias di `streamer` per le applicazioni pigudf.exe e `SHIP` lo distribuisce nei nodi del cluster. In seguito, `streamer` viene usato con l'operatore `STREAM` per elaborare le singole righe contenute in LOG e restituire i dati sotto forma di serie di colonne.

    > [!NOTE]
    > Il nome dell'applicazione usato per lo streaming deve essere racchiuso tra caratteri \` (carattere di apice inverso) quando associato ad alias e da ' (virgoletta singola) se usato con `SHIP`.

4. Dopo l'immissione dell'ultima riga il processo dovrebbe essere avviato. L'output restituito è simile al testo seguente:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="summary"></a>Riepilogo

In questo documento è stato illustrato come usare un'applicazione .NET Framework da Hive e Pig in HDInsight. Per altre informazioni su come usare Python con Hive e Pig, vedere [Usare Python con Hive e Pig in HDInsight](hdinsight-python.md).

Per altre modalità d'uso di Pig e Hive e per informazioni su come usare MapReduce, vedere i documenti seguenti:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

