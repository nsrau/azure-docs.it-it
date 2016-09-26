<properties
pageTitle="Utilizzare DataFu con Pig in HDInsight"
description="DataFu è una raccolta di librerie da usare con Hadoop. Informazioni su come utilizzare DataFu con Pig sul cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>  

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>  

#Utilizzare DataFu con pig in HDInsight

DataFu è una raccolta di librerie open source per l'utilizzo con Hadoop. In questo documento si apprenderà come utilizzare DataFu sul cluster HDInsight e come utilizzare DataFu User Defined Functions (UDF) con Pig.

##Prerequisiti

* Una sottoscrizione di Azure.

* Un cluster Azure HDInsight (basato su Linux o su Windows)

* Nozioni di base di [utilizzo di Pig in HDInsight](hdinsight-use-pig.md)

##Installare DataFu in HDInsight basato su Linux

> [AZURE.NOTE] DataFu viene installato nei cluster basati su Linux versione 3.3 e versioni successive e nei cluster basati su Windows. Non viene installato nei cluster basati su Linux precedenti alla versione 3.3.
>
> Se si usa un cluster basato su Linux versione 3.3 o successiva o un cluster basato su Windows, è possibile ignorare questa sezione.

DataFu può essere scaricato e installato dall’archivio Maven. Utilizzare la procedura seguente per aggiungere DataFu al cluster di HDInsight:

1. Usare SSH per connettersi al cluster HDInsight basato su Linux. Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Utilizzare il comando seguente per scaricare il file jar di DataFu tramite l'utilità wget, o copiare e incollare il collegamento nel browser per avviare il download.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Successivamente, caricare il file all’archiviazione predefinita per il cluster HDInsight. In questo modo si rende disponibile il file per tutti i nodi nel cluster e il file rimarrà nella risorsa di archiviazione anche se si elimina e ricrea il cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] Nell'esempio precedente si archivia il file jar in `wasbs:///example/jars` poiché la directory esiste già nell'archivio del cluster. È possibile utilizzare qualsiasi percorso desiderato nell'archiviazione cluster HDInsight.

##Utilizzare DataFu con Pig

I passaggi in questa sezione presuppongono che si abbia familiarità con l'utilizzo di Pig in HDInsight e fornisce solo le istruzioni Pig Latin, non i passaggi relativi al loro utilizzo con il cluster. Per ulteriori informazioni sull'utilizzo di Pig con HDInsight, vedere [Usare Pig con HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Quando si utilizza DataFu da Pig in un cluster HDInsight basato su Linux, è innanzitutto necessario registrare il file jar utilizzando la seguente istruzione Pig Latin:
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```  
>
> DataFu è registrato per impostazione predefinita nei cluster HDInsight basati su Windows.

In genere si definirà un alias per le funzioni DataFu. ad esempio:

    DEFINE SHA datafu.pig.hash.SHA();
    
Definisce un alias denominato `SHA` per la funzione di hashing di SHA. È quindi possibile utilizzarlo in uno script Pig Latin per generare un hash per i dati di input. Ad esempio, quanto segue sostituisce i nomi nei dati di input con un valore hash:

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Se si utilizza con i dati di input seguenti:

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Viene generato l'output seguente:

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##Passaggi successivi

Per altre informazioni su DataFu o Pig, vedere i seguenti documenti:

* [Guida ad Apache Pig DataFu](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Usare Pig con HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0914_2016-->