---
title: Problemi con i server di area in Azure HDInsight
description: Problemi con i server di area in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/16/2019
ms.openlocfilehash: 6e734a661557b024257fcd1b9d9c2da6a3bc8f85
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640234"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemi con i server di area in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="scenario-unassigned-regions"></a>Scenario: Aree non assegnate

### <a name="issue"></a>Problema

Quando si `hbase hbck` esegue il comando, viene visualizzato un messaggio di errore simile al seguente:

```
multiple regions being unassigned or holes in the chain of regions
```

Dall'interfaccia utente di Apache HBase Master è possibile visualizzare il numero di aree sbilanciate in tutti i server di area. Si può quindi eseguire il comando `hbase hbck` per visualizzare i difetti nella catena di aree.

### <a name="cause"></a>Causa

I buchi possono essere il risultato di aree offline.

### <a name="resolution"></a>Risoluzione

Correggere le assegnazioni. Seguire i passaggi seguenti per ripristinare lo stato normale delle aree non assegnate:

1. Accedere al cluster HBase di HDInsight con SSH.

1. Eseguire `hbase zkcli` il comando per connettersi con la shell ZooKeeper.

1. Eseguire `rmr /hbase/regions-in-transition` il `rmr /hbase-unsecure/regions-in-transition` comando o.

1. Uscire dalla shell Zookeeper usando `exit` il comando.

1. Aprire l'interfaccia utente di Apache Ambari e quindi riavviare il servizio HBase Master attivo.

1. Eseguire `hbase hbck` di nuovo il comando (senza ulteriori opzioni). Controllare l'output e assicurarsi che tutte le aree siano assegnate.

---

## <a name="scenario-dead-region-servers"></a>Scenario: Server di aree non recapitabili

### <a name="issue"></a>Problema

Impossibile avviare i server di area.

### <a name="cause"></a>Causa

Più directory WAL di suddivisione.

1. Ottiene l'elenco di WALs corrente `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`:.

1. Esaminare il `wals.out` file. Se sono presenti troppe directory di suddivisione (a partire dalla suddivisione *), è probabile che il server di area non riesca a causa di queste directory.

### <a name="resolution"></a>Risoluzione

1. Arrestare HBase dal portale di Ambari.

1. Eseguire `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` per ottenere un nuovo elenco di WALs.

1. Spostare le directory di suddivisione * in una cartella `splitWAL`temporanea ed eliminare le directory di suddivisione *.

1. Eseguire `hbase zkcli` il comando per connettersi con la shell Zookeeper.

1. Eseguire `rmr /hbase-unsecure/splitWAL`.

1. Riavviare il servizio HBase.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
