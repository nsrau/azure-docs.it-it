---
title: Monitorare le prestazioni di un cluster - Azure HDInsight
description: Come monitorare l'integrità e le prestazioni dei cluster Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 1da86e36cf20dc15152aea74be6c43a4cb43d3b4
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539769"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorare le prestazioni del cluster in Azure HDInsight

Monitorare l'integrità e le prestazioni di un cluster HDInsight è fondamentale per mantenere livelli ottimali di prestazioni e di utilizzo delle risorse. Il monitoraggio agevola l'individuazione e la risoluzione degli errori di configurazione del cluster e dei problemi del codice utente.

Le sezioni seguenti illustrano come monitorare e ottimizzare il carico nei cluster, le code Apache Hadoop YARN e come rilevare i problemi di limitazione del servizio di archiviazione.

## <a name="monitor-cluster-load"></a>Monitorare il carico del cluster

I cluster Hadoop offrono prestazioni ottimali quando il carico nel cluster viene distribuito in modo uniforme su tutti i nodi. In questo modo le attività di elaborazione vengono eseguite senza essere limitate da RAM, CPU o risorse del disco nei singoli nodi.

Per esaminare in modo dettagliato i nodi del cluster e il relativo caricamento, accedere all' [interfaccia utente Web di Ambariri](hdinsight-hadoop-manage-ambari.md), quindi selezionare la scheda **host** . Gli host sono elencati in base ai nomi di dominio completi. Lo stato operativo di ogni host viene visualizzato tramite un indicatore di integrità colorato:

| Colore | Descrizione |
| --- | --- |
| Rosso | Almeno un componente master dell'host è inattivo. Passare il mouse sull'indicatore per visualizzare una descrizione comando in cui sono elencati i componenti interessati. |
| Orange | Almeno un componente secondario nell'host è inattivo. Passare il mouse sull'indicatore per visualizzare una descrizione comando in cui sono elencati i componenti interessati. |
| Giallo | Il server Ambari non ha ricevuto un heartbeat dall'host per più di 3 minuti. |
| Green | Stato di esecuzione normale. |

Verrà visualizzata anche una serie di colonne in cui sono riportati il numero di core e la quantità di RAM per ogni host, nonché l'utilizzo del disco e il carico medio.

