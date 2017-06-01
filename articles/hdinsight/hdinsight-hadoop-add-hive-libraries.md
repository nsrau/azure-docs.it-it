---
title: Aggiungere librerie Hive durante la creazione del cluster HDInsight | Documentazione Microsoft
description: Informazioni su come aggiungere librerie Hive (file con estensione jar) a un cluster HDInsight durante la creazione del cluster.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 2fd74b8d-c006-45c6-a9e2-72ff5d2d978a
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 6f6d7bf50878cc6938f19ba7f79f968f44f82ed6
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017

---
# <a name="add-custom-hive-libraries-when-creating-your-hdinsight-cluster"></a>Aggiungere librerie Hive personalizzate durante la creazione del cluster HDInsight

Se sono disponibili librerie di uso frequente con Hive in HDInsight, questo documento contiene informazioni sull'uso di un'azione script per precaricare le librerie durante la creazione del cluster. Le librerie aggiunte usando i passaggi in questo documento sono disponibili in modo globale in Hive, non è necessario utilizzare [ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) per caricarli.

## <a name="how-it-works"></a>Funzionamento

Quando si crea un cluster, è possibile specificare facoltativamente un'azione script che esegue uno script nei nodi del cluster durante la creazione. Lo script in questo documento accetta un singolo parametro, ovvero un percorso WASB che contiene le librerie (archiviate come file con estensione JAR) da precaricare.

Durante la creazione del cluster, lo script enumera i file, li copia nella directory `/usr/lib/customhivelibs/` nei nodi head e di lavoro, quindi li aggiunge alla proprietà `hive.aux.jars.path` nel file `core-site.xml`. Nei cluster basati su Linux, aggiorna anche il file `hive-env.sh` con il percorso dei file.

> [!NOTE]
> L'uso delle azioni script in questo articolo rende le librerie disponibili negli scenari seguenti:
>
> * **HDInsight basato su Linux**: quando si usa un client Hive, **WebHCat**, e **HiveServer2**.
> * **HDInsight basato su Windows**, quando si usa un client Hive e **WebHCat**.

## <a name="the-script"></a>Lo script

**Percorso dello script**

Per i **cluster basati su Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Per i **cluster basati su Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

**Requisiti**

* Gli script devono essere applicati ai **nodi head** e ai **nodi di lavoro**.

* I file con estensione jar da installare devono essere memorizzati nell'archivio BLOB di Azure in un **singolo contenitore**.

* L'account di archiviazione contenente la libreria dei file con estensione jar **deve** essere collegato al cluster HDInsight durante la creazione. Deve essere l'account di archiviazione predefinito o un account aggiunto tramite la __configurazione facoltativa__.

* Il percorso WASB al contenitore deve essere specificato come parametro dell'azione script. Ad esempio, se i file con estensione jar sono archiviati in un contenitore denominato **libs** in un account di archiviazione denominato **mystorage**, il parametro deve essere **wasbs://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]
  > In questo documento si presuppone che un account di archiviazione e un contenitore BLOB siano già stati creati e che i file siano stati caricati nel contenitore.
  >
  > Se non è stato creato un account di archiviazione, è possibile farlo tramite il [Portale di Azure](https://portal.azure.com). È quindi possibile usare un'utilità, ad esempio [Azure Storage Explorer](http://storageexplorer.com/) (Esplora archivi di Azure), per creare un contenitore nell'account e caricarvi i file.

## <a name="create-a-cluster-using-the-script"></a>Creare un cluster usando lo script

> [!NOTE]
> La procedura seguente consente di creare un cluster HDInsight basato su Linux. Per creare un cluster basato su Windows, selezionare **Windows** come sistema operativo del cluster durante la creazione del cluster e usare lo script di Windows (PowerShell) invece dello script bash.
>
> È anche possibile usare Azure PowerShell o HDInsight .NET SDK per creare un cluster con questo script. Per altre informazioni sull'uso di questi metodi, vedere [Personalizzare i cluster HDInsight con azioni script](hdinsight-hadoop-customize-cluster-linux.md).

1. Avviare il provisioning di un cluster seguendo i passaggi descritti in [Effettuare il provisioning di cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md) senza completarlo.

2. Nel pannello **Configurazione facoltativa** selezionare **Azioni script** e specificare le informazioni seguenti:

   * **NOME**: immettere un nome descrittivo per l'azione script.

   * **SCRIPT URI**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh

   * **HEAD**: selezionare questa opzione.

   * **LAVORO**: selezionare questa opzione.

   * **ZOOKEEPER**: lasciare vuoto questo campo.

   * **PARAMETRI**: immettere l'indirizzo WASB per l'account di archiviazione e il contenitore che contiene i file con estensione jar. Ad esempio, **wasbs://libs@mystorage.blob.core.windows.net/**.

3. Nella parte inferiore di **Azioni di script** usare il pulsante **Seleziona** per salvare la configurazione.

4. Nel pannello **Configurazione facoltativa** selezionare **Account di archiviazione collegati** e il collegamento **Aggiungi una chiave di archiviazione**. Selezionare l'account di archiviazione che contiene i file con estensione jar e quindi usare i pulsanti di **selezione** per salvare le impostazioni e tornare al pannello **Configurazione facoltativa**.

5. Usare il pulsante **Seleziona** nella parte inferiore del pannello **Configurazione facoltativa** per salvare le informazioni relative alla configurazione facoltativa.

6. Continuare il provisioning del cluster come descritto in [Effettuare il provisioning dei cluster HDInsight in Linux](hdinsight-hadoop-provision-linux-clusters.md).

Al termine della creazione del cluster, sarà possibile usare i file con estensione JAR aggiunti tramite questo script da Hive senza dover usare l'istruzione `ADD JAR`.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di Hive, vedere l'articolo relativo all' [uso di Hive con HDInsight](hdinsight-use-hive.md)

