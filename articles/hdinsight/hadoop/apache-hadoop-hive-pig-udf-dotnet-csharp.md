---
title: C#, Apache Hive & Apache Pig in Apache Hadoop-Azure HDInsight
description: Informazioni su come usare le funzioni definite dall'utente C# con lo streaming Apache Hive e Apache Pig in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: hrasheed
ms.openlocfilehash: b8baf8ee11d34756e55f3a78fd5916e042785587
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821659"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Usare C# funzioni definite dall'utente con Apache hive e Apache Pig in Apache Hadoop in HDInsight

Informazioni su come usare C# funzioni definite dall'utente (UDF) con [Apache hive](https://hive.apache.org) e [Apache Pig](https://pig.apache.org) in HDInsight.

> [!IMPORTANT]
> I passaggi descritti in questo documento funzionano con i cluster HDInsight basati su Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'articolo sul [controllo delle versioni del componente di HDInsight](../hdinsight-component-versioning.md).

Sia Hive sia Pig sono in grado di passare i dati alle applicazioni esterne per l'elaborazione. Questo processo è noto come _streaming_. Quando si usa un'applicazione .NET, i dati vengono passati all'applicazione in STDIN e l'applicazione restituisce i risultati in STDOUT. Per leggere e scrivere da STDIN e STDOUT, è possibile usare `Console.ReadLine()` e `Console.WriteLine()` da un'applicazione console.

## <a name="prerequisites"></a>Prerequisiti

