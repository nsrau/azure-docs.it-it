---
title: Apache Ambari UI 502 error in Azure HDInsight
description: Errore Apache Ambari UI 502 quando si tenta di accedere al cluster Azure HDInsightApache Ambari UI 502 error when you try to access your Azure HDInsight cluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 2b17c2488e47148e8845433f9c7613e1127fbffa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895765"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: Apache Ambari UI 502 error in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si tenta di accedere all'interfaccia utente di Apache Ambari per il cluster HDInsight, viene visualizzato un messaggio simile a: "502 - Server Web ha ricevuto una risposta non valida mentre si agisce come gateway o server proxy".

## <a name="cause"></a>Causa

In generale, il codice di stato HTTP 502 indica che il server Ambari non viene eseguito correttamente sul nodo head attivo. Ci sono alcune possibili cause principali.

## <a name="resolution"></a>Risoluzione

Nella maggior parte dei casi, per attenuare il problema, è possibile riavviare il nodo head attivo. In alternativa, scegliere una macchina virtuale di dimensioni maggiori per il nodo head.

### <a name="ambari-server-failed-to-start"></a>Avvio del server Ambari non riuscito

È possibile controllare i registri ambari-server per scoprire perché il server Ambari non è riuscito ad avviarsi. Un motivo comune è l'errore di verifica della coerenza del database. È possibile trovare questo fuori `/var/log/ambari-server/ambari-server-check-database.log`in questo file di registro: .

Se sono state apportate modifiche al nodo del cluster, annullarle. Usa sempre l'interfaccia utente di Ambari per modificare le configurazioni correlate ad Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Il server Ambari utilizza al 100% la CPU

In rare situazioni, abbiamo visto processo ambari-server ha quasi 100% di utilizzo della CPU costantemente. Come attenuazione, è possibile eseguire lo ssh fino al nodo head attivo e terminare il processo server Ambari e riavviarlo.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Server Ambari ucciso da oom-killer

In alcuni scenari, il nodo head esaurisce la memoria e l'assassino di Linux inizia a selezionare i processi da uccidere. È possibile verificare questa situazione eseguendo una ricerca nell'ID di processo AmbariServer, che non deve essere trovato. Poi guarda `/var/log/syslog`il tuo , e cerca qualcosa di simile a questo:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Quindi identificare quali processi stanno prendendo ricordi e cercare di ulteriore causa radice.

### <a name="other-issues-with-ambari-server"></a>Altri problemi con il server Ambari

Raramente il server Ambari non è in grado di gestire la richiesta in ingresso, è possibile trovare ulteriori informazioni esaminando i registri ambari-server per individuare eventuali errori. Uno di questi casi è un errore come questo:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
