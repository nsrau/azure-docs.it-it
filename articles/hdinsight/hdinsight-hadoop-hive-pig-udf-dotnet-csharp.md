---
title: Usare C# con Hive e Pig in Hadoop in HDInsight - Azure | Microsoft Docs
description: Informazioni su come usare le funzioni definite dall'utente C# con lo streaming Hive e Pig in Azure HDInsight.
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
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: b192b19c27045ab6a98baf382e467a0a74d30861
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Usare le funzioni definite dall'utente C# con lo streaming Hive e Pig in Hadoop in HDInsight

Informazioni su come usare le funzioni definite dall'utente C# con Apache Hive e Pig in HDInsight.

> [!IMPORTANT]
> I passaggi descritti in questo documento funzionano con i cluster HDInsight basati su Linux e su Windows. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md).

Sia Hive sia Pig sono in grado di passare i dati alle applicazioni esterne per l'elaborazione. Questo processo è noto come _streaming_. Quando si usa un'applicazione .NET, i dati vengono passati all'applicazione in STDIN e l'applicazione restituisce i risultati in STDOUT. Per leggere e scrivere da STDIN e STDOUT, è possibile usare `Console.ReadLine()` e `Console.WriteLine()` da un'applicazione console.

## <a name="prerequisites"></a>Prerequisiti

