---
title: Apache HBase REST not responding to requests in Azure HDInsight
description: Risolvere il problema con Apache HBase REST che non risponde alle richieste in Azure HDInsight.Resolve issue with Apache HBase REST not responding to requests in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 49b547829a369ea6df35e2f1c2f7d60458e41040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887173"
---
# <a name="scenario-apache-hbase-rest-not-responding-to-requests-in-azure-hdinsight"></a>Scenario: Apache HBase REST not responding to requests in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Il servizio REST Apache HBase non risponde alle richieste in Azure HDInsight.The Apache HBase REST service does not respond to requests in Azure HDInsight.

## <a name="cause"></a>Causa

La possibile causa potrebbe essere Apache HBase REST servizio è perdite di socket, che è particolarmente comune quando il servizio è stato in esecuzione per un lungo periodo di tempo (ad esempio, mesi). Dall'SDK del client, è possibile che venga visualizzato un messaggio di errore simile al:

```
System.Net.WebException : Unable to connect to the remote server --->
System.Net.Sockets.SocketException : A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond 10.0.0.19:8090
```

## <a name="resolution"></a>Risoluzione

Riavviare HBase REST usando il comando seguente dopo SSHing all'host. È inoltre possibile utilizzare le azioni di script per riavviare il servizio in tutti i nodi di lavoro:You can also use script actions to restart this service on all worker nodes:

```bash
sudo service hdinsight-hbrest restart
```

Questo comando arresterà server area HBase nello stesso host. È possibile avviare manualmente HBase Region Server tramite Ambari oppure consentire ad Ambari di riavviare automaticamente la funzionalità DiBase Region Server.

Se il problema persiste, è possibile installare lo script di attenuazione seguente come processo CRON che viene eseguito ogni 5 minuti su ogni nodo di lavoro. Questo script di attenuazione esegue il ping del servizio REST e lo riavvia nel caso in cui il servizio REST non risponda.

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

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
