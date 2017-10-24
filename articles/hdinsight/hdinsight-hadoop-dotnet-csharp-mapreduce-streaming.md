---
title: Usare C# con MapReduce in Hadoop in HDInsight - Azure | Microsoft Docs
description: Informazioni su come usare C# per creare soluzioni di MapReduce con Hadoop in HDInsight di Azure.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2017
ms.author: larryfr
ms.openlocfilehash: 96f0be021a2b362c702f91ec4b532df8a334529f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Usare C# con lo streaming di MapReduce su Hadoop in HDInsight

Informazioni su come usare C# per creare una soluzione di MapReduce su HDInsight.

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md).

Hadoop streaming è un'utilità che consente di eseguire processi MapReduce tramite uno script o eseguibile. In questo esempio, .NET è usato per implementare il mapper e il reducer per una soluzione di conteggio parole.

## <a name="net-on-hdinsight"></a>.NET su HDInsight

__I cluster HDInsight basati su Linux__ usano [Mono (https://mono-project.com)](https://mono-project.com) per eseguire le applicazioni .NET. La versione Mono 4.2.1 è inclusa nella versione 3.5 di HDInsight. Per altre informazioni sulla versione Mono compresa in HDInsight, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](hdinsight-component-versioning.md). Per usare una versione specifica di Mono, vedere il documento [Install or update Mono](hdinsight-hadoop-install-mono.md) (Installare o aggiornare Mono).

Per altre informazioni sulla compatibilità Mono con le versioni di .NET Framework, vedere il documento relativo alla [compatibilità Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Come funziona lo streaming di Hadoop

Il processo di base usato per il flusso in questo documento è il seguente:

1. Hadoop passa i dati al mapper (mapper.exe in questo esempio) su STDIN.
2. Il mapper elabora i dati ed emette una coppia chiave/valore delimitata da tabulazione su STDOUT.
3. L'output viene letto da Hadoop e quindi passato al riduttore (reducer.exe in questo esempio) su STDIN.
4. Il riduttore legge le coppie chiave/valore delimitate da tabulazioni, elabora i dati e quindi genera il risultato come coppie chiave/valore delimitate da tabulazione su STDOUT.
5. L'output viene letto da Hadoop e scritto nella directory di output.

Per altre informazioni sui flussi, vedere il documento [Hadoop Streaming (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Prerequisiti

* Una familiarità nello scrivere e nel compilare il codice C# destinato a .NET Framework 4.5. Nella procedura di questo documento viene usato Visual Studio 2017.

* Un modo per caricare i file .exe sul cluster. La procedura in questo documento usa gli strumenti Data Lake per Visual Studio per caricare i file nell'archiviazione primaria per il cluster.

* Azure PowerShell o un client SSH.

* Un cluster Hadoop in HDInsight. Per altre informazioni sulla creazione di un cluster, vedere [Creare cluster Hadoop in HDInsight](hdinsight-provision-clusters.md).

## <a name="create-the-mapper"></a>Creare il mapper

In Visual Studio creare una nuova __applicazione console__ denominata __mapper__. Usare il codice seguente per l'applicazione:

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

Dopo aver creato l'applicazione, compilarla per produrre il file `/bin/Debug/mapper.exe` nella directory del progetto.

## <a name="create-the-reducer"></a>Creare il reducer

In Visual Studio creare una nuova __applicazione console__ denominata __reducer__. Usare il codice seguente per l'applicazione:

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

Dopo aver creato l'applicazione, compilarla per produrre il file `/bin/Debug/reducer.exe` nella directory del progetto.

## <a name="upload-to-storage"></a>Caricare nella risorsa di archiviazione

1. In Visual Studio aprire **Esplora server**.

2. Espandere **Azure** e quindi **HDInsight**.

3. Se richiesto, immettere le credenziali della sottoscrizione di Azure, quindi fare clic su **Accedi**.

4. Espandere il cluster HDInsight in cui si desidera distribuire l'applicazione. Viene elencata una voce con il testo __(Account di archiviazione predefinito)__.

    ![Esplora server con account di archiviazione per il cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Se è possibile espandere questa voce, si usa un __Account di archiviazione di Azure__ come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, espandere la voce e quindi fare doppio clic su __(Contenitore predefinito)__.

    * Se non è possibile espandere questa voce, si usa un __Azure Data Lake Store__ come risorsa di archiviazione predefinita per il cluster. Per visualizzare i file nel percorso di archiviazione predefinito per il cluster, fare doppio clic sulla voce __(Account di archiviazione predefinito)__.

5. Per caricare i file con estensione .exe, usare uno dei metodi seguenti:

    * Se si usa un __Account di Archiviazione di Azure__, fare clic sull'icona per il caricamento, quindi passare alla cartella **bin\debug** per il progetto **mapper**. Selezionare infine il file **mapper.exe** e fare clic su **Ok**.

        ![icona relativa al caricamento](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Se si usa __Azure Data Lake Store__, fare doppio clic su un'area vuota nell'elenco di file e quindi selezionare __Carica__. Selezionare infine il file **mapper.exe** e fare clic su **Apri**.

    Una volta terminato il caricamento __mapper.exe__, ripetere il processo di caricamento per il file __reducer.exe__.

## <a name="run-a-job-using-an-ssh-session"></a>Eseguire un processo: uso di una sessione SSH

1. Connettersi al cluster HDInsight usando SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Usare uno dei comandi seguenti per avviare il processo MapReduce:

    * Se si usa __Azure Data Lake Store__ come risorsa di archiviazione predefinita:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Se si usa __Archiviazione di Azure__ come risorsa di archiviazione predefinita:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    L'elenco seguente descrive le operazioni eseguite da ogni parametro:

    * `hadoop-streaming.jar`: il file con estensione jar che contiene la funzionalità di streaming MapReduce.
    * `-files`: aggiunge i file `mapper.exe` e `reducer.exe` a questo processo. `adl:///` o `wasb:///` prima di ogni file rappresenta il percorso della radice di archiviazione predefinita per il cluster.
    * `-mapper`: specifica il file che implementa il mapper.
    * `-reducer`: specifica il file che implementa il reducer.
    * `-input`: dati di input.
    * `-output`: directory di output.

3. Dopo il completamento del processo di MapReduce, usare il comando seguente per visualizzare i risultati:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    L'elenco seguente è un esempio dei dati restituiti da questo comando:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Esecuzione di un processo: Uso di PowerShell

Usare il seguente script di PowerShell per eseguire un processo MapReduce e scaricare i risultati.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Questo script richiede l'account di accesso del cluster e la password, insieme al nome del cluster HDInsight. Al termine del processo, l'output viene scaricato in un file denominato `output.txt`. Il testo seguente è un esempio dei dati nel file `output.txt`:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di MapReduce con HDInsight, vedere l'articolo [Usare MapReduce in Hadoop su HDInsight](hdinsight-use-mapreduce.md).

Per informazioni sull'uso di C# con Hive e Pig, vedere [Usare le funzioni definite dall'utente C# con Hive e Pig](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md).

Per informazioni sull'uso di C# con Storm in HDInsight tramite, vedere [Sviluppare topologie C# per Apache Storm in HDInsight tramite gli strumenti Hadoop per Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).