---
title: Problemi di heartbeat di Apache Ambari in Azure HDInsight
description: Revisione dei vari motivi per i problemi di heartbeat di Apache Ambari in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: adc5dfcef8cce269b6b6d982178433b8ee163f92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998270"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemi di heartbeat di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="scenario-high-cpu-utilization"></a>Scenario: utilizzo CPU elevato

### <a name="issue"></a>Problema

L'agente Ambari ha un utilizzo elevato della CPU, che genera avvisi dall'interfaccia utente di Ambari che per alcuni nodi viene perso l'heartbeat dell'agente Ambari. L'avviso di heartbeat perso è in genere temporaneo.

### <a name="cause"></a>Causa

A causa dei diversi bug di Ambari Agent, in rari casi, l'agente Ambari può avere una percentuale di utilizzo della CPU elevata (vicina a 100).

### <a name="resolution"></a>Risoluzione

1. Identificare l'ID processo (PID) di Ambari-Agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Quindi eseguire il comando seguente per visualizzare l'utilizzo della CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Riavviare Ambari-Agent per attenuare il problema:

    ```bash
    service ambari-agent restart
    ```

1. Se il riavvio non funziona, terminare il processo Ambari-Agent e avviarlo:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: agente Ambari non avviato

### <a name="issue"></a>Problema

L'agente Ambari non è stato avviato e genera avvisi dall'interfaccia utente di Ambari che per alcuni nodi viene perso l'heartbeat dell'agente Ambari.

### <a name="cause"></a>Causa

Gli avvisi sono causati dall'agente Ambari non in esecuzione.

### <a name="resolution"></a>Risoluzione

1. Confermare lo stato di Ambari-Agent:

    ```bash
    service ambari-agent status
    ```

1. Verificare se i servizi del controller di failover sono in esecuzione:

    ```bash
    ps -ef | grep failover
    ```

    Se i servizi del controller di failover non sono in esecuzione, probabilmente a causa di un problema impedire a HDInsight-Agent di avviare il controller di failover. Controllare il log HDInsight-Agent dal `/var/log/hdinsight-agent/hdinsight-agent.out` file.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: heartbeat perso per Ambari

### <a name="issue"></a>Problema

L'agente heartbeat Ambari è andato perso.

### <a name="cause"></a>Causa

I log di OMS causano un utilizzo elevato della CPU.

### <a name="resolution"></a>Risoluzione

* Disabilitare la registrazione di monitoraggio di Azure usando il cmdlet di PowerShell [Disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) .
* Eliminare il `mdsd.warn` file di log

---

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]