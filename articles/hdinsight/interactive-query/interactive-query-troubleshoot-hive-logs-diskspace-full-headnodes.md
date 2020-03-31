---
title: Apache Hive Logs filling up disk space - Azure HDInsight
description: The Apache Hive logs are filling up the disk space on the head nodes in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943958"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: i log di Apache Hive riempiono lo spazio su disco nei nodi Head in Azure HDInsight

Questo articolo descrive le procedure per la risoluzione dei problemi e le possibili soluzioni per problemi correlati a spazio su disco insufficiente nei nodi head nei cluster di Azure HDInsight.This article describes troubleshooting steps and possible resolutions for issues related to not enough disk nodes on the head nodes in Azure HDInsight clusters.

## <a name="issue"></a>Problema

In un cluster Apache Hive/LLAP, i log indesiderati occupano l'intero spazio su disco nei nodi head. A causa del quale, a seguito di problemi potrebbe essere visto.

1. Accesso SSH non riesce a causa di nessun a0> nel nodo head.
2. Ambari restituisce *ERRORE HTTP: 503 Servizio non disponibile*.

I `ambari-agent` registri mostrerebbero quanto segue quando si verifica il problema.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nelle configurazioni avanzate Hive-log4j, il parametro *log4j.appender.RFA.MaxBackupIndex* viene omesso. Provoca una generazione infinita di file di registro.

## <a name="resolution"></a>Risoluzione

1. Passare a Riepilogo componente Hive nel portale `Configs` Ambari e fare clic sulla scheda.

2. Vai alla `Advanced hive-log4j` sezione in Impostazioni avanzate.

3. Impostare `log4j.appender.RFA` il parametro come RollingFileAppender. 

4. `log4j.appender.RFA.MaxFileSize` Impostare `log4j.appender.RFA.MaxBackupIndex` e come segue.

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
5. `hive.root.logger` Impostare `INFO,RFA` su come segue. L'impostazione predefinita è DEBUG, che rende i registri diventano molto grandi.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Salvare le configurazioni e riavviare i componenti necessari.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
