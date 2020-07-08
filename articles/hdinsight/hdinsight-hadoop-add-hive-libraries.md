---
title: Librerie di Apache Hive durante la creazione del cluster-Azure HDInsight
description: Informazioni su come aggiungere librerie di Apache Hive (file jar) a un cluster HDInsight durante la creazione del cluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: c678372fbd54e528a8a16eacc601e815cfd32e58
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86082234"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Aggiungere librerie Apache Hive personalizzate durante la creazione del cluster HDInsight

Informazioni su come pre-caricare le librerie di [Apache hive](https://hive.apache.org/) in HDInsight. Questo documento contiene informazioni sull'uso di un'Azione Script per precaricare le librerie durante la creazione del cluster. Le librerie aggiunte usando i passaggi descritti in questo documento sono disponibili a livello globale in hive. non è necessario usare [Add jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) per caricarle.

## <a name="how-it-works"></a>Come funziona

Quando si crea un cluster, è possibile usare un'azione script per modificare i nodi del cluster quando vengono creati. Lo script in questo documento accetta un solo parametro, ovvero la posizione delle librerie. Questa posizione deve essere in un Account di archiviazione di Azure e le librerie devono essere archiviate come file con estensione jar.

Durante la creazione del cluster, lo script enumera i file, li copia nella directory `/usr/lib/customhivelibs/` nei nodi head e di lavoro, quindi li aggiunge alla proprietà `hive.aux.jars.path` nel file `core-site.xml`. Nei cluster basati su Linux, aggiorna anche il file `hive-env.sh` con il percorso dei file.

L'uso dell'azione script in questo articolo rende disponibili le librerie quando si usa un client hive per **WebHCat**e **HiveServer2**.

## <a name="the-script"></a>Lo script

**Percorso script**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Requisiti

* Gli script devono essere applicati ai **nodi head** e ai **nodi di lavoro**.

* I file con estensione jar da installare devono essere memorizzati nell'archivio BLOB di Azure in un **singolo contenitore**.

* L'account di archiviazione contenente la libreria dei file con estensione jar **deve** essere collegato al cluster HDInsight durante la creazione. Deve essere l'account di archiviazione predefinito o un account aggiunto tramite __le impostazioni dell'account di archiviazione__.

* Il percorso WASB al contenitore deve essere specificato come parametro dell'azione script. Ad esempio, se i file con estensione jar sono archiviati in un contenitore denominato **libs** in un account di archiviazione denominato **mystorage**, il parametro deve essere `wasbs://libs@mystorage.blob.core.windows.net/`.

  > [!NOTE]  
  > In questo documento si presuppone che un account di archiviazione e un contenitore BLOB siano già stati creati e che i file siano stati caricati nel contenitore.
  >
  > Se non è stato creato un account di archiviazione, è possibile farlo tramite il [Portale di Azure](https://portal.azure.com). È quindi possibile usare un'utilità, ad esempio [Azure Storage Explorer](https://storageexplorer.com/) (Esplora archivi di Azure), per creare un contenitore nell'account e caricarvi i file.

## <a name="create-a-cluster-using-the-script"></a>Creare un cluster usando lo script

1. Avviare il provisioning di un cluster usando la procedura descritta in effettuare il provisioning di [cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md), ma non completare il provisioning. È anche possibile usare Azure PowerShell o HDInsight .NET SDK per creare un cluster con questo script. Per altre informazioni sull'uso di questi metodi, vedere [Personalizzare i cluster HDInsight con azioni script](hdinsight-hadoop-customize-cluster-linux.md). Per il portale di Azure, nella scheda **configurazione e prezzi** selezionare l' **azione + Aggiungi script**.

1. Per l' **archiviazione**, se l'account di archiviazione contenente la libreria di file jar sarà diverso dall'account usato per il cluster, completare gli **account di archiviazione aggiuntivi**.

1. Per le **azioni script**, fornire le seguenti informazioni:

    |Proprietà |valore |
    |---|---|
    |Tipo di script|- Personalizzato|
    |Nome|Librerie |
    |URI script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Tipo/i di nodo|Head, ruolo di lavoro|
    |Parametri|immettere l'indirizzo WASB per l'account di archiviazione e il contenitore che contiene i file con estensione jar. Ad esempio: `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Per Apache Spark 2,1, usare questo URI dello script bash: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Continuare il provisioning del cluster come descritto in [Effettuare il provisioning dei cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md).

Al termine della creazione del cluster, è possibile usare i file jar aggiunti tramite questo script da hive senza dover usare l' `ADD JAR` istruzione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Hive, vedere [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
