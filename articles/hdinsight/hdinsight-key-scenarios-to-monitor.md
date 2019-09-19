---
title: Monitorare le prestazioni di un cluster - Azure HDInsight
description: Come monitorare l'integrità e le prestazioni dei cluster Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 7a7544ef9fe5724d1f6c11918411a76461d908e5
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104406"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorare le prestazioni del cluster in Azure HDInsight

Monitorare l'integrità e le prestazioni di un cluster HDInsight è fondamentale per mantenere livelli ottimali di prestazioni e di utilizzo delle risorse. Il monitoraggio agevola l'individuazione e la risoluzione degli errori di configurazione del cluster e dei problemi del codice utente.

Le sezioni seguenti illustrano come monitorare e ottimizzare il carico nei cluster, le code Apache Hadoop YARN e come rilevare i problemi di limitazione del servizio di archiviazione.

## <a name="monitor-cluster-load"></a>Monitorare il carico del cluster

I cluster Hadoop offrono prestazioni ottimali quando il carico nel cluster viene distribuito in modo uniforme su tutti i nodi. In questo modo le attività di elaborazione vengono eseguite senza essere limitate da RAM, CPU o risorse del disco nei singoli nodi.

Per una panoramica sui nodi del cluster e sul rispettivo caricamento, accedere all'[interfaccia utente Web Ambari](hdinsight-hadoop-manage-ambari.md) e selezionare la scheda **Host**. Gli host sono elencati in base ai relativi nomi di dominio completi. Lo stato operativo di ogni host viene visualizzato tramite un indicatore di integrità colorato:

| Colore | Descrizione |
| --- | --- |
| Rosso | Almeno un componente master dell'host è inattivo. Passare il mouse sull'indicatore per visualizzare una descrizione comando in cui sono elencati i componenti interessati. |
| Arancione | Almeno un componente secondario nell'host è inattivo. Passare il mouse sull'indicatore per visualizzare una descrizione comando in cui sono elencati i componenti interessati. |
| Giallo | Il server Ambari non riceve un heartbeat dall'host da più di 3 minuti. |
| Verde | Stato di esecuzione normale. |

Verrà visualizzata anche una serie di colonne in cui sono riportati il numero di core e la quantità di RAM per ogni host, nonché l'utilizzo del disco e il carico medio.

