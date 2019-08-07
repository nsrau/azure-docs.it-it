---
title: Apache HBase REST non risponde alle richieste in Azure HDInsight
description: Risolvere il problema con HDInsight HBase REST che non risponde alle richieste
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/01/2019
ms.openlocfilehash: 7219f66457e47bba34e750ec74810b8d2edee36e
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68817108"
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

## <a name="resolution"></a>Risoluzione

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

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
