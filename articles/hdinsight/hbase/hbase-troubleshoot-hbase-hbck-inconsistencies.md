---
title: hbase hbck restituisce le incoerenze in Azure HDInsightHbase hbck returns inconsistencies in Azure HDInsight
description: hbase hbck restituisce le incoerenze in Azure HDInsightHbase hbck returns inconsistencies in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: fa02ac0dfe229f3e82d1c1c62d83ca06a81efca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887326"
---
# <a name="scenario-hbase-hbck-command-returns-inconsistencies-in-azure-hdinsight"></a>Scenario: `hbase hbck` il comando restituisce incoerenze in Azure HDInsightScenario: command returns inconsistencies in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue-region-is-not-in-hbasemeta"></a>Problema: l'area geografica non è in`hbase:meta`

Area xxx in HDFS, `hbase:meta` ma non elencata o distribuita in alcun server di area.

### <a name="cause"></a>Causa

Variabile.

### <a name="resolution"></a>Risoluzione

1. Correggere la metatabella eseguendo:

    ```
    hbase hbck -ignorePreCheckPermission –fixMeta
    ```

1. Assegnare aree a ServerArea eseguendo:Assign regions to RegionServers by running:

    ```
    hbase hbck -ignorePreCheckPermission –fixAssignment
    ```
---

## <a name="issue-region-is-offline"></a>Problema: l'area è offline

Regione xxx non distribuita in alcun ServerArea. Ciò significa che `hbase:meta`l'area è in , ma non in linea.

### <a name="cause"></a>Causa

Variabile.

### <a name="resolution"></a>Risoluzione

Portare le regioni online eseguendo:

```
hbase hbck -ignorePreCheckPermission –fixAssignment
```

---

## <a name="issue-regions-have-the-same-startend-keys"></a>Problema: le aree hanno le stesse chiavi di inizio/fineIssue: Regions have the same start/end keys

### <a name="cause"></a>Causa

Variabile.

### <a name="resolution"></a>Risoluzione

Unire manualmente le aree sovrapposte. Vai alla tabella HBase HMaster Web UI sezione, selezionare il collegamento alla tabella, che presenta il problema. Verrà visualizzata la chiave iniziale/chiave finale di ogni area appartenente a tale tabella. Quindi unire le aree sovrapposte. Nella shell HBase, fare `merge_region 'xxxxxxxx','yyyyyyy', true`. Ad esempio:

```
RegionA, startkey:001, endkey:010,

RegionB, startkey:001, endkey:080,

RegionC, startkey:010, endkey:080.
```

In questo scenario, è necessario unire RegionA e RegionC e ottenere RegionD con lo stesso intervallo di chiavi di RegionB, quindi unire RegionB e RegionD. xxxxxxx e yyyyy sono la stringa hash alla fine del nome di ogni area. Fare attenzione a non unire due regioni discontinue. Dopo ogni unione, ad esempio merge A e C, HBase avvierà una compattazione in RegionD.After each merge, like merge A and C, HBase will start a compaction on RegionD. Attendere il completamento della compattazione prima di eseguire un'altra unione con RegionD. È possibile trovare lo stato di compattazione in tale pagina del server di area nell'interfaccia utente HBase HMaster.You can find the reaction status on that region server page in HBase HMaster UI.

---

## <a name="issue-cant-load-regioninfo"></a>Problema: impossibile caricare`.regioninfo`

Impossibile caricare `.regioninfo` per `/hbase/data/default/tablex/regiony`l'area .

### <a name="cause"></a>Causa

Ciò è probabilmente dovuto all'eliminazione parziale dell'area quando RegionServer si arresta in modo anomalo o la macchina virtuale viene riavviata. Attualmente, Archiviazione di Azure è un file system BLOB flat e alcune operazioni sui file non sono atomiche.

### <a name="resolution"></a>Risoluzione

Pulire manualmente i file e le cartelle rimanenti:

1. Esegui `hdfs dfs -ls /hbase/data/default/tablex/regiony` per controllare quali cartelle/file sono ancora sotto di esso.

1. Esegui `hdfs dfs -rmr /hbase/data/default/tablex/regiony/filez` per eliminare tutti i file/cartelle figlio

1. Eseguire `hdfs dfs -rmr /hbase/data/default/tablex/regiony` per eliminare la cartella dell'area.

---

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
