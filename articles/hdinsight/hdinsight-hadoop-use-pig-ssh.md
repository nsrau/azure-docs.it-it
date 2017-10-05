---
title: Usare Pig di Hadoop con SSH in un cluster HDInsight - Azure | Microsoft Docs
description: Informazioni su come connettersi a un cluster Hadoop basato su Linux con SSH e quindi usare il comando Pig per eseguire istruzioni Pig Latin in modo interattivo o come processo batch.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b646a93b-4c51-4ba4-84da-3275d9124ebe
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e4c893ef4bfa573dd9fbc9c9b0ae296720769842
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="run-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Eseguire processi Pig in un cluster basato su Linux con il comando Pig (SSH)

[!INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Informazioni su come eseguire in modo interattivo i processi Pig da una connessione SSH al cluster HDInsight. Il linguaggio di programmazione Pig Latin consente di descrivere le trasformazioni applicate ai dati di input per produrre l'output desiderato.

> [!IMPORTANT]
> I passaggi descritti in questo documento richiedono un cluster HDInsight basato su Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Connettersi con SSH

Connettersi al cluster HDInsight usando SSH. L'esempio seguente si connette a un cluster denominato **myhdinsight** come account denominato **sshuser**:

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net

**Se è stata fornita una chiave del certificato per l'autenticazione SSH** durante la creazione del cluster HDInsight, potrebbe essere necessario specificare il percorso della chiave privata nel sistema client.

    ssh sshuser@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se è stata specificata una password per l'autenticazione SSH** durante la creazione del cluster HDInsight, fornire la password quando richiesto.

Per altre informazioni sull'uso di SSH con HDInsight, vedere l'articolo [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Usare il comando Pig

1. Una volta connessi, avviare l'interfaccia della riga di comando di Pig (CLI) tramite il comando seguente:

        pig

    Dopo qualche istante verrà visualizzato un prompt dei comandi `grunt>` .

2. Immettere la seguente istruzione:

        LOGS = LOAD '/example/data/sample.log';

    Questo comando carica i contenuti del file sample. log in LOGS. È possibile visualizzare i contenuti del file usando l'istruzione seguente:

        DUMP LOGS;

3. Successivamente, trasformare i dati applicando un'espressione regolare per estrarre solo il livello di registrazione da ogni record usando l'istruzione seguente:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    È possibile usare **DUMP** per visualizzare i dati dopo la trasformazione. In questo caso, usare `DUMP LEVELS;`.

4. Continuare ad applicare le trasformazioni usando le istruzioni presenti nella tabella seguente:

    | Istruzione Pig Latin | Funzionamento dell'istruzione |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Rimuove le righe che contengono un valore null per il livello di registrazione e memorizza i risultati in `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Raggruppa le righe in base al livello di registrazione e memorizza i risultati in `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Crea un set di dati che contiene ciascun valore univoco del livello di registrazione e il numero di occorrenze. Il set di dati viene archiviato in `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Ordina i livelli di registrazione in base al numero (decrescente) e memorizza i risultati in `RESULT`. |

    > [!TIP]
    > Usare `DUMP` per visualizzare il risultato della trasformazione dopo ogni passaggio.

5. È anche possibile salvare i risultati di una trasformazione usando l'istruzione `STORE` . Ad esempio, la seguente istruzione salva l'`RESULT` nella directory `/example/data/pigout` nella risorsa di archiviazione predefinita per il cluster:

        STORE RESULT into '/example/data/pigout';

   > [!NOTE]
   > I dati vengono memorizzati nella directory specificata nei file denominati `part-nnnnn`. Se la directory esiste già, si riceve un errore.

6. Per uscire dal prompt grunt, immettere la seguente istruzione:

        QUIT;

### <a name="pig-latin-batch-files"></a>File batch di Pig Latin

È inoltre possibile usare il comando Pig per eseguire processi Pig Latin contenuti in un file.

1. Dopo aver chiuso il prompt grunt, usare il seguente comando per indirizzare STDIN in un file denominato `pigbatch.pig`. Questo file viene creato nella home directory per l'account utente SSH.

        cat > ~/pigbatch.pig

2. Digitare o incollare le righe seguenti e quindi premere CTRL+D al termine.

        LOGS = LOAD '/example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

3. Usare l'istruzione seguente per eseguire il file `pigbatch.pig` tramite il comando Pig.

        pig ~/pigbatch.pig

    Al termine dell'esecuzione del processo batch, verrà visualizzato il seguente output:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Passaggi successivi

Per informazioni generali su sull'uso di Pig con HDInsight, vedere il documento seguente:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)

Per altre informazioni su come usare Hadoop con HDInsight, vedere i documenti seguenti:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