![Panoramica sulla scheda host Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Selezionare uno dei nomi host per visualizzare informazioni dettagliate sui componenti in esecuzione sull'host e le relative metriche. Le metriche vengono visualizzate come sequenze temporali selezionabili e riguardano l'utilizzo della CPU, il carico, l'utilizzo del disco, l'utilizzo della memoria, l'utilizzo della rete e il numero di processi.

![Panoramica dei dettagli dell'host Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Per informazioni dettagliate sull'impostazione degli avvisi e la visualizzazione delle metriche, vedere [Gestire i cluster HDInsight tramite l'utilizzo dell'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="yarn-queue-configuration"></a>Configurazione della coda YARN

Hadoop include vari servizi in esecuzione sulla relativa piattaforma distribuita. YARN (Yet Another Resource Negotiator) coordina tali servizi e alloca le risorse cluster per garantire la distribuzione uniforme dei carichi nel cluster.

YARN divide inoltre le due responsabilità del JobTracker (gestione delle risorse e pianificazione/monitoraggio dei processi) in due daemon: un ResourceManager globale e un ApplicationMaster per ogni applicazione.

ResourceManager è un'*utilità di pianificazione* ed esegue esclusivamente l'arbitraggio delle risorse disponibili tra le applicazioni concorrenti. Garantisce inoltre che tutte le risorse siano sempre in uso, ottimizzandole per varie costanti come i contratti di servizio, le garanzie di capacità e così via. ApplicationMaster negozia invece le risorse da ResourceManager e interagisce con NodeManager per eseguire e monitorare i contenitori e il relativo consumo di risorse.

Quando più tenant condividono un cluster di grandi dimensioni, si verifica una condizione di concorrenza per le risorse del cluster. CapacityScheduler è un'utilità di pianificazione collegabile che semplifica la condivisione delle risorse disponendo le richieste in coda. CapacityScheduler supporta anche *code gerarchiche* con cui garantisce che le risorse vengano condivise tra le code secondarie di un'organizzazione prima che alle code di altre applicazioni sia concessa la possibilità di usare le risorse libere.

YARN consente di allocare le risorse a queste code e indica se tutte le risorse disponibili sono assegnate. Per visualizzare informazioni sulle code, accedere all'interfaccia utente Web Ambari e quindi selezionare **YARN Queue Manager** (Gestore code YARN) dal menu principale.

![Gestione code YARN Apache Ambari](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Sul lato sinistro della pagina YARN Queue Manager (Gestore code YARN) viene visualizzato un elenco di code, con la relativa percentuale di capacità assegnata.

![Pagina dei dettagli YARN Queue Manager (Gestore code YARN)](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Per un'analisi più approfondita delle code, nell'elenco a sinistra del dashboard Ambari selezionare il servizio **YARN**. Nel menu a discesa **Quick Links** (Collegamenti rapidi) selezionare **ResourceManager UI** (Interfaccia utente di ResourceManager) sotto il nodo attivo.

![Collegamenti menu dell'interfaccia utente Gestione risorse](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

Nell'interfaccia utente di ResourceManager selezionare **Scheduler** (Utilità di pianificazione) dal menu a sinistra. Viene visualizzato un elenco delle code disponibili in *Application Queues* (Code dell'applicazione). In quest'area è possibile visualizzare la capacità usata per ognuna delle code, come vengono distribuiti i processi tra di esse e se i processi hanno risorse limitate.

![Menu dell'interfaccia utente Gestione risorse di Apache HAdoop](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Limitazione del servizio di archiviazione

È possibile che si verifichi un collo di bottiglia delle prestazioni del cluster a livello di archiviazione. Questo tipo di collo di bottiglia è spesso causato dal *blocco* delle operazioni di input/output, che si verifica quando le attività in esecuzione inviano più richieste di I/O di quante possa gestirne il servizio di archiviazione. Questo blocco crea una coda di richieste di I/O in attesa di essere elaborate al termine dell'elaborazione delle richieste di I/O correnti. I blocchi sono generati quindi da una *limitazione del servizio di archiviazione*, che non è un limite fisico, ma un limite imposto dal servizio di archiviazione in base a un contratto di servizio. Questo limite impedisce infatti che il servizio venga monopolizzato da un singolo client o tenant. Il contratto di servizio limita il numero di I/O al secondo (IOPS) consentiti in Archiviazione di Azure: per informazioni dettagliate, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Se si usa Archiviazione di Azure, per informazioni sul monitoraggio dei problemi correlati all'archiviazione e alla limitazione, vedere [Monitorare, diagnosticare e risolvere i problemi dell'Archiviazione di Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Se l'archivio di backup del cluster è Azure Data Lake Storage, la limitazione è determinata probabilmente da limiti di larghezza di banda. La limitazione, in questo caso, può essere identificata verificando la presenza di errori di limitazione nei log delle attività. Per Azure Data Lake Store, vedere la sezione sulla limitazione relativa al servizio desiderato in questi articoli:

* [Linee guida per l'ottimizzazione delle prestazioni di Apache Hive in HDInsight e di Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Linee guida per l'ottimizzazione delle prestazioni di MapReduce in HDInsight e di Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Linee guida per l'ottimizzazione delle prestazioni di Apache Storm in HDInsight e di Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi e il monitoraggio dei cluster, visitare i collegamenti seguenti:

* [Analizzare i log di HDInsight](hdinsight-debug-jobs.md)
* [Eseguire il debug delle app con i log di Apache Hadoop YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Abilitare i dump dell'heap per i servizi Apache Hadoop in HDInsight basato su Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
