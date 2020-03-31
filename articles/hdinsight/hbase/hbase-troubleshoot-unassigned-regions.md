---
title: Problemi con i server di area in Azure HDInsight
description: Problemi con i server di area in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 85aeafb2c4461b50d399e40d9abff2ac04b677c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272760"
---
# <a name="issues-with-region-servers-in-azure-hdinsight"></a>Problemi con i server di area in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="scenario-unassigned-regions"></a>Scenario: aree non assegnate

### <a name="issue"></a>Problema

Quando `hbase hbck` si esegue il comando, viene visualizzato un messaggio di errore simile al seguente:

```
multiple regions being unassigned or holes in the chain of regions
```

Dall'interfaccia utente master di Apache HBase è possibile visualizzare il numero di aree non bilanciate in tutti i server di area. Si può quindi eseguire il comando `hbase hbck` per visualizzare i difetti nella catena di aree.

### <a name="cause"></a>Causa

I fori possono essere il risultato di aree offline.

### <a name="resolution"></a>Risoluzione

Correggere le assegnazioni. Seguire i passaggi seguenti per ripristinare lo stato normale delle aree non assegnate:

1. Accedere al cluster HDInsight HBase utilizzando SSH.

1. Eseguire `hbase zkcli` il comando per connettersi con la shell di .ooKeeper.

1. Eseguire `rmr /hbase/regions-in-transition` `rmr /hbase-unsecure/regions-in-transition` o eseguire il comando.

1. Uscire da zookeeper `exit` shell utilizzando il comando.

1. Aprire l'interfaccia utente di Apache Ambari e quindi riavviare il servizio HBase Master attivo.

1. Eseguire `hbase hbck` nuovamente il comando (senza ulteriori opzioni). Controllare l'output e assicurarsi che tutte le aree siano assegnate.

---

## <a name="scenario-dead-region-servers"></a>Scenario: server di area morta

### <a name="issue"></a>Problema

Non è stato possibile avviare i server dell'area geografica.

### <a name="cause"></a>Causa

Più directory WAL di divisione.

1. Ottenere l'elenco delle `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out`leggi attuali: .

1. Esaminare `wals.out` il file. Se sono presenti troppe directory di divisione (a partire dalla suddivisione z), è probabile che il server dell'area non riesca a causa di tali directory.

### <a name="resolution"></a>Risoluzione

1. Interrompere HBase dal portale Ambari.

1. Eseguire `hadoop fs -ls -R /hbase/WALs/ > /tmp/wals.out` per ottenere un nuovo elenco di WAL.

1. Spostare le directory di divisione `splitWAL`di z in una cartella temporanea, ed eliminare le directory di divisione.

1. Eseguire `hbase zkcli` il comando per connettersi con il guscio dello zookeeper.

1. Eseguire `rmr /hbase-unsecure/splitWAL`.

1. Riavviare il servizio HBase.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
