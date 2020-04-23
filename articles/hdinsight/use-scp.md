---
title: Usare SCP con Apache Hadoop in Azure HDInsight
description: Questo documento fornisce informazioni sulla connessione a HDInsight con i comandi SSH e SCP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: bb90db8a15a872fc28b3d09183228a67edf290e3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103230"
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
* [HDInsight con Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Passaggi successivi

* [Usare SSH con HDInsight](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Usare nodi perimetrali in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node)