* Una familiarità nello scrivere e nel compilare il codice C# destinato a .NET Framework 4.5.

    * Usare qualsiasi IDE desiderato. Si consigliano [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 o [Visual Studio Code](https://code.visualstudio.com/). Nella procedura di questo documento viene usato Visual Studio 2017.

* Un modo per caricare i file con estensione exe nel cluster ed eseguire i processi Pig e Hive. Si consiglia di usare gli strumenti Data Lake per Visual Studio, Azure PowerShell e l'interfaccia della riga di comando di Azure. La procedura in questo documento usa gli strumenti Data Lake per Visual Studio per caricare i file ed eseguire l'esempio di query Hive.

    Per informazioni su altri modi per eseguire query Hive e processi Pig, vedere i seguenti documenti:

    * [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)

    * [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

* Un cluster Hadoop in HDInsight. Per altre informazioni sulla creazione di un cluster, vedere [Creare cluster Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET su HDInsight

* Cluster __HDInsight basati su Linux__ che usano [Mono (https://mono-project.com)](https://mono-project.com) per eseguire le applicazioni .NET. La versione mono 4.2.1 è inclusa nella versione 3.5 di HDInsight.

    Per altre informazioni sulla compatibilità Mono con le versioni di .NET Framework, vedere il documento relativo alla [compatibilità Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

    Per usare una versione specifica di Mono, vedere il documento [Install or update Mono](hdinsight-hadoop-install-mono.md) (Installare o aggiornare Mono).

* I cluster di __HDInsight basato su Windows__ usano Microsoft .NET Common Language Runtime per eseguire le applicazioni .NET.

Per altre informazioni sulla versione del framework .NET e di Mono compresa nelle versioni di HDInsight, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Creare il progetto C\#

### <a name="hive-udf"></a>UDF di Hive

1. Aprire Visual Studio e creare una soluzione. Come tipo di progetto selezionare **App console (.NET Framework)** e assegnare al nuovo progetto il nome **HiveCSharp**.

    > [!IMPORTANT]
    > Selezionare __.NET Framework 4.5__ se si usa un cluster HDInsight basato su Linux. Per altre informazioni sulla compatibilità Mono con le versioni di .NET Framework, vedere il documento relativo alla [compatibilità Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

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

### <a name="pig-udf"></a>UDF di Pig

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

    Questo codice analizza le righe inviate da Pig e riformatta quelle che iniziano con `java.lang.Exception`.

3. Salvare **Program.cs**e quindi compilare il progetto.

## <a name="upload-to-storage"></a>Caricare nella risorsa di archiviazione

1. In Visual Studio aprire **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Se richiesto, immettere le credenziali della sottoscrizione di Azure, quindi fare clic su **Accedi**.

4. Espandere il cluster HDInsight in cui si desidera distribuire l'applicazione. Viene elencata una voce con il testo __(Account di archiviazione predefinito)__.

    ![Esplora server con account di archiviazione per il cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Se è possibile espandere questa voce, si usa un __Account di archiviazione di Azure__ come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, espandere la voce e quindi fare doppio clic su __(Contenitore predefinito)__.

    * Se non è possibile espandere questa voce, si usa un __Azure Data Lake Store__ come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, fare doppio clic sulla voce __(Account di archiviazione predefinito)__.

6. Per caricare i file con estensione .exe, usare uno dei metodi seguenti:

    * Se si usa un __Account di Archiviazione di Azure__, fare clic sull'icona per il caricamento, quindi passare alla cartella **bin\debug** per il progetto **HiveCSharp**. Selezionare infine il file **HiveCSharp.exe** e fare clic su **Ok**.

        ![icona relativa al caricamento](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Se si usa __Azure Data Lake Store__, fare doppio clic su un'area vuota nell'elenco di file e quindi selezionare __Carica__. Selezionare infine il file **HiveCSharp.exe** e fare clic su **Apri**.

    Una volta terminato il caricamento di __HiveCSharp.exe__, ripetere il processo di caricamento per il file __PigUDF.exe__.

## <a name="run-a-hive-query"></a>Eseguire una query Hive

1. In Visual Studio aprire **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Fare clic con il pulsante destro del mouse sul cluster in cui è stata distribuita l'applicazione **HiveCSharp**, quindi selezionare **Scrivi una query Hive**.

4. Per la query Hive usare il testo seguente:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Rimuovere il commento dell'istruzione `add file` che corrisponde al tipo di archiviazione predefinita usata per il cluster.

    Questa query seleziona i campi `clientid`, `devicemake` e `devicemodel` da `hivesampletable` e li passa all'applicazione HiveCSharp.exe. La query si aspetta che l'applicazione restituisca tre campi, che vengono archiviati come `clientid`, `phoneLabel` e `phoneHash`. La query prevede anche di trovare HiveCSharp.exe nella radice del contenitore di archiviazione predefinito.

5. Fare clic su **Invia** per inviare il processo al cluster HDInsight. Viene visualizzata la finestra **Hive Job Summary** (Riepilogo processo Hive).

6. Fare clic su **Aggiorna** per aggiornare il riepilogo fino all'impostazione del valore **Stato processo** su **Completato**. Per visualizzare l'output del processo, fare clic su **Output processo**.

## <a name="run-a-pig-job"></a>Eseguire un processo Pig

1. Per connettersi al cluster HDInsight, usare uno dei metodi seguenti:

    * Se si usa un cluster HDInsight __basato su Linux__, usare SSH. Ad esempio, `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Per altre informazioni, vedere [Connettersi a HDInsight (Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
    
    * Se viene usato un cluster HDInsight __basato su Windows__, [Connettersi a cluster con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)

2. Usare uno dei comandi seguenti per avviare la riga di comando di Pig:

        pig

    > [!IMPORTANT]
    > Se si usa un cluster basato su Windows, usare invece i comandi seguenti:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Viene visualizzato un prompt `grunt>`.

3. Immettere il codice seguente per eseguire un processo Pig che usa l'applicazione .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    L'istruzione `DEFINE` crea un alias di `streamer` per le applicazioni pigudf.exe e `CACHE` lo carica dalla risorsa di archiviazione per il cluster. In seguito, `streamer` viene usato con l'operatore `STREAM` per elaborare le singole righe contenute in LOG e restituire i dati sotto forma di serie di colonne.

    > [!NOTE]
    > Il nome dell'applicazione usato per lo streaming deve essere racchiuso tra caratteri \` (carattere di apice inverso) quando associato ad alias e da ' (virgoletta singola) se usato con `SHIP`.

4. Dopo l'immissione dell'ultima riga il processo dovrebbe essere avviato. L'output restituito è simile al testo seguente:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come usare un'applicazione .NET Framework da Hive e Pig in HDInsight. Per altre informazioni su come usare Python con Hive e Pig, vedere [Usare Python con Hive e Pig in HDInsight](hdinsight-python.md).

Per altre modalità d'uso di Pig e Hive e per informazioni su come usare MapReduce, vedere i documenti seguenti:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)
