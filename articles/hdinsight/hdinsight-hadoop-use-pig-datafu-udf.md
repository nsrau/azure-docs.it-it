---
title: Utilizzare DataFu con pig in HDInsight - Azure | Microsoft Docs
description: "DataFu è una raccolta di librerie da usare con Hadoop. Informazioni su come utilizzare DataFu con Pig sul cluster HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 511d47a8a73c42e5983150ba23ee4fcc7a9e3151
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Utilizzare DataFu con pig in HDInsight

Informazioni su come usare DataFu con HDInsight. DataFu è una raccolta di librerie open source per l'uso con Pig in Hadoop.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure.

* Un cluster Azure HDInsight (basato su Linux o su Windows)

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Nozioni di base di [utilizzo di Pig in HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Installare DataFu in HDInsight basato su Linux

> [!IMPORTANT]
> DataFu viene installato nei cluster basati su Linux versione 3.3 e versioni successive e nei cluster basati su Windows. Non viene installato nei cluster basati su Linux precedenti alla versione 3.3.
>
> Se si usa un cluster basato su Windows o un cluster basato su Linux versione 3.3 o successiva, ignorare questa sezione.

DataFu può essere scaricato e installato dall’archivio Maven. Utilizzare la procedura seguente per aggiungere DataFu al cluster di HDInsight:

1. Usare SSH per connettersi al cluster HDInsight basato su Linux. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Utilizzare il comando seguente per scaricare il file jar di DataFu tramite l'utilità wget, o copiare e incollare il collegamento nel browser per avviare il download.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. Successivamente, caricare il file all’archiviazione predefinita per il cluster HDInsight. Inserendo il file nell'archiviazione predefinita lo si rende disponibile a tutti i nodi del cluster.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > Con il comando precedente il file jar viene archiviato in `/example/jars`, poiché questa directory esiste già nell'archiviazione cluster. È possibile utilizzare qualsiasi percorso desiderato nell'archiviazione cluster HDInsight.

## <a name="use-datafu-with-pig"></a>Utilizzare DataFu con Pig

La procedura descritta in questa sezione presuppone che si abbia familiarità con l'uso di Pig in HDInsight. Per ulteriori informazioni sull'utilizzo di Pig con HDInsight, vedere [Usare Pig con HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> Se DataFu è stato installato manualmente seguendo la procedura descritta nella sezione precedente, è necessario registrarlo prima di poterlo usare.
>
> * Se il cluster usa l'archiviazione di Azure, usare un percorso `wasb://`. Ad esempio, `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> * Se il cluster usa Azure Data Lake Store, usare un percorso `adl://`. Ad esempio, `register adl://home/example/jars/datafu-1.2.0.jar`.

Per le funzioni DataFu viene spesso definito un alias. L'esempio seguente definisce un alias di `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

È quindi possibile usare l'alias in uno script Pig Latin per generare un hash per i dati di input. Il codice seguente, ad esempio, sostituisce la posizione nei dati di input con un valore hash:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Viene generato l'output seguente:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su DataFu o Pig, vedere i seguenti documenti:

* [Guida ad Apache Pig DataFu](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
