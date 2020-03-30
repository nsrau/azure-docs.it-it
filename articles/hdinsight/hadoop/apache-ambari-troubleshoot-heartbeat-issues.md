---
title: Problemi di heartbeat di Apache Ambari in Azure HDInsight
description: Revisione di vari motivi per problemi di heartbeat di Apache Ambari in Azure HDInsightReview of various reasons for Apache Ambari heartbeat issues in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057074"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemi di heartbeat di Apache Ambari in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="scenario-high-cpu-utilization"></a>Scenario: utilizzo elevato della CPU

### <a name="issue"></a>Problema

L'agente Ambari ha un utilizzo elevato della CPU, che genera avvisi dall'interfaccia utente di Ambari che per alcuni nodi l'heartbeat dell'agente Ambari viene perso. L'avviso heartbeat lost è in genere temporaneo.

### <a name="cause"></a>Causa

A causa di vari bug ambari-agent, in rari casi, l'ambari-agent può avere un elevato (quasi 100) utilizzo della CPU percentuale.

### <a name="resolution"></a>Risoluzione

1. Identificare l'ID processo (pid) di ambari-agent:

    ```bash
    ps -ef | grep ambari_agent
    ```

1. Quindi eseguire il comando seguente per visualizzare l'utilizzo della CPU:

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Riavviare ambari-agent per ridurre il problema:

    ```bash
    service ambari-agent restart
    ```

1. Se il riavvio non funziona, interrompere il processo ambari-agent e quindi avviarlo:

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>Scenario: agente Ambari non avviato

### <a name="issue"></a>Problema

L'agente Ambari non è stato avviato e genera avvisi dall'interfaccia utente di Ambari che per alcuni nodi l'heartbeat dell'agente Ambari viene perso.

### <a name="cause"></a>Causa

Gli avvisi sono causati dall'agente Ambari non in esecuzione.

### <a name="resolution"></a>Risoluzione

1. Confermare lo stato dell'agente ambari:

    ```bash
    service ambari-agent status
    ```

1. Verificare se i servizi del controller di failover sono in esecuzione:Confirm if failover controller services are running:

    ```bash
    ps -ef | grep failover
    ```

    Se i servizi del controller di failover non sono in esecuzione, è probabile che ciò si sia verificato un problema che impedisce all'agente di failover di avviare il controller di failover. Controllare il log hdinsight-agent dal `/var/log/hdinsight-agent/hdinsight-agent.out` file.

## <a name="scenario-heartbeat-lost-for-ambari"></a>Scenario: Heartbeat perso per Ambari

### <a name="issue"></a>Problema

L'agente heartbeat Ambari è stato perso.

### <a name="cause"></a>Causa

I registri OMS causano un utilizzo elevato della CPU.

### <a name="resolution"></a>Risoluzione

* Disabilitare la registrazione di Monitoraggio di Azure usando il cmdlet [PowerShell Disable-AzHDInsightMonitoring.Disable](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) Azure Monitor logging using the Disable-AzHDInsightMonitoring PowerShell cmdlet.
* Eliminare `mdsd.warn` il file di registro

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
