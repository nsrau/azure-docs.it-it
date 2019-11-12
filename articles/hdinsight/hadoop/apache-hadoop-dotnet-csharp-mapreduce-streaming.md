---
title: Usare C# con MapReduce in Hadoop in HDInsight - Azure
description: Informazioni su come usare C# per creare soluzioni di MapReduce con Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: hrasheed
ms.openlocfilehash: 1cdf029d296bd6ff11b6531cd47dc6a7fd3163c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930289"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Usare C# con lo streaming di MapReduce su Apache Hadoop in HDInsight

Informazioni su come usare C# per creare una soluzione di MapReduce su HDInsight.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per ulteriori informazioni, vedere [Apache Hadoop Components on HDInsight](../hdinsight-component-versioning.md).

Apache Hadoop Streaming è un'utilità che consente di eseguire processi MapReduce tramite uno script o un eseguibile. In questo esempio, .NET è usato per implementare il mapper e il reducer per una soluzione di conteggio parole.

## <a name="net-on-hdinsight"></a>.NET su HDInsight

Per eseguire applicazioni .NET, i cluster *HDInsight basati su Linux* usano [Mono (https://mono-project.com)](https://mono-project.com)). La versione Mono 4.2.1 è inclusa nella versione 3.6 di HDInsight. Per altre informazioni sulla versione di mono inclusa in HDInsight, vedere [Apache Hadoop componenti disponibili con diverse versioni di HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions). 

Per altre informazioni sulla compatibilità Mono con le versioni di .NET Framework, vedere il documento relativo alla [compatibilità Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Come funziona lo streaming di Hadoop

Il processo di base usato per il flusso in questo documento è il seguente:

1. Hadoop passa i dati al mapper (*Mapper. exe* in questo esempio) su stdin.
2. Il mapper elabora i dati ed emette una coppia chiave/valore delimitata da tabulazione su STDOUT.
3. L'output viene letto da Hadoop e quindi passato al riduttore (*Reducer. exe* in questo esempio) su stdin.
4. Il riduttore legge le coppie chiave/valore delimitate da tabulazioni, elabora i dati e quindi genera il risultato come coppie chiave/valore delimitate da tabulazione su STDOUT.
5. L'output viene letto da Hadoop e scritto nella directory di output.

Per altre informazioni sullo streaming, vedere [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>prerequisiti

* Visual Studio.

* Una familiarità nello scrivere e nel compilare il codice C# destinato a .NET Framework 4.5.

* Un modo per caricare i file .exe sul cluster. La procedura in questo documento usa gli strumenti Data Lake per Visual Studio per caricare i file nell'archiviazione primaria per il cluster.

* Azure PowerShell o un client di Secure Shell (SSH).

* Un cluster Hadoop in HDInsight. Per altre informazioni sulla creazione di un cluster, vedere [Creare cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Creare il mapper

In Visual Studio creare una nuova applicazione console .NET Framework denominata *Mapper*. Usare il codice seguente per l'applicazione:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Dopo aver creato l'applicazione, compilarla per produrre il file */bin/debug/Mapper.exe* nella directory del progetto.

## <a name="create-the-reducer"></a>Creare il reducer

In Visual Studio creare una nuova applicazione console .NET Framework denominata *Reducer*. Usare il codice seguente per l'applicazione:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Dopo aver creato l'applicazione, compilarla per produrre il file */bin/debug/Reducer.exe* nella directory del progetto.

## <a name="upload-to-storage"></a>Caricare nella risorsa di archiviazione

Successivamente, è necessario caricare le applicazioni *Mapper* e *Reducer* nell'archivio HDInsight.

1. In Visual Studio scegliere **visualizza** > **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Se richiesto, immettere le credenziali della sottoscrizione di Azure e quindi selezionare **Accedi**.

4. Espandere il cluster HDInsight in cui si desidera distribuire l'applicazione. Viene elencata una voce con il testo **(Account di archiviazione predefinito)** .

   ![Account di archiviazione, cluster HDInsight, Esplora server, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Se è possibile espandere la voce **(account di archiviazione predefinito)** , si sta usando un **account di archiviazione di Azure** come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nella risorsa di archiviazione predefinita per il cluster, espandere la voce e quindi fare doppio clic sul **contenitore predefinito**.

   * Se non è possibile espandere la voce **(account di archiviazione predefinito)** , si sta usando **Azure Data Lake storage** come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, fare doppio clic sulla voce **(Account di archiviazione predefinito)** .

5. Per caricare i file con estensione .exe, usare uno dei metodi seguenti:

    * Se si usa un **account di archiviazione di Azure**, selezionare l'icona **Carica BLOB** . 

        ![Icona di caricamento HDInsight per Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Nella finestra di dialogo **Carica nuovo file** , in **nome file**, selezionare **Sfoglia**. Nella finestra di dialogo **Carica BLOB** passare alla cartella *bin\Debug* per il progetto *Mapper* , quindi scegliere il file *Mapper. exe* . Infine, selezionare **Apri** e quindi **OK** per completare il caricamento. 

    * Per **Azure Data Lake storage**, fare clic con il pulsante destro del mouse su un'area vuota nell'elenco dei file e quindi scegliere **carica**. Infine, selezionare il file *Mapper. exe* e quindi selezionare **Apri**.

    Una volta terminato il caricamento *mapper.exe*, ripetere il processo di caricamento per il file *reducer.exe*.

## <a name="run-a-job-using-an-ssh-session"></a>Eseguire un processo: uso di una sessione SSH

La procedura seguente descrive come eseguire un processo MapReduce usando una sessione SSH:

1. Connettersi al cluster HDInsight usando SSH. Eseguire ad esempio il comando `ssh sshuser@<clustername>-ssh.azurehdinsight.net`. Per altre informazioni, vedere [usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare uno dei comandi seguenti per avviare il processo MapReduce:

   * Se la risorsa di archiviazione predefinita è **archiviazione di Azure**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasb:///mapper.exe,wasb:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Se la risorsa di archiviazione predefinita è **Data Lake storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Se la risorsa di archiviazione predefinita è **Data Lake storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   Nell'elenco seguente vengono descritti gli elementi rappresentati da ogni parametro e opzione:

   * *Hadoop-Streaming. jar*: specifica il file jar che contiene la funzionalità di flusso MapReduce.
   * `-files`: specifica i file *Mapper.* exe e *Reducer. exe* per questo processo. La dichiarazione di protocollo `wasb:///`, `adl:///`o `abfs:///` prima di ogni file è il percorso della radice di archiviazione predefinita per il cluster.
   * `-mapper`: specifica il file che implementa il mapper.
   * `-reducer`: specifica il file che implementa il riduttore.
   * `-input`: specifica i dati di input.
   * `-output`: specifica la directory di output.

3. Al termine del processo del MapReduce, usare il comando seguente per visualizzare i risultati:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   L'elenco seguente è un esempio dei dati restituiti da questo comando:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Esecuzione di un processo: Uso di PowerShell

Usare il seguente script di PowerShell per eseguire un processo MapReduce e scaricare i risultati.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Questo script richiede l'account di accesso del cluster e la password, insieme al nome del cluster HDInsight. Al termine del processo, l'output viene scaricato in un file denominato *output. txt*. Il testo seguente è un esempio dei dati nel file `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di MapReduce con HDInsight, vedere [usare MapReduce in Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md).

Per informazioni sull'uso di C# con Hive e Pig, vedere [Usare le funzioni definite dall'utente C# con Apache Hive e Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Per informazioni sull'uso di C# con Storm in HDInsight, vedere [Sviluppare topologie C# per Apache Storm in HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
