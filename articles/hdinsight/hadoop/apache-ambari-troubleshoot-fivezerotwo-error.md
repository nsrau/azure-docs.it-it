---
title: Errore 502 dell'interfaccia utente di Apache Ambari in Azure HDInsight
description: Errore di Apache Ambari UI 502 quando si tenta di accedere al cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 8e911fcb68fb318ce0a9c1178eee1c7d126728e0
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810925"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: Errore 502 dell'interfaccia utente di Apache Ambari in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si prova ad accedere all'interfaccia utente di Apache Ambari per il cluster HDInsight, viene ricevuto un messaggio simile a: "502-risposta non valida ricevuta dal server Web in funzione come server proxy o gateway".

## <a name="cause"></a>Causa

In generale, il codice di stato HTTP 502 indica che il server Ambari non è in esecuzione correttamente nel nodo head attivo. Esistono alcune possibili cause principali.

## <a name="resolution"></a>Risoluzione

Nella maggior parte dei casi, per attenuare il problema, è possibile riavviare il nodo head attivo. In alternativa, scegliere una dimensione di macchina virtuale più grande per la nodo head.

### <a name="ambari-server-failed-to-start"></a>Non è stato possibile avviare il server Ambari

È possibile controllare i log di Ambari-server per scoprire perché il server Ambari non è stato avviato. Un motivo comune è l'errore di verifica coerenza del database. È possibile trovare questo file di log: `/var/log/ambari-server/ambari-server-check-database.log`.

Se sono state apportate modifiche al nodo del cluster, annullarle. Usare sempre l'interfaccia utente di Ambari per modificare qualsiasi configurazione correlata a Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Server Ambari con utilizzo CPU del 100%

In rare situazioni, abbiamo visto che il processo Ambari-server ha un utilizzo della CPU quasi al 100% costante. Come mitigazione, è possibile connettersi tramite SSH al nodo head attivo e terminare il processo del server Ambari e avviarlo di nuovo.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Server Ambari terminato da memoria insufficiente

In alcuni scenari, la memoria di nodo head esaurisce la memoria e la memoria di Linux è iniziata a selezionare i processi da terminare. Per verificare questa situazione, è possibile cercare l'ID del processo AmbariServer, che non è stato trovato. Esaminare il `/var/log/syslog`e cercare un risultato simile al seguente:

```
Jul 27 15:29:30 hn0-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Individuare quindi i processi che assumono le memorie e provare a causarne ulteriormente la causa principale.

### <a name="other-issues-with-ambari-server"></a>Altri problemi relativi al server Ambari

Raramente il server Ambari non è in grado di gestire la richiesta in ingresso, è possibile trovare altre informazioni esaminando i log di Ambari-server per eventuali errori. Uno di questi casi è un errore simile al seguente:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