* Una familiarità nello scrivere e nel compilare il codice C# destinato a .NET Framework 4.5.

    Usare qualsiasi IDE desiderato. È consigliabile usare [Visual Studio](https://www.visualstudio.com/vs) o [Visual Studio Code](https://code.visualstudio.com/). I passaggi descritti in questo documento usano Visual Studio 2019.

* Un modo per caricare i file con estensione exe nel cluster ed eseguire i processi Pig e Hive. È consigliabile [Data Lake Tools per Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md), [Azure PowerShell](/powershell/azure)e l' [interfaccia](/cli/azure/install-azure-cli?view=azure-cli-latest)della riga di comando di Azure. La procedura in questo documento usa gli strumenti Data Lake per Visual Studio per caricare i file ed eseguire l'esempio di query Hive.

    Per informazioni su altri modi per eseguire query hive, vedere [che cos'è Apache hive e HiveQL in Azure HDInsight?](hdinsight-use-hive.md).

* Un cluster Hadoop in HDInsight. Per altre informazioni sulla creazione di un cluster, vedere [creare cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET su HDInsight

Per eseguire applicazioni .NET, i cluster *HDInsight basati su Linux* usano [Mono (https://mono-project.com)](https://mono-project.com)). La versione Mono 4.2.1 è inclusa nella versione 3.6 di HDInsight.

Per altre informazioni sulla compatibilità Mono con le versioni di .NET Framework, vedere il documento relativo alla [compatibilità Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Per altre informazioni sulla versione di .NET Framework e mono incluse con le versioni HDInsight, vedere la pagina relativa alle [versioni dei componenti HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Creare il progetto C\#

Le sezioni seguenti descrivono come creare un C# progetto in Visual Studio per un Apache hive UDF e una funzione definita dall'utente di Apache Pig.

### <a name="apache-hive-udf"></a>UDF Apache Hive

Per creare un C# progetto per una funzione definita dall'utente Apache hive:

1. Aprire Visual Studio.

2. Nella finestra **Start** selezionare **Crea un nuovo progetto**.

3. Nella finestra **Crea un nuovo progetto** scorrere fino a selezionare il modello **App Console (.NET Framework)** ( C# versione). Quindi selezionare **Avanti**.

4. Nella finestra **Configura nuovo progetto** , immettere il nome di un **progetto** *HiveCSharp*e individuare o creare un **percorso** in cui salvare il nuovo progetto. Selezionare quindi **Crea**.

5. Nell'IDE di Visual Studio sostituire il contenuto di *Program.cs* con il codice seguente:

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

6. Dalla barra dei menu scegliere **compila** > **Compila soluzione** per compilare il progetto.

### <a name="apache-pig-udf"></a>UDF Apache Pig

Per creare un C# progetto per una funzione definita dall'utente Apache hive:

1. Aprire Visual Studio.

2. Nella finestra **Start** selezionare **Crea un nuovo progetto**.

3. Nella finestra **Crea un nuovo progetto** scorrere fino a selezionare il modello **App Console (.NET Framework)** ( C# versione). Quindi selezionare **Avanti**.

4. Nella finestra **Configura nuovo progetto** immettere il nome di un **progetto** *PigUDF*e passare a o creare un **percorso** in cui salvare il nuovo progetto. Selezionare quindi **Crea**.

5. Nell'IDE di Visual Studio sostituire il contenuto di *Program.cs* con il codice seguente:

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

6. Dalla barra dei menu scegliere **compila** > **Compila soluzione** per compilare il progetto.

## <a name="upload-to-storage"></a>Caricare nella risorsa di archiviazione

Caricare quindi le applicazioni hive e Pig UDF nell'archivio in un cluster HDInsight.

1. In Visual Studio scegliere **visualizza** > **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Se richiesto, immettere le credenziali della sottoscrizione di Azure e quindi selezionare **Accedi**.

4. Espandere il cluster HDInsight in cui si desidera distribuire l'applicazione. Viene elencata una voce con il testo **(Account di archiviazione predefinito)** .

    ![Account di archiviazione predefinito, cluster HDInsight, Esplora server](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Se è possibile espandere questa voce, si usa un **account di archiviazione di Azure** come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, espandere la voce e quindi fare doppio clic su **(Contenitore predefinito)** .

    * Se non è possibile espandere questa voce, si usa **Azure Data Lake storage** come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, fare doppio clic sulla voce **(Account di archiviazione predefinito)** .

5. Per caricare i file con estensione .exe, usare uno dei metodi seguenti:

    * Se si usa un **account di archiviazione di Azure**, selezionare l'icona **Carica BLOB** .

        ![Icona di caricamento HDInsight per il nuovo progetto](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Nella finestra di dialogo **Carica nuovo file** , in **nome file**, selezionare **Sfoglia**. Nella finestra di dialogo **Carica BLOB** passare alla cartella *bin\Debug* per il progetto *HiveCSharp* , quindi scegliere il file *HiveCSharp. exe* . Infine, selezionare **Apri** e quindi **OK** per completare il caricamento.
    
    * Se si usa **Azure Data Lake storage**, fare clic con il pulsante destro del mouse su un'area vuota nell'elenco dei file e quindi scegliere **carica**. Infine, scegliere il file *HiveCSharp. exe* e selezionare **Apri**.

    Una volta terminato il caricamento di *HiveCSharp.exe*, ripetere il processo di caricamento per il file *PigUDF.exe*.

## <a name="run-an-apache-hive-query"></a>Eseguire una query Apache Hive

A questo punto è possibile eseguire una query hive che usa l'applicazione della funzione definita dall'utente hive.

1. In Visual Studio scegliere **visualizza** > **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Fare clic con il pulsante destro del mouse sul cluster in cui è stata distribuita l'applicazione *HiveCSharp*, quindi selezionare **Scrivi una query Hive**.

4. Per la query Hive usare il testo seguente:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Rimuovere il commento dell'istruzione `add file` che corrisponde al tipo di archiviazione predefinita usata per il cluster.

    Questa query seleziona i campi `clientid`, `devicemake`e `devicemodel` da `hivesampletable`, quindi passa i campi all'applicazione *HiveCSharp. exe* . La query si aspetta che l'applicazione restituisca tre campi, che vengono archiviati come `clientid`, `phoneLabel` e `phoneHash`. La query prevede anche di trovare *HiveCSharp. exe* nella radice del contenitore di archiviazione predefinito.

5. Selezionare **Submit (Invia** ) per inviare il processo al cluster HDInsight. Viene visualizzata la finestra **Hive Job Summary** (Riepilogo processo Hive).

6. Selezionare **Aggiorna** per aggiornare il riepilogo finché **lo stato del processo** non viene **completato**. Per visualizzare l'output del processo, selezionare **output del processo**.

## <a name="run-an-apache-pig-job"></a>Eseguire un processo Apache Pig

È anche possibile eseguire un processo Pig che usa l'applicazione Pig UDF.

1. Connettersi al cluster HDInsight usando SSH. Eseguire ad esempio il comando `ssh sshuser@<clustername>-ssh.azurehdinsight.net`. Per altre informazioni, vedere [usare SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare il comando seguente per avviare la riga di comando di Pig:

    ```shell
    pig
    ```

    Viene visualizzato un prompt `grunt>`.

3. Immettere il codice seguente per eseguire un processo Pig che usa l'applicazione .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    L'istruzione `DEFINE` crea un alias di `streamer` per l'applicazione *PigUDF. exe* e `CACHE` lo carica dalla risorsa di archiviazione predefinita per il cluster. In seguito `streamer` viene utilizzato con l'operatore `STREAM` per elaborare le singole righe contenute in `LOG` e restituire i dati come una serie di colonne.

    > [!NOTE]
    > Il nome dell'applicazione usato per lo streaming deve essere racchiuso tra il carattere \` (apice inverso) quando viene usato con un alias e il carattere ' (virgoletta singola) quando viene usato con `SHIP`.

4. Dopo l'immissione dell'ultima riga il processo dovrebbe essere avviato. L'output restituito è simile al testo seguente:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come usare un'applicazione .NET Framework di hive e Pig in HDInsight. Per informazioni su come usare Python con Hive e Pig, vedere [Usare Python con Apache Hive e Apache Pig in HDInsight](python-udf-hdinsight.md).

Per altre modalità d'uso di hive e per informazioni sull'uso di MapReduce, vedere gli articoli seguenti:

* [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)
