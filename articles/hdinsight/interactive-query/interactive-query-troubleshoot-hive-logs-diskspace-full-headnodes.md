---
title: Log di Apache Hive riempimento dello spazio su disco-Azure HDInsight
description: I log Apache Hive riempiono lo spazio su disco nei nodi head di Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78943958"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: i log di Apache Hive riempiono lo spazio su disco nei nodi head in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi allo spazio su disco insufficiente nei nodi head nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

In un cluster Apache Hive/LLAP, i log indesiderati occupano l'intero spazio su disco nei nodi head. A causa di ciò, è possibile che vengano visualizzati i problemi seguenti.

1. L'accesso SSH ha esito negativo a causa della mancanza di spazio sul nodo head.
2. Ambari restituisce un *errore http: 503 servizio non disponibile*.

Quando `ambari-agent` si verifica il problema, nei log verrà visualizzato quanto segue.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nelle configurazioni Advanced hive-log4j, il parametro *log4j. appender. RFA. MaxBackupIndex* viene omesso. Causa la generazione infinita di file di log.

## <a name="resolution"></a>Soluzione

1. Passare a hive Component Summary (Riepilogo componenti hive) nel portale `Configs` di Ambari e fare clic sulla scheda.

2. Passare alla `Advanced hive-log4j` sezione all'interno delle impostazioni avanzate.

3. Impostare `log4j.appender.RFA` il parametro come RollingFileAppender. 

4. Impostare `log4j.appender.RFA.MaxFileSize` e `log4j.appender.RFA.MaxBackupIndex` come indicato di seguito.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Impostare `hive.root.logger` su `INFO,RFA` come indicato di seguito. L'impostazione predefinita è DEBUG, che consente di aumentare le dimensioni dei log.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Salvare le configurazioni e riavviare i componenti richiesti.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