![Panoramica sulla scheda host Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Selezionare uno dei nomi host per visualizzare informazioni dettagliate sui componenti in esecuzione sull'host e le relative metriche. Le metriche vengono visualizzate come sequenze temporali selezionabili e riguardano l'utilizzo della CPU, il carico, l'utilizzo del disco, l'utilizzo della memoria, l'utilizzo della rete e il numero di processi.

![Panoramica dei dettagli dell'host Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Per informazioni dettagliate sull'impostazione degli avvisi e la visualizzazione delle metriche, vedere [Gestire i cluster HDInsight tramite l'utilizzo dell'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="yarn-queue-configuration"></a>Configurazione della coda YARN

Hadoop include vari servizi in esecuzione sulla relativa piattaforma distribuita. YARN (Yet Another Resource Negotiator) coordina tali servizi e alloca le risorse cluster per garantire la distribuzione uniforme dei carichi nel cluster.

YARN divide inoltre le due responsabilità del JobTracker (gestione delle risorse e pianificazione/monitoraggio dei processi) in due daemon: un ResourceManager globale e un ApplicationMaster per ogni applicazione.

ResourceManager è un' *utilità di pianificazione* ed esegue esclusivamente l'arbitraggio delle risorse disponibili tra le applicazioni concorrenti. Garantisce inoltre che tutte le risorse siano sempre in uso, ottimizzandole per varie costanti come i contratti di servizio, le garanzie di capacità e così via. ApplicationMaster negozia invece le risorse da ResourceManager e interagisce con NodeManager per eseguire e monitorare i contenitori e il relativo consumo di risorse.

Quando più tenant condividono un cluster di grandi dimensioni, c'è concorrenza per le risorse del cluster. CapacityScheduler è un'utilità di pianificazione collegabile che semplifica la condivisione delle risorse disponendo le richieste in coda. Il CapacityScheduler supporta anche le *Code gerarchiche* per garantire che le risorse vengano condivise tra le code secondarie di un'organizzazione, prima che le code di altre applicazioni possano utilizzare risorse gratuite.

YARN consente di allocare le risorse a queste code e indica se tutte le risorse disponibili sono assegnate. Per visualizzare informazioni sulle code, accedere all'interfaccia utente Web Ambari e quindi selezionare **YARN Queue Manager** (Gestore code YARN) dal menu principale.

![Gestione code YARN Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Sul lato sinistro della pagina YARN Queue Manager (Gestore code YARN) viene visualizzato un elenco di code, con la relativa percentuale di capacità assegnata.

![Pagina dei dettagli YARN Queue Manager (Gestore code YARN)](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Per un'analisi più approfondita delle code, nell'elenco a sinistra del dashboard Ambari selezionare il servizio **YARN** . Nel menu a discesa **Quick Links** (Collegamenti rapidi) selezionare **ResourceManager UI** (Interfaccia utente di ResourceManager) sotto il nodo attivo.

![Collegamenti menu dell'interfaccia utente Gestione risorse](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Nell'interfaccia utente di ResourceManager selezionare **Scheduler** (Utilità di pianificazione) dal menu a sinistra. Viene visualizzato un elenco delle code disponibili in *Application Queues* (Code dell'applicazione). In quest'area è possibile visualizzare la capacità usata per ognuna delle code, come vengono distribuiti i processi tra di esse e se i processi hanno risorse limitate.

![Menu dell'interfaccia utente Gestione risorse di Apache HAdoop](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Limitazione del servizio di archiviazione

È possibile che si verifichi un collo di bottiglia delle prestazioni del cluster a livello di archiviazione. Questo tipo di collo di bottiglia è spesso dovuto al *blocco* delle operazioni di input/output (i/o), che si verificano quando le attività in esecuzione inviano un maggior numero di operazioni di i/o rispetto al servizio di archiviazione Questo blocco crea una coda di richieste di I/O in attesa di essere elaborate al termine dell'elaborazione delle richieste di I/O correnti. I blocchi sono dovuti alla *limitazione dell'archiviazione* , che non è un limite fisico, ma piuttosto un limite imposto dal servizio di archiviazione da un contratto di servizio (SLA). Questo limite impedisce infatti che il servizio venga monopolizzato da un singolo client o tenant. Il contratto di contratto limita il numero di operazioni di i/o al secondo (IOPS) per archiviazione di Azure. per i dettagli, vedere [obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../storage/common/scalability-targets-standard-account.md).

Se si usa archiviazione di Azure, per informazioni sul monitoraggio dei problemi relativi all'archiviazione, inclusa la limitazione, vedere [monitorare, diagnosticare e risolvere i problemi archiviazione di Microsoft Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md).

Se l'archivio di backup del cluster è Azure Data Lake Storage (ADLS), la limitazione è probabilmente dovuta ai limiti della larghezza di banda. La limitazione, in questo caso, può essere identificata verificando la presenza di errori di limitazione nei log delle attività. Per Azure Data Lake Store, vedere la sezione sulla limitazione relativa al servizio desiderato in questi articoli:

* [Linee guida per l'ottimizzazione delle prestazioni di Apache Hive in HDInsight e di Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Linee guida per l'ottimizzazione delle prestazioni di MapReduce in HDInsight e di Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Linee guida per l'ottimizzazione delle prestazioni di Apache Storm in HDInsight e di Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Risolvere i problemi relativi alle prestazioni del nodo lente

In alcuni casi la lentezza può verificarsi a causa di spazio su disco insufficiente sul cluster. Esaminare i passaggi seguenti:

1. Usare il [comando ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi a ogni nodo.

1. Controllare l'utilizzo del disco eseguendo uno dei comandi seguenti:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Esaminare l'output e verificare la presenza di file di grandi dimensioni nella `mnt` cartella o in altre cartelle. In genere, le `usercache` `appcache` cartelle e (mnt/Resource/Hadoop/Yarn/local/usercache/hive/AppCache/) contengono file di grandi dimensioni.

1. Se sono presenti file di grandi dimensioni, un processo corrente sta causando la crescita del file o un processo precedente non riuscito potrebbe aver contribuito a questo problema. Per verificare se questo comportamento è causato da un processo corrente, eseguire questo comando: 

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Se questo comando indica un processo specifico, è possibile scegliere di terminare il processo usando un comando simile al seguente:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Sostituire `application_id` con l'ID applicazione. Se non sono indicati processi specifici, andare al passaggio successivo.

1. Al termine dell'esecuzione del comando o se non sono indicati processi specifici, eliminare i file di grandi dimensioni identificati eseguendo un comando simile al seguente:

    ```bash
    rm -rf filecache usercache
    ```

Per ulteriori informazioni sui problemi di spazio su disco, vedere [spazio su disco insufficiente](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Se si dispone di file di grandi dimensioni che si desidera gestire ma che contribuiscono al problema di spazio su disco insufficiente, è necessario scalare il cluster HDInsight e riavviare i servizi. Dopo aver completato questa procedura e aver atteso alcuni minuti, si noterà che la risorsa di archiviazione viene liberata e le normali prestazioni del nodo vengono ripristinate.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi e il monitoraggio dei cluster, visitare i collegamenti seguenti:

* [Analizzare i log di HDInsight](./hdinsight-troubleshoot-guide.md)
* [Eseguire il debug delle app con i log di Apache Hadoop YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Abilitare i dump dell'heap per i servizi Apache Hadoop in HDInsight basato su Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)