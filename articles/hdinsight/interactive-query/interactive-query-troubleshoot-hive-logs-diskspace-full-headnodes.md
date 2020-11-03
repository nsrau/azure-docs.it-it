---
title: 'Risoluzione dei problemi: spazio su disco riempito da log Apache Hive-Azure HDInsight'
description: Questo articolo fornisce le procedure per la risoluzione dei problemi da seguire quando Apache Hive i log riempiono lo spazio su disco nei nodi head di Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 107ec012bf2ff76ee1cbe4c5f8252566a5a16127
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288925"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: i log di Apache Hive riempiono lo spazio su disco nei nodi head in Azure HDInsight

Questo articolo descrive le procedure per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi allo spazio su disco insufficiente nei nodi head nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

In un cluster Apache Hive/LLAP, i log indesiderati occupano l'intero spazio su disco nei nodi head. Questa condizione può causare i problemi seguenti:

- L'accesso SSH non riesce perché non è rimasto spazio sul nodo head.
- Ambari genera un *errore http: 503 servizio non disponibile*.
- Il riavvio di HiveServer2 Interactive non riesce.

`ambari-agent`Quando si verifica il problema, i log includeranno le voci seguenti:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Causa

Nelle configurazioni Advanced hive log4j la pianificazione dell'eliminazione predefinita corrente prevede l'eliminazione dei file più vecchi di 30 giorni, in base alla data dell'Ultima modifica.

## <a name="resolution"></a>Soluzione

1. Passare al riepilogo dei componenti hive nel portale di Ambari e selezionare la scheda **configs (configurazioni** ).

2. Passare alla `Advanced hive-log4j` sezione in **Impostazioni avanzate**.

3. Impostare il `appender.RFA.strategy.action.condition.age` parametro su un'età di propria scelta. In questo esempio l'età viene impostata su 14 giorni: `appender.RFA.strategy.action.condition.age = 14D`

4. Se non vengono visualizzate impostazioni correlate, aggiungere le impostazioni seguenti:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Impostare `hive.root.logger` su `INFO,RFA` , come illustrato nell'esempio seguente. L'impostazione predefinita è `DEBUG` , che rende i log di grandi dimensioni.

    ```
    # Define some default values that can be overridden by system properties
    hive.log.threshold=ALL
    hive.root.logger=INFO,RFA
    hive.log.dir=${java.io.tmpdir}/${user.name}
    hive.log.file=hive.log
    ```

6. Salvare le configurazioni e riavviare i componenti richiesti.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
