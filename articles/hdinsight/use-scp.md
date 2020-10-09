---
title: Usare SCP con Apache Hadoop in Azure HDInsight
description: Questo documento fornisce informazioni sulla connessione a HDInsight con i comandi SSH e SCP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 44c87a3415a236f614510aa3163ad2e9cbd1f64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856178"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Usare SCP con Apache Hadoop in Azure HDInsight

Questo articolo fornisce informazioni su come trasferire in modo sicuro i file con il cluster HDInsight.

## <a name="copy-files"></a>Copiare i file

L'utilità `scp` può essere usata per copiare i file da e verso singoli nodi del cluster. Il comando seguente copia ad esempio la directory `test.txt` dal sistema locale al nodo head primario:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Poiché dopo `:` non è specificato alcun percorso, il file viene inserito nella home directory `sshuser`.

L'esempio seguente copia il file `test.txt` dalla home directory `sshuser` nel nodo head primario al sistema locale:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

`scp` può accedere solo al file system dei singoli nodi all'interno del cluster. Non può essere usato per accedere ai dati nella risorsa di archiviazione compatibile con HDFS per il cluster.

Usare `scp` quando è necessario caricare una risorsa per l'uso da una sessione SSH. Caricare ad esempio uno script Python e quindi eseguire lo script da una sessione SSH.

Per informazioni sul caricamento diretto dei dati nell'archivio compatibile con HDFS, vedere i documenti seguenti:

* [HDInsight con archiviazione di Azure](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight con Azure Data Lake storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare SSH con HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
