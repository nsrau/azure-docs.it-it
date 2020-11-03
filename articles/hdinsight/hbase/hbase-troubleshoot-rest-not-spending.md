---
title: Apache HBase REST non risponde alle richieste in Azure HDInsight
description: Risolvere il problema con Apache HBase REST che non risponde alle richieste in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: c4a0ef82b951fa43eb4c58050d3148fd2d695026
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286978"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase REST non risponde alle richieste in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il servizio REST di Apache HBase non risponde alle richieste in Azure HDInsight.

## <a name="cause"></a>Causa

Questa possibile causa potrebbe essere la perdita di socket da parte del servizio REST di Apache HBase, che è particolarmente comune quando il servizio è in esecuzione per molto tempo (ad esempio, mesi). Dall'SDK del client, è possibile che venga visualizzato un messaggio di errore simile al seguente:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Soluzione

Riavviare HBase REST usando il comando seguente dopo SSHing all'host. È anche possibile usare le azioni script per riavviare il servizio in tutti i nodi del ruolo di lavoro:

```bash
sudo service hdinsight-hbrest restart
```

Questo comando arresterà il server di area HBase nello stesso host. È possibile avviare manualmente il server di area HBase tramite Ambari oppure lasciare che la funzionalità di riavvio automatico di Ambari ripristini automaticamente il server di area di HBase.

Se il problema persiste, è possibile installare il seguente script di mitigazione come processo CRON che viene eseguito ogni 5 minuti in ogni nodo di lavoro. Questo script di mitigazione esegue il ping del servizio REST e lo riavvia nel caso in cui il servizio REST non risponda.

```bash
#!/bin/bash
nc localhost 8090 < /dev/null
if [ $? -ne 0 ]
    then
    echo "RESTServer is not responding. Restarting"
    sudo /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh restart rest
fi
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]