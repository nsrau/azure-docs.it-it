---
title: Problemi di heartbeat di Apache Ambari in Azure HDInsight
description: Revisione dei vari motivi per i problemi di heartbeat di Apache Ambari in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/02/2019
ms.openlocfilehash: 0b20bb5265dd5fa139a691fcb21aaa1795ff352c
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735976"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Problemi di heartbeat di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="scenario-high-cpu-utilization"></a>Scenario: Utilizzo CPU elevato

### <a name="issue"></a>Problema

L'agente Ambari ha un utilizzo elevato della CPU, che genera avvisi dall'interfaccia utente di Ambari che per alcuni nodi viene perso l'heartbeat dell'agente Ambari.

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

## <a name="scenario-ambari-agent-not-started"></a>Scenario: Agente Ambari non avviato

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

    Se i servizi del controller di failover non sono in esecuzione, è probabile che a causa di un problema si impedisca a HDInsight-Agent di avviare il controller di failover. Controllare il log HDInsight-Agent `/var/log/hdinsight-agent/hdinsight-agent.out` dal file.

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
